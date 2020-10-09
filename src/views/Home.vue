<template>
  <div class="home">
    <div class="header">
      <button class="back-button" @click="backQuestion">
        Артка
      </button>
      <template>
        <button class="next-button" @click="nextQuestion">
          Алга
        </button>
      </template>
      <button class="back-button" @click="end">
        Аяқтау
      </button>
    </div>
    <div v-if="$route.query.type == 2">
      <div>
        <select v-model="select" id="select" @change="questionId = 0">
          <option value="1">Математикалық сауаттылық</option>
          <option value="2">Қазақстан тарихы</option>
          <option value="3">Оқу сауаттылығы</option>
          <option value="4">{{sub1}}</option>
          <option value="5">{{sub2}}</option>
        </select>
      </div>
      <div class="container">
        <div class="question" v-for="(i, ind) in questions[select-1]" :key="ind">
          <template v-if="ind === questionId">
            <h4>{{ i.text }}</h4>
            
            <img v-for="im in i.question_photo" :src="im.photo" :key="im.photo">
            <div class="answers" v-for="(v, index) in i.question_variant" :key="index">
              <button  :id="`button`+v.id" @click="touchButton(ind, v.id)"
                      :class="[i.selected_id.includes(v.id) ? 'active' : '', 'variant-button']">
                {{ v.text }}
                {{i.selected_id.includes(v.id)}}
              </button>
            </div>
          </template>
        </div>
      </div>
    </div>
    <div v-else>
      <div>
        <div class="subname">{{sub1}}</div>
      </div>
      <div class="container">
        <div class="question" v-for="(i, ind) in questions" :key="ind">
          <template v-if="ind === questionId">
            <h4>{{ i.text }}</h4>
            
            <img v-for="im in i.question_photo" :src="im.photo" :key="im.photo">
            <div class="answers" v-for="(v, index) in i.question_variant" :key="index">
              <button  :id="`button`+v.id" @click="touchButton(ind, v.id)"
                      :class="[i.selected_id.includes(v.id) ? 'active' : '', 'variant-button']">
                {{ v.text }}
              </button>
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Home',
  data() {
    return {
      questionId: 0,
      select: 1,
      sub1: '',
      sub2: '',
      sub1_id: 0,
      sub2_id: 0,
      questions: [
        
      ],
    }
  },
  mounted(){
    let headers = {
      "Authorization": "Token " + this.$route.query.key
    }
    if (this.$route.query.type == 1){
      axios.get(
        "http://138.68.102.163/ent/oneSubject/"+this.$route.query.sub1,
        {headers}
      ).then(r=>{
        for(let i in r.data.data){
          r.data.data[i]['selected_id'] = []
        }
        this.questions = r.data.data
        this.sub1 = r.data.subject
        this.sub1_id = r.data.data[0].subject
      }, r=> {
        console.log(r)
      })
    }else{
      let data = {
        "sub_id1": this.$route.query.sub1,
        "sub_id2": this.$route.query.sub2
      }
      axios.post(
        "http://138.68.102.163/ent/pass",
        data,
        {headers}
      ).then(r => {
        let i = r.data
        this.sub1 = i.sub1.subject
        this.sub2 = i.sub2.subject
        this.sub1_id = i.sub1.data[0].subject
        this.sub2_id = i.sub2.data[0].subject
        for (let s in i.math_log){
          i.math_log[s]['selected_id'] = []
        }
        for (let s in i.history){
          i.history[s]['selected_id'] = []
        }
        for (let s in i.literacy){
          i.literacy[s]['selected_id'] = []
        }
        for (let s in i.sub1.data){
          i.sub1.data[s]['selected_id'] = []
        }
        for (let s in i.sub2.data){
          i.sub2.data[s]['selected_id'] = []
        }
        this.questions.push(i.math_log)
        this.questions.push(i.history)
        this.questions.push(i.literacy)
        this.questions.push(i.sub1.data)
        this.questions.push(i.sub2.data)
      }, r=> {
        console.log(r)
      })
    }
  },
  methods: {
    nextQuestion() {
      if (this.$route.query.type == 1){
        if (this.questionId < this.questions.length-1){
          this.questionId++
        }
      }
      else{
        if (this.questionId < this.questions[this.select-1].length-1){
          this.questionId++
        }
      }
    },
    backQuestion() {
      if (this.questionId > 0){
        this.questionId--
      }
    },
    touchButton(id1, id2) {
      if (this.$route.query.type == 1){
        if (this.questions[id1].question_variant.length >= 8){
          if (this.questions[id1]['selected_id'].includes(id2)){

          }else{
            this.questions[id1]['selected_id'].push(id2)
            if (this.questions[id1]['selected_id'].length > 3){
              this.questions[id1]['selected_id'] = this.questions[id1]['selected_id'].slice(1)
            }
            let btns = document.querySelectorAll("[id^='button']");
            btns.forEach(e => {
              e.classList.remove('active')
            })
            for (let s in this.questions[id1]['selected_id']){
              let variantButton = document.getElementById("button"+this.questions[id1]['selected_id'][s].toString());
              variantButton.classList.add('active')
            }
          }
        }else{
          let btns = document.querySelectorAll("[id^='button']");
          btns.forEach(e => {
            e.classList.remove('active')
          })
          let variantButton = document.getElementById(`button${id2}`);
          variantButton.classList.add('active')
          this.questions[id1]['selected_id'] = [id2]
        }
      }
      else{
        if (this.questions[this.select-1][id1].question_variant.length >= 8){
          if (this.questions[this.select-1][id1]['selected_id'].includes(id2)){

          }else{
            this.questions[this.select-1][id1]['selected_id'].push(id2)
            if (this.questions[this.select-1][id1]['selected_id'].length > 3){
              this.questions[this.select-1][id1]['selected_id'] = this.questions[this.select-1][id1]['selected_id'].slice(1)
            }
            let btns = document.querySelectorAll("[id^='button']");
            btns.forEach(e => {
              e.classList.remove('active')
            })
            for (let s in this.questions[this.select-1][id1]['selected_id']){
              let variantButton = document.getElementById("button"+this.questions[this.select-1][id1]['selected_id'][s].toString());
              variantButton.classList.add('active')
            }
          }
        }else{
          let btns = document.querySelectorAll("[id^='button']");
          btns.forEach(e => {
            e.classList.remove('active')
          })
          let variantButton = document.getElementById(`button${id2}`);
          variantButton.classList.add('active')
          this.questions[this.select-1][id1]['selected_id'] = [id2]
        }
      }
    },
    end(){
      this.$router.push({name: "Test", params: {'questions': this.questions}, 
                        query: {'sub1': this.sub1, "sub2": this.sub2, 'key': this.$route.query.key,
                                'type': this.$route.query.type, 
                                "sub1_id": this.sub1_id, "sub2_id": this.sub2_id}})
    }
  }
}
</script>

