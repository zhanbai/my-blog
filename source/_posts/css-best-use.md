---
title: CSS 编写：从语义化到复用化
date: 2019-12-27 10:33:43
category: 技术
tags: CSS
---

最近看了一篇关于 CSS 编写的文章，觉得思路有一定启发性，所以通过一定的整理消化，促成本篇文章。

### 语义化 CSS

从我们开始学习编写 CSS，关注点分离就是我们了解到的最佳实践，即内容和样式的分离。

下面看一个例子：

```
<div class="text-center">
    hello world!
</div>
```

其中定义了类 `text-center` 表明文本居中的设计决定，这违反了关注点分离的思路，推荐的做法应该是根据文本内容来命名类名，比如 `greeting`。CSS 样式如下：

```
<style>
.greeting {
    text-align: center;
}
</style>

<div class="greeting">
    hello world!
</div>
```

使用关注点分离的 Web 应用，如果想要改变整体样式风格，只需对 CSS 部分做重新设计即可。

看下面一个例子：

```
<style>
.author-bio {
    background-color: white;
    > h2 {
        font-size: 18px;
        color: #323233;
    }
    > p {
        font-size: 16px;
        color: #646566;
    }
}
</style>

<div class="author-bio">
    <h2>李小龙</h2>
    <p>原名李振藩，师承叶问，1940 年出生于美国加州旧金山，祖籍中国广东省佛山市顺德区均安镇，世界武道变革先驱者、武术技击家、武术哲学家、MMA 之父、武术宗师、功夫片的开创者和截拳道创始人、华人武打电影演员，中国功夫首位全球推广者、好莱坞首位华人主角。</p>
</div>
```

上述例子已经实现了内容与样式的分离，但是 CSS 本身是嵌套的，似乎显得没有那么分离。从 CSS 结构中也可以看出 HTML 的大致结构。

### 解决 CSS 样式的耦合问题

解决 CSS 样式嵌套的耦合，比较出名的是 BEM 方法，即块元素修饰器。使用这种方面，上面的例子可以改写为：

```
<style>
.author-bio {
    background-color: white;
}
author-bio__name {
    font-size: 18px;
    color: #323233;
}
author-bio__body {
    font-size: 16px;
    color: #646566;
}
</style>

<div class="author-bio">
    <h2 class="author-bio__name">李小龙</h2>
    <p class="author-bio__body">原名李振藩，师承叶问，1940 年出生于美国加州旧金山，祖籍中国广东省佛山市顺德区均安镇，世界武道变革先驱者、武术技击家、武术哲学家、MMA 之父、武术宗师、功夫片的开创者和截拳道创始人、华人武打电影演员，中国功夫首位全球推广者、好莱坞首位华人主角。</p>
</div>
```

上述方案解决了 CSS 样式的耦合问题，CSS 类名也是语义化的，跟 HTML 标记也没有了关联。

假如我要处理一个类似的样式，比如显示文章的预览，同样是标题 + 内容的形式。如果我们将 `author-bio` 应用到文章预览，似乎违反了语义化的规则，那么如果我们定义 `article-preview` 类名，我们就要把具体的样式都复制或者重写一遍。当然，我们也可以使用 @extend 来继承 `author-bio` 的样式。

除了上述两种方式，是否有第三种方式？我们可以创建与内容无关的样式，虽然从语义的角度讲，作者简介和文章预览没有必然联系，但是从设计角度讲，他们有着共同的样式，可以定义 `media-card` 类。

如果我们想要改变作者简介的样式，而不改变文章预览的样式，我们需要定义新的类。从以上我们可以看出，如果从是否符合关注点分离的规则来看待 HTML 和 CSS 的关系，那就是非黑即白的问题。这似乎不是非常正确的思路，相反，我们更应该考虑的是依赖问题。

如果是 CSS 依赖于 HTML（关注点分离），即 HTML 是独立的、可复用的，那么 CSS 就是不可复用的；如果是 HTML 依赖于 CSS（混合问题），即 CSS 是独立的、可复用的，那么 HTML 就是不可复用的。在行业中，CSS Zen Garden 使用的是第一种方法，而常见的 UI 框架，如 Bootstrap 等使用的是第二种方法。

这两种方法没有谁对谁错，我们从可复用角度来思考 CSS 的优化问题。

### CSS 的复用化

我们的目的是尽可能创建不依赖 HTML 内容的可复用的类名定义。这样的类名可能有：`card` `btn` `btn-primary` `img-round` `badge` 等。

看下面一个例子：

```
<form class="stacked-form" action="#">
    <div class="stacked-form__section">
        <button class="btn btn--secondary">Cancel</button>
        <button class="btn btn--primary">Submit</button>
    </div>
</form>
```

如果我们想要这个表单中两个按钮之间隔开一定距离，可以创建一个新的子组件 `actions-list`，该组件易于复用。

```
<style>
.actions-list {
    text-align: right;
}
.actions-list__item {
    margin-right: 1rem;
    &:last-child {
        margin-right: 0;
    }
}
</style>

<form class="stacked-form" action="#">
    <div class="stacked-form__section actions-list">
        <button class="actions-list__item btn btn--secondary">Cancel</button>
        <button class="actions-list__item btn btn--primary">Submit</button>
    </div>
</form>
```

沿着复用这条思路走下去，久而久之基本可以构建一套全新的 UI 组件库，而无需编写过多新的 CSS。


