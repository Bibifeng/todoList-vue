<template>
  <div id="app">
    <h1>{{ msg }}</h1>
    <input type="text" v-model="doWhat" v-on:keydown="add($event)" placeholder="输入计划" class="textInput">

    <h3>未完成</h3>
    <ul>
      <li v-for="(item,key) in todoList " v-if="!item.isOver">
        <input type="checkbox" v-model="item.isOver" v-on:change="saveChange()">
        {{ item.things }}
        <button @click="del(key)">删除</button>
      </li>
    </ul>

    <h3>已结束</h3>
    <ul>
      <li v-for="(item,key) in todoList " v-if="item.isOver" :class="{'overPlease':item.isOver}">
        <input type="checkbox" v-model="item.isOver" @change="saveChange()">
        {{ item.things }}
         <button @click="del(key)">删除</button>
      </li>
    </ul>
    <hr>
    <button @click="delLocalStorage()">清除本地存储</button>
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
          alert('已清除,请刷新页面');
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

<style>
  .overPlease{
    text-decoration: line-through;
  }

  .textInput{
    width: 400px;
    height: 20px;
    outline:none;
    border: solid 1px #b4b4b4;
    border-radius: 4px;
    padding-left: 2px;
  }
  .textInput:focus{
    border: solid 1px #05aaf4;
    -webkit-box-shadow:inset 0 1px 1px rgba(0, 0, 0, .075), 0 0 8px rgba(102, 175, 233, .6);
    box-shadow:inset 0px 1px 1px rgba(0,0,0,0.075), 0px 0px 8px rgba(102,175,233,0.6);
  }

</style>