<style lang="scss" scoped>
* {
  margin: 0;
  padding: 0;
}
.home {
  .header {
    display: flex;
    padding: 5px 20px;
    button {
      width: 100px;
      height: 30px;
      font-size: 14px;
      font-weight: 600;
      text-transform: uppercase;
    }
  }
  .container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
    .question {
      display: flex;
      flex-direction: column;
      padding: 40px 0px;
      position: absolute;
      top: 60px;
      width: 90%;
      h4 {
        margin-bottom: 5px;
      }
      img {
        max-width: 300px;
        max-height: 400px;
        margin-bottom: 20px;
      }
      .variant-button {
        width: 100%;
        height: 35px;
        text-align: left;
        padding: 5px;
        margin: 5px 5px 5px 0;
        justify-self: center;
        border: 1px solid #929292;
        border-radius: 10px;
        background: #f1f1f1;
      }
      .success {
        background: #4fb34f;
      }
    }
  }
}
select{
  width: 100%;
  height: 30px;
  padding: 2px 5px;
  border: 1px solid #929292;
  background: #f1f1f1;
}
.active{
  background: green!important; 
  color: #fff;
}
.subname{
  background:  #f1f1f1;
  font-weight: 500;
  font-size: 1.1em;
  text-align: center;
  padding: 5px;
  color: #000;
}
</style>

