<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Vue-备忘录</h1>
    <div id="todolist">
      <div class="textPut">
        <button class="btn1" v-on:click="finishAll()">V</button>
        <input type="text" placeholder="输入一个备忘录"
               v-model="newmsg"
               v-on:keyup.enter="newItem(newmsg)">
        <button class="send" v-on:click="newItem(newmsg)">New</button>
      </div>
      <content>
        <ul class="list">
          <li v-for="(item,index) in items"
              v-bind:key="item.id"
              v-bind:class="{ finished : item.isFinished}"
              v-show="item.show"
              v-on:mouseover="butDisplay(item)"
              v-on:mouseout="butDisappear(item)">
            <label
              v-if="item.flag">
              {{ item.message }}
            </label>
            <input type="text" class="editinp"
                   v-else
                   v-model="item.message"
                   v-on:keydown.enter="editedItem(item)" v-on:blur="editedItem(item)" v-focus>
            <span class="deleList" v-on:click="deleteItem(item,index)" v-show="item.icon">删除</span>
            <span class="editList" v-on:click="editItem(item,index)" v-show="item.icon">编辑</span>
            <span class="compList" v-on:click="switchFinish(item)" v-show="item.icon">完成</span>
          </li>
        </ul>
      </content>
      <footer>
        <div id="todoCount">未完成数：{{ countAble }}</div>
        <div class="all" v-on:click="allShow"
             v-bind:class="{ butkeep: allKeep}">全部</div>
        <div class="able" v-on:click="hideFinish"
             v-bind:class="{ butkeep: ableKeep}">未完成</div>
        <div class="already" v-on:click="hideTodo"
             v-bind:class="{ butkeep: compKeep}">已完成</div>
        <a href="javascript:;" class="clearfin" v-on:click="clearFinish" v-show="arise">清空已完成</a>
      </footer>
    </div>
  </div>
</template>

<script>
  import store from './components/store'
export default {
  name: 'App',
  data() {
    return {
      msg: 'this is a todolist',
      items: store.fetch(),
      newmsg:'',
      allKeep:true,
      ableKeep:false,
      compKeep:false,
    }
  },
  computed:{
    countItem: function () {
      return this.items.length;
    },
    countFinished: function () {
      let j = 0;
      for( let i=0; i< this.items.length ;i++ ) {
        if (this.items[i].isFinished) {
          j+=1;
        }
      }
      return j;
    },
    countAble: function () {
      return this.countItem-this.countFinished;
    },
    arise:function () {
      if(this.countFinished != 0){
        return true;
      }else {
        return false;
      }
    }
  },
  watch:{
    countItem:function (val) {
      if(val ==0){
        document.getElementsByTagName('content')[0].style.display='none';
        document.getElementsByTagName('footer')[0].style.display='none';
      }else {
        document.getElementsByTagName('content')[0].style.display='block';
        document.getElementsByTagName('footer')[0].style.display='block';
      }
    },
    items:{
      handler: function(val) {
        store.save(val);
      },
      deep: true
    }
  },
  methods:{
    switchFinish: function (item) {
      item.isFinished = !item.isFinished;
    },
    finishAll: function () {
      for(let i = 0; i<this.items.length;i++) {
        if (!this.items[i].isFinished) {
          for(let j = 0; j<this.items.length;j++){
            this.items[j].isFinished = true;
          }
          return;
        }
      }
      for(let i = 0; i<this.items.length;i++){
        this.items[i].isFinished = false;
      }
    },
    newItem: function (newmsg) {
      if(!newmsg){
        return
      }
      this.items.unshift({
        message: newmsg,
        isFinished: false,
        flag : true,
        show : true,
        icon : false,
      });
      this.newmsg=''
    },
    deleteItem: function (item,index) {
      this.items.splice(index, 1);
    },
    editItem: function (item) {
      item.flag = !item.flag;
      item.icon = false;
    },
    editedItem: function (item) {
      item.flag = true;
    },
    hideFinish: function () {
      for(let i = 0; i<this.items.length; i++){
        if(this.items[i].isFinished){
          this.items[i].show = false;
        }else{
          this.items[i].show = true;
        }
      }
      this.ableKeep = true;
      this.allKeep = false;
      this.compKeep = false;
    },
    hideTodo: function () {
      for(let i = 0; i<this.items.length; i++){
        if(!this.items[i].isFinished){
          this.items[i].show = false;
        }else{
          this.items[i].show = true;
        }
      }
      this.ableKeep = false;
      this.allKeep = false;
      this.compKeep = true;
    },
    allShow: function () {
      for(let i = 0; i< this.items.length;i++){
        this.items[i].show = true;
      }
      this.ableKeep = false;
      this.allKeep = true;
      this.compKeep = false;
    },
    clearFinish: function () {
      for (let i = 0; i < this.items.length; i++) {
        if (this.items[i].isFinished) {
          this.items.splice(this.items.indexOf(this.items[i]), 1);
          i--;
        }
      }
    },
    butDisappear: function (item) {
      item.icon = false;
    },
    butDisplay: function (item) {
      item.icon = true;
    },
  },
  directives: {
    focus: {
      // 指令的定义
      inserted: function (el) {
        el.focus()
      }
    }
  },
}
</script>

