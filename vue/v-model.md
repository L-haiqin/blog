v-model的使用限制`input(checkbox,radio,text),select,textarea,components`。

### HTML原生的输入元素

通过使用v-model指令在**元素的输入元素**上创建双向数据绑定，它会根据控件类型自动选取正确的方法来更新元素。

v-model 本质上不过是语法糖，负责监听用户的输入事件以更新数据， 即当input输入框值改变/复选框值改变时，相关绑定的data属性也会同时改变。

```html
<input v-model="text" type="text">
```

两个功能：

1）view层的输入值影响data的属性值

- 通过编译模板文件，为控件的v-model**绑定input事件**，监听页面输入值，来更新data属性值。

2）data属性值发生改变时，更新view层的数值变化

- 在初始化vue实例的时候，model层**通过defineProperty来监听属性（get，set方法）**，一旦属性被重新赋值，则告诉页面更新。

### 自定义组件的v-model

父组件中在子组件上使用v-model，默认会用**value的prop**来接受父组件v-model绑定的值，然后子组件通过**input事件**将更新后的值传递给父组件。

```html
<!-- 父组件中 -->
<MyInput v-model="message"></MyInput>
<!-- v-model只是语法题，真正的实现形式如下，但子组件MyInput里面的props和$emit要有 -->
<MyInput :value="message" @input="message=$event"></MyInput>


<!-- 子组件中 -->
<template>
	<div>
		<input type="text" :value="value" @input="handleInput" />
	</div>
</template>

<script>
export default{
	props:['value'],
	methods:{
		// 将event事件作为参数传进去
		handleInput(e){
			this.$emit('input',e.target.value)
		}
	}
}
</script>
```

为什么需要自定义组件的v-model呢，因为对于checkbox、radio、select以及components的时候，没有默认的value和input事件，在使用时，我们需要显式的去声明定义这些东西，因此**model选项**就出现了，在子组件中指定**prop的值**和**监听的事件event**。

v-model在内部为不同的输入元素使用不同的属性并抛出不同的事件：

- text和textarea元素使用value属性和input事件

- checkbox和radio使用checked属性和change事件（需要使用model选项显示地指定prop和event）

- select使用value和change事件（需要使用model选项显示地指定prop和event）

```html
<!-- 父组件中 -->
<MyCheckbox v-model="checked"></MyCheckbox>
<!-- checkbox监听的事件是change事件 -->
<MyCheckbox :checked="checked" @change="checked=$event"></MyCheckbox>


<!-- 子组件中 -->
<input type="checkbox" :checked="checked" @change="handleChange"/>
	
<script>
export default{
	// 默认情况下，一个组件上的 v-model 会把 value 用作 prop ，把 input 用作 event。
	// 所以当我们在一个自定义组件上使用v-model并不能实现双向绑定，因为自定的组件并没有默认的value和input事件，
	// 在使用时，我们需要显式的去声明定义这些东西。
	// 这是就使用model选项，在定义组件的时候，指定prop的值和监听的事件。
	model:{
		prop:'checked',
		// 命名事件，需要对应下面$emit即可，也需要对应父组件中的监听事件
		event:'change'
	},
	props:['checked'],
	methods:{
		handleChange(e){
			this.$emit('change',e.target.checked)
		}
	}
}
</script>
```



参考博客：[掘金讲解](https://juejin.cn/post/6844903705733513223)