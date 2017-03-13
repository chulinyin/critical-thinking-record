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

    <section class="thinking-list">
      <ul >
        <li class="thinking" 
          v-for="thinking in todayThinkings">
          
          <div class="abstract"
            :class="{ editing: thinking.editingTitle }">
            <div class="icon"> 
              <img v-if="thinking.type === 'obs'"
                src="./assets/obs_icon.png" alt="">
              <img v-else-if="thinking.type === 'qu'" 
                src="./assets/qu_icon.png" alt="">
              <img v-else-if="thinking.type === 'idea'" 
                src="./assets/idea_icon.png" alt="">
            </div> 
            
            <label @dblclick="editTitle(thinking)">{{ thinking.title }}</label>
            <textarea
              v-model="thinking.title"
              v-focus="thinking.editingTitle"
              @blur="doneEditTitle(thinking)"
              @keyup.enter="doneEditTitle(thinking)"
              @keyup.esc="cancelEditTitle(thinking)"
            ></textarea> 

            <button class="seeDetail" 
              :class="{rotate180:thinking.showDetail}" 
              @click="toggleDetial(thinking)"
            >&or;</button>
            <span class="time">{{ thinking.time }}</span>
            <button class="delete" 
              @click="removeThinking(thinking)"
            >&Chi;</button> 
          </div>

          <div class="detail" 
            :class="{ editing: thinking.editingDetail }"
            v-show="thinking.showDetail"
            >
            <label @dblclick="editDetail(thinking)">{{ thinking.detail }}</label>
            <textarea
              v-model="thinking.detail"
              v-focus="thinking.editingDetail"
              @blur="doneEditDetail(thinking)"
              @keyup.enter="doneEditDetail(thinking)"
              @keyup.esc="cancelEditDetial(thinking)"
              ></textarea> 
          </div>
        </li>
      </ul>
    </section>
  </section>
</div>
</template>

<script>
const STORAGE_KEY = 'critical-thinking-record';

const initThinkings = [
  {
    type: 'idea',
    title: '这里可以输入一个有趣想法的简短描述哦～',
    time: getTime(),
    detail: '这里你就可以输入详细的描述了，可以之后再慢慢添加哦～',
    showDetail: false,
    editingTitle: false,
    editingDetail: false
  },
  {
    type: 'obs',
    title: '这里可以输入一个有趣观察的简短描述哦～',
    time: getTime(),
    detail: '这里你就可以输入详细的描述了，可以之后再慢慢添加哦～',
    showDetail: false,
    editingTitle: false,
    editingDetail: false
  },
  {
    type: 'qu',
    title: '这里可以输入一个有趣问题的简短描述哦～',
    time: getTime(),
    detail: '这里你就可以输入详细的描述了，可以之后再慢慢添加哦～',
    showDetail: false,
    editingTitle: false,
    editingDetail: false
  }
];

function getTime() {
  let date = new Date();
  let hours = padding(date.getHours());
  let minutes = padding(date.getMinutes());
  function padding(num){
      return num > 9 ? num+'' : '0'+ num
  }
  return hours + ' : ' + minutes;
}

let thinkingStorage = {
  fetch(){
    let thinkings = JSON.parse(localStorage.getItem(STORAGE_KEY)) || initThinkings;
    thinkingStorage.uid = thinkings.length;
    return thinkings;
  },
  save(thinkings){
    localStorage.setItem(STORAGE_KEY, JSON.stringify(thinkings));
  }
};