<style>
  *{
    margin: 0 auto;
    padding: 0;
  }
  #app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0 auto;
  padding: 0;
  width: 400px;
  }
  #app img{
    max-width: 150px;
  }
  h1{
    font-size: 60px;
  }
  #todolist{
    background: white;
  }
  .textPut{
    position: relative;
    width: 100%;
    font-size: 30px;
  }
  .textPut button.btn1{
    width: 36px;
    height: 46px;
    position: absolute;
    background: darkseagreen;
    left: 10px;
    top: 2px;
    border-radius: 10px;
    border-color: ghostwhite;
    border-style: solid;
    border-width: 5px;
  }
  .textPut button.send{
    width: 50px;
    height:46px;
    position: absolute;
    background: lightslategrey;
    right:0;
    top: 2px;
    border-radius: 10px;
    border-color: ghostwhite;
    border-style: solid;
    border-width: 5px;
  }
  .textPut input{
    width: 285px;
    height: 40px;
    padding: 5px 50px 5px 50px;
    font-size: 30px;
    border: 0;
  }
  ul{
    width: 300px;
    margin: 0;
    margin-left: 30px;
    text-align: center;
    font-family: Microsoft "Microsoft YaHei";
    font-size: 22px;
    list-style: decimal;
  }
  ul li{
    width: 300px;
    padding-right: 70px;
    position: relative;
    padding-bottom: 5px;
    border-bottom:1px solid grey;
  }
  ul li label{
    width: 240px;
    padding: 0 30px 0 40px;
    word-break: break-all;
  }
  ul li .editinp{
    border:2px solid steelblue;
    font-size: 22px;
    width: 220px;
    padding: 0 30px 0 10px;
    margin-left: 30px;
    color: #2c3e50;
    float: left;
  }
  ul li span.editList{
    position: absolute;
    left: 0;
    top: 0;
  }
  ul li span.compList{
    position: absolute;
    right: 0;
    top: 0;
  }
  ul li span.deleList{
    position: absolute;
    right: 40px;
    top: 0;
  }
  .editList,.compList,.deleList{
    font-size: 15px;
    color: green;
    cursor: pointer;
  }
  .finished label{
    text-decoration: line-through;
  }
  footer{
    position: relative;
    height: 30px;
    text-align: center;
    font-size: 15px;
  }
  footer:before{
    content:'';
    position: absolute;
    right: 0;
    bottom: 0;
    left: 0;
    height: 20px;
    overflow: hidden;
    box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6, 0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6, 0 17px 2px -6px rgba(0, 0, 0, 0.2);
  }
  footer #todoCount,.all,.already,.able,.clearfin{
    top:5px;
    position: absolute;
  }
  footer div.butkeep{
    padding: 0 2px;
    border-radius: 4px;
    border: 1px solid lightgrey;
  }
  .all{
    left:250px;
    padding: 0 4px;
    border: 2px solid white;
    cursor: default;
  }
  .already{
    left:110px;
    padding: 0 4px;
    border: 2px solid white;
    cursor: default;
  }
  .able{
    padding: 0 4px;
    left:180px;
    border: 2px solid white;
    cursor: default;
  }
  a.clearfin:link,a.clearfin:visited,a.clearfin:active{
    right: 0;
    text-decoration: none;
    color: #2c3e50;
  }
</style>
