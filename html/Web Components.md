# Web Components

## 概念和使用

Web Components 由三项主要技术组成，它们可以被一起使用来创建封装功能的定制元素，可以在你喜欢的任何地方重用，不必担心代码冲突。

* Custom elements（自定义元素）：一组JavaScript API，允许您定义custom elements及其行为，然后可以在您的用户界面中按照需要使用它们
* Shadow DOM（影子 DOM）：一组 JavaScript API，用于将封装的“影子”DOM树附加到元素（与主文档DOM分开呈现）并控制其关联的功能
* HTML templates（HTML 模板）：<template> 和 <slot> 元素使您可以编写不在呈现页面中显示的标记模板

实现web component的基本方法如下：
1. 创建一个类或函数来指定web组件的功能
2. 使用 CustomElementRegistry.define() 方法注册您的新的自定义元素，并向其传递要定义的元素名称、指定元素功能的类、以及可选的其所继承自的元素
3. 如果需要的话，使用Element.attachShadow()方法将一个shadow DOM附加到自定义元素上。使用通常阿德DOM方法向shadow DOM中添加子元素、事件监听等等
4. 如果需要的话，使用<template>和<slot>定义一个HTML模板。再次使用常规DOM方法克隆模板并将其附加到您的shadow DOM中
5. 在页面任何您喜欢的位置使用自定义元素，就像使用常规HTML元素那样

## 使用 templates 和 slots

### 关于模板

<template> 元素及其内容不会在DOM中呈现，但仍可以使用JavaScript去引用它。

### 在 Web Components 中使用模板

```javascript
customElements.define('my-paragraph',
    class extends HTMLElement {
        constructor() {
            super()
            let template = document.getElementById('my-paragraph')
            let templateContent = template.content

            const shadowRoot = this.attachShadow({mode: 'open'})
                .appendChild(templateContent.cloneNode(true))
        }
    }
)
```

```html
<template>
    <style>
        p {
            color: white;
            background-color: #666;
            padding: 5px;
        }
    </style>
    <p>My paragraph</p>
</template>
```

### 使用 slots 添加灵活度

```html
<p>
    <slot name="my-text">My default text</slot>
</p>
```
