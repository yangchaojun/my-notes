# 什么是DOM Reflow

Reflow 计算页面的外观。元素上的回流会重新计算元素的维度和位置，并且还会触发对该元素的子元素、祖先元素和DOM中出现在该元素之后的元素的进一步回流。然后调用最终重绘。回流是非常昂贵的，但不幸的是它很容易被触发。

Reflow occurs when you:

- 在DOM中插入、删除或更新元素（insert, remove or update an element in the DOM）

- 修改页面中的内容，比如在Input框中输入内容（modify content on the page, e.g. the text in an input box）

- 移动DOM元素（move a DOM element）

- DOM动画（animate a DOM element）

- take measurements of an element such as offsetHeight or getComputedStyle

  对一个元素进行度量，例如offseight或getComputedStyle（take measurements of an element such as offsetHeight or getComputedStyle）

- 改变CSS样式（change a CSS style）

- 改变元素的样式类名（change the className of an element）

- 增加或删除新的层叠样式表（add or remove a stylesheet）

- 缩放窗口大小（resize the window）

- 滚动窗口（scroll）