Bem 是块（block）、元素（element）、修饰符（modifier）的简写

```
-   中划线 ：仅作为连字符使用，表示某个块或者某个子元素的多单词之间的连接记号。
__  双下划线：双下划线用来连接块和块的子元素
_   单下划线：单下划线用来描述一个块或者块的子元素的一种状态

type-block__element_modifier
```

BEM命名约定的模式：

```css
.block {}
.block__element {}
.block-modifier {}
```

`block` 代表了更高级别的抽象或组件

`block_element` 代表了.bllock的后代、用于形成一个完整的.block的整体

`block--modifier` 代表了.block的不同状态或不同不版本。使用两个连字符和下划线而不是一个，是为了让你自己的块可以用单个连字符来界定。

