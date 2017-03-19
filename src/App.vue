<template>
<div id="app">
  <h1 class="slogan">Let's record your critical thinking</h1>

  <div class="newThinking">
    <input type="text" 
      class="newTitle" 
      placeholder="Input your thinking title, choose a type and press Enter"
      v-model.trim="newTitle"
      @keyup.enter="addThinking"
    >
    <select class="newType" v-model="newType">
      <option value="obs">O</option>
      <option value="qu">Q</option>
      <option value="idea">I</option>
    </select>
  </div>

  <section class="today-thinkings">
    <thinking-list :thinkings="todayThinkings"></thinking-list>
  </section>

  <section class="history-thinkings">
    <ul>
      <li class="card" 
        v-if="card.length>0"
        v-for="card in historyThinkings">
        <header>
          <span class="date">{{ card[0].time | parseTime | displayYMD }}</span>
          <!-- @add: 待添加功能——排序 -->
          <!-- <button class="sort">排序</button> -->
        </header>
        <thinking-list :thinkings="card"></thinking-list>
      </li>
    </ul>
  </section>

<!--  @add: 待添加功能——查看更多历史 
  <div class="see-history">
    <button class="btn">&or;</button>
    <button class="btn">&or;</button>
  </div> 
-->
</div>
</template>

<script>

// 引入测试数据
import initThinkings from './testData.json'

// 引入组件
import ThinkingList from './components/ThinkingList.vue'

const STORAGE_KEY = 'critical-thinking-record';

let recordsStorage = {
  fetch(){
    let records = JSON.parse(localStorage.getItem(STORAGE_KEY)) || initThinkings;
    return this.update(records);
  },
  save(records){
    localStorage.setItem(STORAGE_KEY, JSON.stringify(records));
  },
  // 将今天以前的数据推入历史数据中
  update(records){
    let todayThinkings = records.todayThinkings;
    if(todayThinkings.length > 0 && this._hasExpire(todayThinkings[0].time)){
      records.historyThinkings.unshift(todayThinkings);
      records.todayThinkings = [];
    }
    return records;
  },
  // 以天为单位进行过期验证
  _hasExpire(time){
    let oldTime = time;
    let newTime = new Date();
    newTime = + new Date(newTime.getFullYear(),
                      newTime.getMonth(),
                      newTime.getDate(),
                      0, 0, 0);
    return  oldTime < newTime;
  }
};

export default {
  name: 'app',
  components: {
    ThinkingList
  },
  data () {
    return {
      newTitle: '',
      newType: 'qu',
      beforeEditCache: null,
      records: recordsStorage.fetch(),
    }
  },
  computed: {
    todayThinkings(){
      return this.records.todayThinkings;
    },
    historyThinkings(){
      return  this.records.historyThinkings
    }
  },
  watch: {
    records: {
      handler: function(records){
        recordsStorage.save(records);
      },
      deep: true
    }
  },
  filters: {
    parseTime(time){
      let date = time > 0 ? new Date(time) : new Date();
      return {
        year: date.getFullYear(),
        month: padding(date.getMonth()+1),
        date: padding(date.getDate()),
        hours: padding(date.getHours()),
        minutes: padding(date.getMinutes())
      }
      function padding(num){
          return num > 9 ? num+'' : '0'+ num;
      }
    },
    displayYMD(time){
      return time.year + '-' + time.month + '-' + time.date;
    }
  },
  methods: {
    addThinking(){
      if(this.newTitle){
        this.todayThinkings.unshift({
          type: this.newType,
          title: this.newTitle,
          time: + new Date(),
          detail:'Double click and write done your descriptions.',
          showDetail: false,
          editingTitle: false,
          editingDetail: false
        });
        this.newTitle = '';
      }
    }
  }
}
</script>

<style lang="scss">
$themeColor: #75aacd;
$focusColor: #b7daf2;

#app {
  width: 640px;
  margin: 0 auto;
  margin-bottom: 100px;

  .slogan {
    margin-top: 80px;
    text-align: center;
    font-size: 60px;
    color: #75aacd;
    opacity: 0.6;
  }

  .newThinking {
    position: relative;
    margin-top: 70px;
    
    .newTitle {
      width: 100%;
      height: 70px;
      padding: 35px;
      padding-left: 25px;
      padding-right: 68px;
      border: 1px solid #3a89bd;
      border-radius: 10px;
      font-size: 18px;
      color: #8b8b8b;
    }
    .newTitle:focus {
      border: 1px solid $focusColor;
      box-shadow: #2872a5 0 0 5px;
    }  

    .newType {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 25px;
      font-size: 18px;
      color: #75aacd;
    }
  }

  .today-thinkings .thinking-list {
    padding-left: 57px;
    margin-top: 50px;
  }

  .history-thinkings {
    padding: 0 30px;
    margin-top: 100px;

    .card {
      padding: 20px;
      padding-top: 30px;
      padding-left: 40px;
      margin-bottom: 50px;
      border-radius: 15px;
      background-color: #f5f5f5;
    }

    .date, .sort {
      font-size: 18px;
      color: #75aacd;
    }

    .sort {
      float: right;
    }

    .thinking-list {
      margin-top: 40px;
      margin-left: 18px;
    }
  } 

  .history-thinkings  .thinking-list {
    .abstract {
      label, textarea {
        font-size: 14px;
        width: 300px;
      }

      .time {
        margin: 0 30px;
      }
    }

    .thinking:hover {
      transform: translateX(-18px);
    }

    .detail {
      label, textarea {
        font-size: 12px;
        width: 290px;
      }
    }
  }
  // .see-history{
  //   button{
  //     display: table;
  //     margin: 0 auto;
  //     transform: scaleX(6);
  //     color: $themeColor;
  //   }
  //   button+button{
  //     margin-top: -5px;
  //   }
  // } 
}
</style>
