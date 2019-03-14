<template>
  <div id="app">

    <div class="todo-title">This is your own todo-list</div>

    <input type="text" v-model="doWhat" v-on:keydown="add($event)" autocomplete="off" placeholder="输入计划，按回车添加" class="textInput">

    <div class="list-area">
      <div class="event-status">未完成：</div>
      <ul>
        <li v-for="(item,key) in todoList " v-if="!item.isOver" class="list-style">
          <input type="checkbox" v-model="item.isOver" v-on:change="saveChange()">
          {{ item.things }}
        <!--<button @click="del(key)" class="btn">删除</button>-->
          <span @click="del(key)">
            <svg class="icon"><use xlink:href="#i-del"></use></svg>
          </span>
        </li>
      </ul>

      <div class="event-status">已结束：</div>
      <ul>
        <li v-for="(item,key) in todoList " v-if="item.isOver" :class="{'overPlease':item.isOver}" class="list-style">
<input type="checkbox" v-model="item.isOver" @change="saveChange()">
          {{ item.things }}
            <!--<button @click="del(key)" class="btn">删除</button>-->
          <span @click="del(key)">
            <svg class="icon"><use xlink:href="#i-del"></use></svg>
          </span>
        </li>
      </ul>
    </div>

    <button @click="delLocalStorage()" class="btn clean-btn">清除本地存储</button>
  </div>
</template>

<script>
  //导入模块
  import storage from './model/storage'

//  console.log(storage);
export default {
  data () {
    return {
      msg:'This is your own todoList',
        //输入框内容
      doWhat:'',
        //事件数组
      todoList:[
/*          {
            things:'语文作业',
            isOver:false
          },
          {
            things:'数学作业',
            isOver:true
          },*/
      ],
    }
  },
  methods:{
    add:function (e) {
          //监听是否按下回车键盘（keyCode为13）
        if(e.keyCode==13){
            //将输入事件push到todoList里面，给一个默认为false的isOver，代表未完成
          this.todoList.push({
            things:this.doWhat,
            isOver:false
          });
            //输入框置为空
          this.doWhat = '';
            //本地保存
//          localStorage.setItem('list',JSON.stringify(this.todoList));
          storage.set('list',this.todoList);
        }
    },
    del:function (key) {
        //删除todoList中对应项
      this.todoList.splice(key,1);
        //本地保存
//      localStorage.setItem('list',JSON.stringify(this.todoList));
      storage.set('list',this.todoList);
    },
    saveChange:function () {
        //checkbox改变时也要本地保存一下
        //以JSON字符串形式存储
//      localStorage.setItem('list',JSON.stringify(this.todoList));
      storage.set('list',this.todoList);
    },
    delLocalStorage:function () {   //清除本地存储
      storage.remove('list');
      if(!storage.get('list')){
          alert('本地数据已清除');
        location.reload();
      }else{
          alert('清除本地存储失败，请重试');
      }
    }
  },
  mounted(){    //生命周期函数
        //JSON字符串 => JSON对象
//      var list = JSON.parse(localStorage.getItem('list'));
    var list = storage.get('list');
    if(list){
        this.todoList = list;
    }
  }
}
</script>

<style lang="scss">

  @keyframes inputStyle{
    0%{
      border-bottom-color: #bdefea;
    }
    50%{
      border-bottom-color: #8789f6;
    }
    100%{
      border-bottom-color: #bdefea;
    }
  }

  #app{
    position: fixed;
    width: 70vw;
    height: 70vh;
    background-color: white;
    /*margin:0 auto;*/
    left: 50%;
    top:50%;
    margin-top: -35vh;
    margin-left: -35vw;
    border-radius: 20px;
    box-shadow: 5px 5px 10px #9a9a9a;

    .todo-title{
      padding: 15px 0;
      text-align: center;
      font-size: 1.5em;
      text-shadow: 2px 2px 5px #9a9a9a;
      color: #606060;
    }

  }

  .overPlease{
    text-decoration: line-through;
  }

  .textInput{
    width: 60%;
    height: 28px;
    outline:none;
    border: none;
    border-bottom: 2px solid #bdefea;
    padding-left: 2px;
    margin: 0 auto;
    display: block;
    margin: 0 auto;
    font-size: 1em;
    color: #666;
    font-family: 微软雅黑;

    &:focus{
      border-bottom-color: #8789f6;
      animation: inputStyle 3s infinite linear;
    }

    &::-webkit-input-placeholder, textarea::-webkit-input-placeholder {
      /* WebKit browsers */
      color: #a7a7a7;
    }
    &:-moz-placeholder, textarea:-moz-placeholder {
      /* Mozilla Firefox 4 to 18 */
      color: #a7a7a7;
    }
    &::-moz-placeholder, textarea::-moz-placeholder {
      /* Mozilla Firefox 19+ */
      color: #a7a7a7;
    }
    &:-ms-input-placeholder, textarea:-ms-input-placeholder {
      /* Internet Explorer 10+ */
      color: #a7a7a7;
    }

  }

  .list-area{
    width: 60%;
    height: 70%;
    margin: 16px auto 0 auto;
    overflow-y:auto;
    border-bottom: 1px solid #e6edef;
    /* 滚动槽 */
    &::-webkit-scrollbar {
      width: 4px;
    }
    &::-webkit-scrollbar-track {
      background: rgba(0,0,0,0.06);
      -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.08);
    }
    /* 滚动条滑块 */
    &::-webkit-scrollbar-thumb {
      background: rgba(0,0,0,0.12);
      -webkit-box-shadow: inset 0 0 10px rgba(0,0,0,0.2);
    }


    .event-status{
      font-size: 1.2em;
      text-shadow: 2px 2px 6px #a7a7a7;
      color: #666;
    }
  }

  .list-style{
    padding: 4px 0;
    margin-bottom: 3px;
    margin-right: 4px;
    vertical-align: middle;
    &:hover{
      background-color: #f6f6f6;
    }
    & span{
      float: right;
      cursor: pointer;
    }
  }


  /*按钮样式*/
  .btn {
    display: inline-flex;
    font-size: 12px;
    height: 27px;
    padding: 0 1em;
    font: inherit;
    border-radius: 6px;
    border: 1px solid #999;
    background: white;
    cursor: pointer;
    justify-content: center;
    /*子元素在父元素垂直居中*/
    align-items: center;
    /*防止多个button水平放置时会上下参差不齐*/
    vertical-align: middle;

    &:hover {
      border-color: #666;
    }
    &:active {
      background-color: #eee;
    }
    &:focus {
      outline: none;
    }
  }

  .clean-btn{
    position: fixed;
    left: 30px;
    bottom: 50px;
  }

  /*icon*/
  .icon{
    width: 1em;
    height: 1em;
    &:hover{
      cursor: pointer;
    }
  }


</style>

