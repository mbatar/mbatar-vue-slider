## Installing

> npm -i mbatar-vue-slider

## Usage
 
- Just import Components from node_modules folder in main.js

```javascript
import Slider from  'mbatar-vue-slider'
```

- Register mbatar-vue-slider component with any name you want

```javascript
Vue.component("Slider", Slider)
```

- You can use it
```javascript
<Slider  :options="sliderOptions"/>
```

-  **Slider options**
```javascript
sliderOptions:  {
	slides: [
		{
			id:  123123,
			src:  "/src/assets/vue.jpg",
			description:  {
			header:  "What is Vue.js?",
			title:  "Vue (pronounced /vjuː/, like view) is a progressive 	framework for building user interfaces. Unlike other monolithic frameworks, Vue is designed from the ground up to be incrementally adoptable.",
			href:  "https://vuejs.org/"
			}
		},
		.
		.
		.
	],
interval:5000,
auto:true,
mode:'slide',
charLength:30
}

```

-  **slides:[...]**

> Required

-  **description:{...}**

> Optionally

-  **interval**

> Optionally, default = 5000ms

-  **auto**

> Required, ture or false

-  **mode**

> Optionally, default = slide

> Options = fade , slide

-  **charLength**

> Optionally, default = 20

 - **Information**
The component covers the entire area. That's why it is recommended to use it this way.
**Note:** It is assumed that the height and width ratios of the images are not constant
```html
<template>
<div class="slider-container">
	<Slider :options="sliderOptions" />
</div>
</template>
<script>
//JS code...
</script>
<style>
.slider-container{
	width:50rem;
	height:25rem;
}
</style>
```
