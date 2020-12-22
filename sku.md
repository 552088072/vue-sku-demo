### 一、 效果图
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5e4b4e1a30ea4bca9f2bb70210450a60~tplv-k3u1fbpfcp-watermark.image)

### 二、 前言

公司项目做商品模块 本人实现了商品sku生成部分 在sku生成后可以选择禁用其中部分sku ( 实际场景部分商品没有sku中的一些规格的时候需要禁用部分sku ) 所以在前台选择sku的时候会出现部分sku无法选中的情况

最开始也抱着百度的方案写这一块功能 但是目前百度的大部分都是 [矩形阵图算法](https://juejin.cn/post/6844904013801095176?utm_source=gold_browser_extension%3Futm_source%3Dgold_browser_extension#heading-6) 但是亲测都有bug   所以最后只能自己写了 无奈自己愚笨 写不出来 最后求助公司其他大佬 和在大佬的帮助下完成此功能 

### 三、 总体实现思路
根据选中的规格和当前需要被验证的规格组成一个对象  然后在循环skuList判断 只要有一条sku中的规格满足这个对象中的每一项即可
具体实现过程看下面代码实现过程 

### 四、 实现代码

1、 先看数据    （这里是全数据 虽然有点长但是方便调试 测试）
``` js  

    const specList = [
    	{ title: "颜色", list: ["红色", "紫色", "白色", "黑色"] },
        { title: "套餐", list: ["套餐一", "套餐二", "套餐三", "套餐四"] },
        { title: "内存", list: ["64G", "128G", "256G"] }
    ]
    
    const skuList = [
        // { id: 1608188117177, specs: ["红色", "套餐一", "64G"] },
        { id: 1608188117178, specs: ["红色", "套餐一", "128G"] },
        { id: 1608188117179, specs: ["红色", "套餐一", "256G"] },
        { id: 1608188117180, specs: ["红色", "套餐二", "64G"] },
        { id: 1608188117181, specs: ["红色", "套餐二", "128G"] },
        { id: 1608188117182, specs: ["红色", "套餐二", "256G"] },
        { id: 1608188117183, specs: ["红色", "套餐三", "64G"] },
        { id: 1608188117184, specs: ["红色", "套餐三", "128G"] },
        { id: 1608188117185, specs: ["红色", "套餐三", "256G"] },
        // { id: 1608188117186, specs: ["红色", "套餐四", "64G"] },
        // { id: 1608188117187, specs: ["红色", "套餐四", "128G"] },
        // { id: 1608188117188, specs: ["红色", "套餐四", "256G"] },
        // { id: 1608188117189, specs: ["紫色", "套餐一", "64G"] },
        // { id: 1608188117190, specs: ["紫色", "套餐一", "128G"] },
        // { id: 1608188117191, specs: ["紫色", "套餐一", "256G"] },
        { id: 1608188117192, specs: ["紫色", "套餐二", "64G"] },
        { id: 1608188117193, specs: ["紫色", "套餐二", "128G"] },
        { id: 1608188117194, specs: ["紫色", "套餐二", "256G"] },
        { id: 1608188117195, specs: ["紫色", "套餐三", "64G"] },
        { id: 1608188117196, specs: ["紫色", "套餐三", "128G"] },
        { id: 1608188117197, specs: ["紫色", "套餐三", "256G"] },
        { id: 1608188117198, specs: ["紫色", "套餐四", "64G"] },
        { id: 1608188117199, specs: ["紫色", "套餐四", "128G"] },
        { id: 1608188117200, specs: ["紫色", "套餐四", "256G"] },
        { id: 1608188117201, specs: ["白色", "套餐一", "64G"] },
        { id: 1608188117202, specs: ["白色", "套餐一", "128G"] },
        { id: 1608188117203, specs: ["白色", "套餐一", "256G"] },
        { id: 1608188117204, specs: ["白色", "套餐二", "64G"] },
        // { id: 1608188117205, specs: ["白色", "套餐二", "128G"] },
        // { id: 1608188117206, specs: ["白色", "套餐二", "256G"] },
        // { id: 1608188117207, specs: ["白色", "套餐三", "64G"] },
        // { id: 1608188117208, specs: ["白色", "套餐三", "128G"] },
        // { id: 1608188117209, specs: ["白色", "套餐三", "256G"] },
        // { id: 1608188117210, specs: ["白色", "套餐四", "64G"] },
        { id: 1608188117211, specs: ["白色", "套餐四", "128G"] },
        { id: 1608188117212, specs: ["白色", "套餐四", "256G"] },
        { id: 1608188117213, specs: ["黑色", "套餐一", "64G"] },
        { id: 1608188117214, specs: ["黑色", "套餐一", "128G"] },
        { id: 1608188117215, specs: ["黑色", "套餐一", "256G"] },
        { id: 1608188117216, specs: ["黑色", "套餐二", "64G"] },
        // { id: 1608188117217, specs: ["黑色", "套餐二", "128G"] },
        // { id: 1608188117218, specs: ["黑色", "套餐二", "256G"] },
        // { id: 1608188117219, specs: ["黑色", "套餐三", "64G"] },
        // { id: 1608188117220, specs: ["黑色", "套餐三", "128G"] },
        // { id: 1608188117221, specs: ["黑色", "套餐三", "256G"] },
        // { id: 1608188117222, specs: ["黑色", "套餐四", "64G"] },
        { id: 1608188117223, specs: ["黑色", "套餐四", "128G"] },
        { id: 1608188117224, specs: ["黑色", "套餐四", "256G"] }
    ]

```

2、 页面代码 
```  html

<div id="app">
    <h3>前端SKU实现</h3>
    <div v-for="(item,index) in specList" :key="index">
      <div class='title'>{{item.title}}</div>
      <div class='spec'>
        <div class='spec-item' v-for="(its,ins) in item.list" :key="its.name + ins">
          <span @click="changeSpec(item.title, its.name, its.able)" :class="[selectSpec[item.title] === its.name ? 'active' : '' , its.able? '' : 'disabled']">{{its.name}}</span>
        </div>
      </div>
    </div>
  </div>
  
 <style>
.spec-item{
  display: inline-block;
  margin-right: 10px;
}
.spec-item span {
  border:1px solid #eee;
  cursor: pointer;
  padding:5px 10px;
}
.spec-item .active{
  border: 1px solid red;
  background-color: red;
  color:#fff;
}
.spec-item .disabled{
  color: #c0c4cc;
  cursor: not-allowed;
  background-image: none;
  background-color: #fff;
  border-color: #ebeef5;
}
</style>
```

3、 核心JS逻辑
```js 

// 第一步引入数据    这里引入的数据就是上面的 specList  skuList
import {specList, skuList} from './data'

export default {
  name: 'App',
  data() {
    return {
      specList:[],
      skuList:[],
      selectSpec:{},  // 选择数据的对象 将已选的数据放在这个对象里面记录下来  用对象的好处在下面深拷贝处就能体验到了 
    }
  },
 
  created() {
    //  第二步  处理数据
    this.skuList = skuList;
    // 初始化选择数据的对象 
    specList.forEach(item => {
      this.$set(this.selectSpec, item.title, "");
    })
    // 将规格数据处理成我们视图所需要的数据类型 
    this.specList = specList.map(item => {
      return {
        title:item.title,
        list: item.list.map(its => {
          return {
            name:its,
            //  判断是否可以选择
            //  这里相当于init 初始化数据  this.isAble() 核心判断逻辑  
            able: this.isAble(item.title, its)  // 注释的调试看逻辑代码 false 
          }
        })
      }
    })
    // 注释的调试看逻辑代码
    // this.selectSpec = {
    //   '颜色':'',
    //   '套餐':'套餐一',
    //   '内存':'64G'
    // }
    // this.isAble('颜色', '红色')
  },
  methods:{
    // 核心判断逻辑 
    // 判断规格是否可以被选择  核心函数 key当前的规格的title   value规格值
    isAble(key, value){
      // 深拷贝 避免被影响  
      var copySelectSpec = JSON.parse(JSON.stringify(this.selectSpec));
      // 用对象的好处就在这了 直接赋值当前验证项
      copySelectSpec[key] = value;
      // 用数组的 some 方法 效率高 符合条件直接退出循环
      let flag = this.skuList.some(item => {
        // 条件判断 核心逻辑判断
        // console.log(item)
        var i = 0 ; 
        // 这个for in 循环的逻辑就对底子不深的人来说就看不懂了 原理就是循环已经选中的 和 正在当前对比的数据 和 所有的sku对比 只有当前验证的所有项满足sku中的规格或者其他规格为空时 即满足条件 稍微有点复杂 把注释的调试代码打开就调试下就可以看懂了
        for(let k in copySelectSpec) {
          //  console.log(copySelectSpec[k])  // 注释的调试看逻辑代码
          if(copySelectSpec[k] != "" && item.specs.includes(copySelectSpec[k])){
            // console.log(item)
            i++
          }else if (copySelectSpec[k] == "") {
            i++;
          }
        }
        // 符合下面条件就退出了 不符合会一直循环知道循环结束没有符合的条件就 return false 了 
        // console.log(i) // 注释的调试看逻辑代码
        return i == specList.length
      })
      console.log(flag)
      return flag
    },
    // 点击事件
    changeSpec(key,value,able){
      if(!able) return
      if(this.selectSpec[key] === value){
        this.selectSpec[key] = ''
      }else {
        this.selectSpec[key] = value
      }
      // forEach循环改变原数组 
      this.specList.forEach(item => {
        item.list.forEach(its => {
          its.able = this.isAble(item.title, its.name);
          console.log(its.name, its.able);
        });
      });
    }
  },
}
```

### 五、 总结
`created`处理数据的时候将 `specList` 的数据处理成我们逻辑和视图结合需要的数据 ( 不要在意数据一不一样 我们后台给的数据也一样 需要前端自己二次处理 ) 此处我用的 `map` 方法处理 用其他循环方法处理也可以只要能得到我们最终的数据就行了 
``` js  

// 处理后的数据
this.specList = [
    {
        "title":"颜色",
        "list":[
            {
                "name":"红色",
                "able":true
            },
            {
                "name":"紫色",
                "able":true
            },
            {
                "name":"白色",
                "able":true
            },
            {
                "name":"黑色",
                "able":true
            }
        ]
    },
    .....
] 

```

当在`created`处理`able`数据时 当前没用选中数据 当前的`selectSpec` 初始化完成 应该是这个样子的 
``` js
this.selectSpec = {"颜色":"","套餐":"","内存":""}
```
循环处理`this.specList`的时候 在处理每个规格是否可以被选中的时候 假如循环到 **红色**  时 此时的 `this.selectSpec = {"颜色":"红色","套餐":"","内存":""}`  在看下面的这段代码 

``` js

   isAble(key, value){  //此时 key = 颜色    value = 红色
      var copySelectSpec = JSON.parse(JSON.stringify(this.selectSpec));
      copySelectSpec[key] = value;  // 此时 copySelectSpec = {"颜色":"红色","套餐":"","内存":""}
      let flag = this.skuList.some(item => {
      	// 按照上面的skuList数据 此时第一次循环 item = { id: 1608188117178, specs: ["红色", "套餐一", "128G"] }
        var i = 0 ; 
        for(let k in copySelectSpec) {  //  for in  懂吧?
          // copySelectSpec[k]  依次是 '红色' \ '' \ ''
          if(copySelectSpec[k] != "" && item.specs.includes(copySelectSpec[k])){  // item中有红色 i++    
            i++
          }else if (copySelectSpec[k] == "") {   // ''后面规格是空字符串 不管 也执行 i++
            i++;
          }
        }
        // 此时 红色满足条件 return true  此时红色规格校验完毕 并会退出当选skuList循环
        return i == specList.length
      })
      return flag
    },
```
上述代码模拟程序 在初始化红色规格时侯的逻辑 其余规格也相同  

由上述代码运行逻辑可见  当选择规格后 就是这样的   
假如选择了 **红色** 此时 `this.selectSpec = {"颜色":"红色","套餐":"","内存":""}` 
验证颜色时 会被这段代码覆盖 `copySelectSpec[key] = value`  
验证其他规格时 假如分别验证 **套餐一**  或者 **套餐四**
``` js

	// 验证套餐一     
        let flag = this.skuList.some(item => {
      	// 按照上面的skuList数据 此时第一次循环 item = { id: 1608188117178, specs: ["红色", "套餐一", "128G"] }
        var i = 0 ; 
        for(let k in copySelectSpec) { 
          // copySelectSpec[k]  依次是 '红色' \ '套餐一' \ ''
          if(copySelectSpec[k] != "" && item.specs.includes(copySelectSpec[k])){  // item中有红色 i++    item中有套餐一 i++  
            i++
          }else if (copySelectSpec[k] == "") {   // ''后面规格是空字符串 不管 也执行 i++
            i++;
          }
        }
        // 此时套餐一满足条件 return true  此时套餐一规格校验完毕 并会退出当选skuList循环
        return i == specList.length
        
        
        
        // 验证套餐四
        let flag = this.skuList.some(item => {
      	// 按照上面的skuList数据 此时第一次循环 item = { id: 1608188117178, specs: ["红色", "套餐一", "128G"] }
	// 直至循环到最后 { id: 1608188117224, specs: ["黑色", "套餐四", "256G"] }  都没有出现一条sku 同时出现套餐四和红色规格的sku  
        // 所以i 最多等于2  specList.length等于3  return false
        var i = 0 ; 
        for(let k in copySelectSpec) {  
          // copySelectSpec[k]  依次是 '红色' \ '套餐四' \ ''
          if(copySelectSpec[k] != "" && item.specs.includes(copySelectSpec[k])){ 
            // item中有红色的就没用套餐四 有套餐四的就没用红色规格 所以 i=1
            i++
          }else if (copySelectSpec[k] == "") {   // '' 后面规格是空字符串 不管 也执行 i++
            i++;
          }
        }
        // 最终i = 2 return false
        return i == specList.length

```
由此可见 不管怎么选择都可以验证所有的规格是否可以被选 

下面附上demo地址  [gitHub:前端商品sku-demo](https://github.com/552088072/vue-sku-demo)

 **记得给我小星星  😊**