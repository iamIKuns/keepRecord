## 模板解析
```html
<div id='app'>
  <h1>{{str}}</h1>
  {{str}}
</div>
```
```javascript
NEW Vue({
  el:'#app',
  data:{
    str:'123'
  }
})
```
```javascript
class Vue{
  //options 为 new Vue({})实例化传入对象
  //{
  //el:'#app',
  //	data:{
  // 		str:'123'
  //	}
	//}
  constructor( options ){
    //获取实例化Vue传入的根节点el:'#app', 此时this.$el为#app
    this.$el = document.querySelector(options.el)
  	//赋值data
    this.$data = options.data
    //传入#app
    this.compile(this.$el);
  }

  compile( node ){
    //获取根元素下的所有子节点
    node.childNodes.forEach((item,index)=>{
      //1为元素节点
      if(item.nodeType == 1){
         //递归 传入自己 替换自己内的文本节点
          this.compile(item);
      }
      //文本节点的类型是3 渲染时 只会替换渲染{{}}内变量 故此只用只用找到类型3的节点
      if(item.nodeType == 3){
        // 正则匹配{{ }} 
        let reg = /\{\{(.*?)\}\}/g
        // 保存当前文本节点内容
        let text = item.textContent
        //完成给节点赋值 replace 第二项接受一个函数  vmKey为正则匹配过后的值
        // {{ str }} ==> str
        item.textContent = text.replace(reg,(mactch,vmKey)=>{
          //.trim()去空格 为str 这一步正式拿到data对应的key
          vmKey = vmKey.trim()
          //在data中取到key对应的值 return
          return this.$data[vmKey]
        })
      }
    })
  }
}
```
> 主要思想就是拿到页面上所有 **文本节点** 循环遍历  
> 找到 **文本节点**  如果是 元素节点  通过递归传入自己作为父节点   继续寻找自己内部的 **文本节点**  以此拿到所有 **文本节点 **  
> 再通过正则匹配 **文本节点** 内的 {{ }} 拿到 {{ }} 内的变量名  通过变量名再匹配data内的变量    
> 从而把data里的变量    替换渲染到 **文本节点 **

## 事件绑定
```html
<button @click='btn'>事件绑定</button>
```
```javascript
NEW Vue({
  el:'#app',
  data:{
    str:'123'
  },
  methods:{
    btn(e){
      alert(this.str,e)
    }
  }
})
```
```javascript
class Vue{
   constructor( options ){
    //赋值this.$options方便后续取methods绑定
    this.$options = options
    //获取实例化Vue传入的根节点el:'#app', 此时this.$el为#app
    this.$el = document.querySelector(options.el)
  	//赋值data
    this.$data = options.data
    //传入#app
    this.compile(this.$el);
  }
	compile( node ){
    //获取根元素下的所有子节点
    node.childNodes.forEach((item,index)=>{
      //1为元素节点
      if(item.nodeType == 1){
        // 判断元素节点是否有属性@click
        if(item.hasAttribute('@click')){
          // 取出当前元素绑定的事件名字 并且去除空格
          let vmKey = item.getAttribute('@click').trim()
          // 给当前元素绑定事件
          item.addEventListener('click',(event)={
            // 获取到methods内事件并且用bind绑定this
            let eventFn = this.$options.methods[vmKey].bind(this)
            // 触发methods内事件 传入事件对象
            eventFn(event)
          })
        }
        if( item.childNodes.length > 0){
            //递归 传入自己 替换自己内的文本节点
        		this.compile(item);
        }
      }
  }
}
```
> 主要通过元素的attr属性 确定事件类型 和要触发methods内函数的名字 
> 通过addeventlistenter增加事件监听 
> bind绑定this指向 触发函数时传入event

## 响应式数据

