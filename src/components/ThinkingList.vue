<template>
	<ul class="thinking-list">
		<li class="thinking"
			v-for="thinking in thinkings">
      <div class="abstract"
        :class="{ editing: thinking.editingTitle }">
        <div class="icon"> 
          <img v-if="thinking.type === 'obs'"
            src="../assets/obs_icon.png" alt="">
          <img v-else-if="thinking.type === 'qu'" 
            src="../assets/qu_icon.png" alt="">
          <img v-else-if="thinking.type === 'idea'" 
            src="../assets/idea_icon.png" alt="">
        </div> 
        
        <label @dblclick="editTitle(thinking)">{{ thinking.title }}</label>
        <textarea
          v-model.trim="thinking.title"
          v-focus="thinking.editingTitle"
          @blur="doneEditTitle(thinking)"
          @keyup.enter="doneEditTitle(thinking)"
          @keyup.esc="cancelEditTitle(thinking)"
        ></textarea> 

        <button class="seeDetail" 
          :class="{rotate180:thinking.showDetail}" 
          @click="toggleDetial(thinking)"
        >&or;</button>
        <span class="time">{{ thinking.time | parseTime | displayHM }}</span>
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
          v-model.trim="thinking.detail"
          v-focus="thinking.editingDetail"
          @blur="doneEditDetail(thinking)"
          @keyup.enter="doneEditDetail(thinking)"
          @keyup.esc="cancelEditDetial(thinking)"
          ></textarea> 
      </div>							
		</li>
	</ul>	
</template>

<script>
  
export default {
	data(){
    return {};
	},
	props:{
		thinkings: {
			type: Array,
			required: true
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
    displayHM(time){
      return time.hours + ' : ' + time.minutes;
    }
  },
  methods: {
    removeThinking(thinking){
      let index = this.thinkings.indexOf(thinking)
      this.thinkings.splice(index,1);
    },
    editTitle(thinking){
      this.beforeEditCache = thinking.title;
      thinking.editingTitle = true;
    },
    doneEditTitle(thinking){
      // 防止cancel后再执行一遍
      if(!thinking.editingTitle) return;
      thinking.editingTitle = false;
      // thinking.time = + new Date();
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
      // thinking.time = + new Date();
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

<style lang="scss" scoped>
$focusColor: #F58120;

.thinking-list {

  .thinking {
    margin-bottom: 30px;  
    transition: transform .3s ease-out;   
  }
  .thinking:hover {
    transform: translateX(-28px);

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
      font-size: 18px;
      line-height: 1.5;
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
      display: block;
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
      border-color:  #75aacd;
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