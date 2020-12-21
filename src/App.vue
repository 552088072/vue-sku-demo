<template>
  <div id="app">
    <div v-for="(item,index) in specList" :key="index">
      <div class='title'>{{item.title}}</div>
      <div class='spec'>
        <div class='spec-item' v-for="(its,ins) in item.list" :key="its.name + ins">
          <span @click="changeSpec(item.title, its.name, its.able)" :class="[selectSpec[item.title] === its.name ? 'active' : '' , its.able? '' : 'disabled']">{{its.name}}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// 引入模拟数据
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
    // 处理数据
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
            // 判断是否可以选择
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
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
  margin-left: 60px;
}
.title{
  line-height: 40px;
}
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