export default {
  name: 'app',
  data () {
    return {
      newTitle: '',
      newType: 'qu',
      beforeEditCache: null,
      todayThinkings: thinkingStorage.fetch()
    }
  },
  watch: {
    todayThinkings: {
      handler: function(todayThinkings){
        thinkingStorage.save(todayThinkings);
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
          time: getTime(),
          detail:'Double click and write done your descriptions.',
          showDetail: false,
          editingTitle: false,
          editingDetail: false
        });
        this.newTitle = '';
      }
    },
    removeThinking(thinking){
      let index = this.todayThinkings.indexOf(thinking)
      this.todayThinkings.splice(index,1);
    },
    editTitle(thinking){
      this.beforeEditCache = thinking.title;
      thinking.editingTitle = true;
    },
    doneEditTitle(thinking){
      // 防止cancel后再执行一遍
      if(!thinking.editingTitle) return;
      thinking.editingTitle = false;
      thinking.title = thinking.title.trim();
      if(!thinking.title) {
        this.removeThinking(thinking);
      }
    },
    cancelEditTitle(thinking){
      thinking.title = this.beforeEditCache;
      thinking.editingTitle = false;
    },
    editDetail(thinking){
      this.beforeEditCache = thinking.detail;
      thinking.editingDetail = true;
    },
    doneEditDetail(thinking){
      if(!thinking.editingDetail) return;
      thinking.editingDetail = false;
      thinking.detail = thinking.detail.trim();
    },
    cancelEditDetail(thinking){
      thinking.detail = this.beforeEditCache;
      thinking.editingDetail = false;
    },
    toggleDetial(thinking){  
      thinking.showDetail = !thinking.showDetail;
    }
  },
  directives: {
    // @add: 第一次获得焦点时，选中全部文字
    focus: function(el, binding){
      if(binding.value) el.focus();;
    }
  }
}
</script>

<style lang="scss">
$focusColor: #F58120;

#app {
  width: 600px;
  margin: 0 auto;
  margin-bottom: 200px;

  .slogan {
    margin-top: 200px;
    text-align: center;
    font-size: 60px;
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
      border: 1px solid #ccc;
      border-radius: 10px;
      font-size: 18px;
      color: #8b8b8b;
    }
    .newTitle:focus {
      border: 1px solid $focusColor;
    }  

    .newType {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      right: 25px;
      font-size: 18px;
    }
  }
}

#app .today-thinkings > .thinking-list {
  padding-left: 30px;
  margin-top: 50px;

  .thinking {
    margin-bottom: 30px;  
    transition: transform .3s ease-out;   
  }
  .thinking:hover {
    transform: translateX(-25px);

    .delete {
      display: inline-block;
    }
  }

  .abstract { 
    display: flex;
    align-items: center;

    .icon {
      width: 30px;
      height: 30px;
      text-align: center;
    }

    label, textarea {
      width: 360px;
      margin-left: 18px;
      margin-right: 25px;
      font-size: 16px;
      line-height:1.5;
    }
    textarea {
      display: none;
      padding: 15px 17px;
      border: 1px solid #ddd;
      overflow: visible;
    }
    textarea:focus {
      border: 1px solid $focusColor;
    }

    .seeDetail {
      transform: scaleX(2);
      color: rgba(94,161,244,0.6);
    }
    .seeDetail:hover {
      color: rgba(94,161,244,1);
    }
    .seeDetail.rotate180 {
      transform: scaleX(2) rotate(180deg);
    }

    .time {
      margin: 0 40px;
      color: #a2cc97;
    }

    .delete {
      display: none;
      transform: scale(1.5);
      color: #cc9797;
      transition: all .5s ease .1s;
    }
    .delete:hover  {
      transform: scale(1.8) rotate(-180deg);
      color: #c76e6e;
    }
  }
  .abstract.editing {
    label{
      display: none;
    }
    textarea{
      display: block;
    }
  }

  .detail {
    margin-left: 50px;
    margin-top: 12px;

    label, textarea {
      width: 350px;
      padding: 20px;
      border: 1px dashed #ddd;
      font-size: 12px;
      line-height: 1.8;
      color: #afafaf;
    }
    label {
      padding-right: 16px;
    }
    label {
      display: block;
    } 
    textarea {
      display: none;
    }
    textarea:focus {
      border-color: $focusColor;
    }
  }
  .detail.editing {
    label {
      display: none;
    }
    textarea {
      display: block;
    }    
  }
}
</style>
