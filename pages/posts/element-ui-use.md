---
title: Element UI 使用碰到的一些问题
date: 2024-04-09T10:00:00.000+00:00
lang: zh
duration: 10 min
---

## `el-input` 按回车后自动刷新页面

如题，后翻看[文档](https://element.eleme.cn/#/zh-CN/component/form)原来是规范要求：

> W3C 标准中有如下[规定](https://www.w3.org/MarkUp/html-spec/html-spec_8.html#SEC8.2)
>> When there is only one single-line text input field in a form，the user agent should accept Enter in that field as a request to submit the form。
> 
> 即：当一个 form 元素中只有一个输入框时，在该输入框中按下回车应提交该表单。如果希望阻止这一默认行为，可以在 `<el-form>` 标签上添加 `@submit.native.prevent`。

## `el-dialog` 中的 `el-form` 重置无效


```html
<el-dialog v-model:visible="show">
  <el-form v-model="form"/>
</el-dialog>
```

`el-form` 会以首次挂载后的状态作为初始默认值，因此我们在使用弹窗回显表单数据时，应该先显示出弹窗，然后在下一次事件循环再去做数据的回显：

```js
function handleEdit(data) {
  this.show = true
  this.$nextTick(() => {
    this.form = data
  })
}
```