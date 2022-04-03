# 由《不优雅的 React Hooks》引发的思考

近日在知乎上看到一篇对 `Hooks` 的批评（[不优雅的 React Hooks](https://zhuanlan.zhihu.com/p/455317250)）。作者从抽象和实现出发讨论了在使用 hooks 的途中遇到的问题，引申出对于 hooks 本身设计上的一些思考。对于文章中的一些问题，我可能持有不同的态度，但是在阅读文章的过程中我也发现自己对 hooks 知之甚少，有些地方还抱着很浅显的、不明确的理解。因而尝试记录些对文章中观点，以及对 React hooks 的思考。我是一个不善于思考的人，可能思路和行文常有不正确的地方，如果有不同的意见，当然欢迎进一步的探讨 :)

## “奇怪的”规矩

不得不说，在刚刚开始接触到 hooks 时，它奇怪的约定确实让我有些摸不到头脑。而文章主要从命名和调用时序两个方面，讨论了 hooks 的奇怪之处。

### 命名

命名是程序员第一棘手的问题, React 团队也不例外。如果我没记错，早些年的 React Rally 上, **Paul O Shannessy** 在讲实现一个简单的 React 原型时，就有吐槽过 React 团队对生命周期函数的命名。

`useXXX` 这样的前缀确实第一眼看上去很不直观。作者也提到 `useGetState` 或者是 `useTitle` 会很让人困惑，让人纠结是怎么个 `use` 法。

我对 Rust 不是很熟悉，但是我觉得和 Rust 里面的关键字 `use` 联系起来可能会比较容易理解。虽然它更像 C# 中的 `using`，起到建立别名绑定的作用，但是它与 ES 中的 `import` 意义还是比较接近的。

下面是 Rust 官方文档中的例子，可以看到，主要还是起到别名绑定的作用：

```rs
use std::option::Option::{Some, None};
use std::collections::hash_map::{self, HashMap};

fn foo<T>(_: T){}
fn bar(map1: HashMap<String, usize>, map2: hash_map::HashMap<String, usize>){}

fn main() {
    // Equivalent to 'foo(vec![std::option::Option::Some(1.0f64),
    // std::option::Option::None]);'
    foo(vec![Some(1.0f64), None]);

    // Both `hash_map` and `HashMap` are in scope.
    let map1 = HashMap::new();
    let map2 = hash_map::HashMap::new();
    bar(map1, map2);
}
```

联系到 Dan 在他[博客](https://overreacted.io/algebraic-effects-for-the-rest-of-us/)中提到的`代数效应`，可以大致联想到使用 `use` 的缘由。因此，如果将 `use` 翻译成**引入一个效应**的话，也许会更利于我们的理解和使用。

### 调用时序

作者在这一部分提到, 对于怎样记录 hooks 存储的数据, React 底层使用链表进行存储，因此 hooks API 是依赖于调用时序的。

作者举例了他想要的 API:

> 最理想的 API 封装应当是给开发者认知负担最小的。好比封装一个纯函数add()，不论开发者是在什么环境调用、在多么深的层级调用、用什么样的调用时序，只要传入的参数符合要求，它就可以正常运作，简单而纯粹。

```ts
function add(a: number, b: number) {
  return a + b
}

function outer() {
  const m = 123;
  setTimeout(() => {
    request('xx').then((n) => {
      const result = add(m, n)         // 符合直觉的调用：无环境要求
    })
  }, 1e3)
}
```

但是命令式语言往往是时序相关的，如评论区的回复：

> 编程依赖时序是再正常不过的事情了，先声明再赋值这不就是时序吗？

我对于 Hooks 的理解应该也是出于知乎的一位答主，现在已经找不到相关的回答，但是大致的思想我还记得：

React 的 hooks 可以理解为函数式组件额外的参数，在 React 调用函数式组件时，会像填写参数一样，依次将 hooks 对应的值填进去。

我个人觉得，如果用这种思路去看待 hooks, 可能会更容易理解其设计的初心。

// TODO: 关于 redux-saga 和 generator 的部分我相对不很熟悉，还需要进一步的研究。回头再接着写 _(:з)∠)_


