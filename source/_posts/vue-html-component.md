---
title: 在纯html页引入组件
---

# 组件

```bash
var registerStepTwo = {
	props: ['data'],
	template: '<div v-show="data.step==2"><h1>自定义组件!</h1></div>',
}
```

# html页的js

```bash
var vm = new Vue({
	el: '#register',
	components: {
		'customStepTwo': registerStepTwo
	}
});
```

# html页面

```bash
<custom-step-two :data="registerForm"></custom-step-two>
```

