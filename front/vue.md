# front Vue

```
<!doctype html>
<html>
<head>
<meta charset="utf-8"/>
<title>vue demo</title>
<script src="vue-2.4.2.js"></script>
</head>
<body>
<script type="text/javascript"></script>
<div id="app" v-if="seen">

<!-- 数据绑定 -->
<span v-bind:title="message">
鼠标悬停几秒钟查看此处动态绑定的提示信息！
</span>

<!-- 数据循环 index从0开始 -->
<ul>
	<li v-for="(todo,index) in todos" v-if="index > 0">
		{{ todo.time }} --{{ index }}-- {{ todo.text }}
	</li>
</ul>

<!-- on事件绑定 -->
<button v-on:click="cshow">按钮cshow</button>
<p>这个按钮被点击了 {{ counter }} 次。</p>

<!-- 用户按键监听 -->
<input type="text" v-on:keyup.space="dshow" />
<button v-on:click="warn('Form cannot be submitted yet.', $event)">Submit</button>

<!-- 单选多选 -->
<br/>
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>Picked: {{ picked }}</span>

<br/>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>
<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>
<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
<br>
<span>Checked names: {{ checkedNames }}</span>

</div>
<div id="app2">
	<span>显示影藏测试</span>
</div>
<hr/>
<div id="example">
	<my-component></my-component>
</div>
<script type="text/javascript">
	var app = new Vue({
		el: '#app',
		data: {
			seen: true, //使用seen时，必须有个v-if="seen"
			message: '页面加载于 ' + new Date(),
			todos: [
				{ text: '学习 JavaScript',time: '2017-09' },
				{ text: '学习 Vue',time: '2017-10' },
				{ text: '整个牛项目',time: '2017-11' }
			],
			name:'this name',
			name2:'222222',
			counter:0,
			picked:'',
			checkedNames:[]
		},
		methods:{
			cshow:function(){
				this.counter += 1;
				// alert('chufa'+this.name2);
			},
			dshow:function(){
				alert('you click space key!');
			},
			warn: function (message, event) {
				//目前不是很明白！！！！！！！！！！！！！！！
				// 现在我们可以访问原生事件对象
				if (event) event.preventDefault()
				alert(message)
			}
		}

	})
	var app2 = new Vue({
		el: '#app2',
		data: {
			seen: false
		}
	})
	var Child = {
		template:'<div>A custom component!</div>'
	}
	new Vue({
		el: '#example',
		components: {
			'my-component':Child
		}
	})
</script>
</body>
</html>
```