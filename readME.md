# Vue 2 和 Vue 3 的区别
90% 的写法完全一致，除了以下几点
Vue 3 的 Template 支持多个根标签，Vue 2 不支持
Vue 3 有 createApp()，而 Vue 2 的是 new Vue()
createApp(组件)，new Vue({template, render})
新 v-model 代替以前的 v-model 和 .sync
新增 context.emit，与 this.$emit 作用相同
this.$emit只能在methods里面用,不能再setup函数中是使用
vue3使用 ref 创建内部数据



# Vue 3 的 v-model
## 前提
value="true" 和 :value="true" 的区别
value="true"  拿到字符串
:value="true" 拿到bool
## 要求
属性名任意，假设为 x
事件名必须为 "update:x"
## 效果
```
<Switch :value="y" @update:value="y = $event"/>
```
可以简写为
```
<Switch v-model:value="y" />
```
$event 的值是 emit 的第二个参数
emit(事件名, 事件参数)
context.emit("update:value", !props.value);
## 文档
这是 Vue 2 到 Vue 3 的一个大变动（breaking change)
[文档](https://v3.vuejs.org/guide/migration/v-model.html#_2-x-syntax)里面有详细的介绍

# Vue 3 属性绑定/继承
默认所有属性都绑定到根元素
```
<templete>
  <button>
    <slot />
  </button>
</templete>
```
使用 inheritAttrs: false 可以取消默认绑定
使用 $attrs 或者 context.attrs 获取所有属性
使用 v-bind="$attrs" 批量绑定属性
```
<templete>
  <div>
    <button v-bind="$attrs">//默认传给div的属性，现在转移到button上面
      <slot />
    </button>
  </div>
  </templete>
  export default {
    inheritAttrs: false //取消默认绑定在div上的属性
  }
```
使用 const {size, level, ...xxx} = context.attrs 将属性分开
```
<templete>
  <div :size="size"> //size属性绑到div
    <button v-bind="rest"> //其余属性全部给到button
      <slot />
    </button>
  </div>
</templete>
```
# props V.S. attrs

props 要先声明才能取值，attrs 不用先声明，可以自动收集传递给组件的所有属性
props 没有声明的属性，会跑到 attrs 里
props 不包含事件（没法办法声明），attrs 包含
props 利用声明可以支持 string 以外的类型，attrs 只有 string 类型

# UI 库的 CSS 注意事项

## 不能使用 scoped
因为 data-v-xxx 中的 xxx 每次运行可能不同
必须输出稳定不变的 class 选择器，方便使用者覆盖
## 必须加前缀
.button 不行，很容易被使用者覆盖
.gulu-button 可以，不太容易被覆盖
.theme-link 不行，很容易被使用者覆盖
.gulu-theme-link 可以，不太容易被覆盖

# 如何在运行时确认子组件的类型
检查 context.slots.default() 数组每一项的type

# onMounted / onUpdated / watchEffect

watchEffect 会在onMounted之前执行，
所以如果想利用watchEffect兼顾onMounted / onUpdated两个生命周期的作用，
需要将watchEffect包裹在onMounted里面


