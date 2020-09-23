---
title: vue 解决数字自动转为string的问题
date: 2019-04-02 14:00:00
updated: 2019-04-02 14:12:16
tags: 
- deepClone
- JS基础增强
categories: 
- js

---
**.lazy**
在默认情况下，v-model 在每次 input 事件触发后将输入框的值与数据进行同步 (除了上述输入法组合文字时)。你可以添加 lazy 修饰符，从而转变为使用 change 事件进行同步


<!--more-->


```javascript
<input v-model.lazy="msg" >
```
**.number**
v-model.number 存在的坑
```javascript
<input type="number" v-model='order'> //在输入值 在输出的时候会被转换为string类型
```
如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：
```javascript
<input type="number" v-model.number="order">
```
这通常很有用，因为即使在 `type="number"` 时，HTML 输入元素的值也总会返回字符串。

**.trim**
如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

想要输入的内容依然是数字
```javascript
<input type="number" v-model.number='order'> 
```
输出的时候是Number类型，因为在内部帮你把数据做了转化Number(order) 输出，
但此时会埋下一个大坑：
```javascript
Number('152845125481254538') // 152845125481254530
Number('152845125481254532') // 152845125481254530
Number('152845125481254534') //152845125481254530
Number('152845125481254523') // 152845125481254530
```
看到这里就会发现 Number() 转化后数据会发生改变，所以在使用`v-model.number`数据会发生**不可控制的改变**

**限制number的输入长度**

```javascript
<input type="number" v-model='order' ref='order' @input='limitOrder'>
limitOrder () {
	// 要做长度判断
    if (this.$refs.order.value.length > 11) {
      this.$refs.order.value = this.$refs.order.value.slice(0, 18)
      this.order = this.$refs.order.value
    }
 },
```

那如何限制小数点？
思路就是通过添加input事件监听，通过正则判断是否符合条件，不符合的话，要重置为上一次符合的数值。

使用方法类似于
```javascript
 <input type="number"
 <!--允许 0到2位小数-->
 data-dotrange="{0,2}"
 v-model.number.trim="num"
 v-limit-input-number="num"/>

// limit-input-number.js

// 设置组件中的指定属性的值
const setValue = function (exp, value, context) {
  value = isNaN(value) ? '' : value
  new Function ('context', 'value', `context.${exp} = value`)(context, value)
}
// 自定义指令
export default {
  bind (el, {expression}, {context}) {
    // 初始化lastValue
    el.lastValue = el.value

    // 通过dataset 判断是否允许小数点
    let allowDot = !!el.dataset.dotrange
    let dotRange = el.dataset.dotrange || `{0,2}` // 默认
    let pattern = `^[0-9]+${allowDot ? `(.[0-9]${dotRange})?` : ''}$`

    if (!expression) {
      throw new TypeError('请绑定expression')
    }

    el.handleInputEvent = function (e) {
      setTimeout(() => {
        if (e.target.value === '') {
          setValue(expression, '', context)
          // 遇到非法数值，则重置
          e.target.value = ''
        } else if (e.target.value !== '' && !new RegExp(pattern).test(e.target.value)) {
          setValue(expression, parseFloat(e.target.lastValue), context)
          // 遇到非法数值，则重置为lastValue
          e.target.value = e.target.lastValue
          if (allowDot) {
          // 这个时候，根据业务逻辑，可以收起键盘，给出吐司提示
            e.target.blur()
            context.showToast(`小数点后最多${dotRange.replace(/[}{]/g, '').split(',')[1]}位`)
          }
        }
        e.target.lastValue = e.target.value
      }, 0)
    }
    el.addEventListener('input', el.handleInputEvent, false)
  },
  unbind (el) {
    el && el.removeEventListener('input', el.handleInputEvent, false)
  }
}
```

参考：[https://juejin.im/post/5b6b9308f265da0f4e62fecc](https://juejin.im/post/5b6b9308f265da0f4e62fecc)