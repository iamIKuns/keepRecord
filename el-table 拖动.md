```javascript
import Sortable from "sortablejs";
```
```javascript
// 使用插件必须指定row-key
<el-table  row-key="uniqueId">
```
```javascript
const cmList = document.querySelector(".cmList .el-table__body-wrapper tbody");
Sortable.create(el, 
  {
    group: { 
      //组名
    	name: "words",
      //true或false或 'clone'或array或function, 是否允许拖从当前组
    	pull: true,
      //true或false或array或function 是否允许拖入当前组
    	put: true 
  	},
  	animation: 0, //动画参数
  	onStart: function(evt) {
    	//开始拖拽出发该函数
  	},
  	onEnd({ newIndex, oldIndex }) {
    	//拖拽结束后表单数据更新
    	const currRow = _t.tableData.splice(oldIndex, 1)[0];
    	_t.tableData.splice(newIndex, 0, currRow);
    	_t.up();
  }
});
```
简单 行 拖拽例子  具体api 参考
[sortable.js对table表格进行拖动排序 - itxst.com](https://www.itxst.com/sortablejs/f7nqyvbn.html)

