<template>
<div id="app">
  <h1 class="slogan">Let's record your critical thinking</h1>

  <section class="today-thinkings">
    <header class="newThinking">
      <input type="text" 
        class="newTitle" 
        placeholder="Input your thinking title, choose a type and press Enter"
        v-model="newTitle"
        @keyup.enter="addThinking"
      >
      <select class="newType" v-model="newType">
        <option value="obs">O</option>
        <option value="qu">Q</option>
        <option value="idea">I</option>
      </select>
    </header>

    <thinking-list :thinkings="todayThinkings"></thinking-list>
  </section>

  <section class="history-thinkings">
    <ul>
      <li class="card" 
        v-if="card.length>0"
        v-for="(card, date) in historyThinkings">
        <header>
          <span class="date">{{ date }}</span>
          <!-- @add: 待添加功能——排序 -->
          <button class="sort">排序</button>
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
import initThinkings from './testData.json'
import ThinkingList from './components/ThinkingList.vue'

const STORAGE_KEY = 'critical-thinking-record';

let recordsStorage = {
  fetch(){
    let records = JSON.parse(localStorage.getItem(STORAGE_KEY)) || initThinkings;
    // @add: 将昨天的数据推入历史数据中
    return records;
  },
  save(records){
    localStorage.setItem(STORAGE_KEY, JSON.stringify(records));
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
  methods: {
    addThinking(){
      if(this.newTitle && this.newTitle.trim()){
        this.todayThinkings.unshift({
          type: this.newType,
          title: this.newTitle,
          time: new Date(),
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
  margin-bottom: 200px;

  .slogan {
    margin-top: 100px;
    text-align: center;
    font-size: 60px;
    color:#75aacd;
  }

  .newThinking {
    position: relative;
    margin-top: 50px;
    
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

  .history-thinkings {
    margin-top: 100px;

    .card {
      width: 640px;
      padding: 20px;
      margin-bottom: 50px;
      border-radius: 15px;
      // box-shadow: #cae1e9 1px 1px 15px;
      background-color: #f5f5f5;
    }
    .date, .sort{
      font-size: 18px;
      color: #75aacd;
    }
    .sort{
      float: right;
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
