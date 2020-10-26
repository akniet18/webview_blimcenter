<template>
  <div class="home">
    <div class="header">
      <div> </div>
      <div style="font-weight: bold; justify-self: center">Тестілеу</div>
      <div class="finish" @click="end">
        Аяқтау
      </div>
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
        <div class="question">
          <template >
            <h4 v-html="questions[select-1][questionId].text"></h4>
            <img v-for="im in questions[select-1][questionId].question_photo" :src="im.photo" :key="im.photo">
          </template>
        </div>

         <div class="settings">
          <button class="back-button" @click="backQuestion">
             <div style="font-size: 1.4em">&#60;</div> 
          </button>
          <div>
            {{questionId+1}}/{{questions[select-1].length}}
          </div>
          <button class="next-button" @click="nextQuestion">
            <div style="font-size: 1.4em">&#62;</div>
          </button>
        </div>

        <div class="question">
          <template>
            <div class="answers" v-for="(v, index) in questions[select-1][questionId].question_variant" :key="index">
              <button  :id="`button`+v.id" @click="touchButton(questionId, v.id)"
                      :class="[questions[select-1][questionId].selected_id.includes(v.id) ? 'active' : '', 'variant-button']">
                {{ v.text }}
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
        <div class="question">
          <template >
            <h4 v-html="questions[questionId].text"></h4>
            <img v-for="im in questions[questionId].question_photo" :src="im.photo" :key="im.photo">
          </template>
        </div>
        
        <div class="settings">
          <button class="back-button" @click="backQuestion">
             <div style="font-size: 1.4em">&#60;</div> 
          </button>
          <div>
            {{questionId+1}}/{{questions.length}}
          </div>
          <button class="next-button" @click="nextQuestion">
            <div style="font-size: 1.4em">&#62;</div>
          </button>
        </div>
        
        <div class="question">
          <template>
            <div class="answers" v-for="(v, index) in questions[questionId].question_variant" :key="index">
              <button  :id="`button`+v.id" @click="touchButton(questionId, v.id)"
                      :class="[questions[questionId].selected_id.includes(v.id) ? 'active' : '', 'variant-button']">
                {{ v.text }}
              </button>
            </div>
          </template>
        </div>

        <div class="modal" ref="modal">
          <div>
            <div>Тестті аяқтағыңыз келеді ме?</div>
            <div class="modalBtnDiv">
              <div class="modalBtn" @click="yesmodal">Иә</div>
              <div class="modalBtn" @click="nomodal">Жоқ</div>
            </div>
          </div>
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
        "http://back.bilimcentre.kz/ent/oneSubject/"+this.$route.query.sub1,
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
        "http://back.bilimcentre.kz/ent/pass",
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
      this.$refs.modal.style.display = "block"
    },
    yesmodal(){
      this.$refs.modal.style.display = "none"
      this.$router.push({name: "Test", params: {'questions': this.questions}, 
                        query: {'sub1': this.sub1, "sub2": this.sub2, 'key': this.$route.query.key,
                                'type': this.$route.query.type, 
                                "sub1_id": this.sub1_id, "sub2_id": this.sub2_id}})
    },
    nomodal(){
      this.$refs.modal.style.display = "none"
    }
  }
}
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  -webkit-user-select:none;
  user-select: none;
}
button {
-webkit-appearance: none;
-moz-appearance: none;
appearance: none;
outline: none;
}
.home {
  .header {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    color: #fff;
    height: 30px;
    padding: 5px 10px;
    background: #02C302;
    align-items: center;
    -webkit-box-pack: space-between;
        -ms-flex-pack: space-between;
            justify-content: center;
    .finish{
      justify-self: end
    }
  }
  .container {
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-orient: vertical;
    -webkit-box-direction: normal;
        -ms-flex-direction: column;
            flex-direction: column;
    -webkit-box-pack: center;
        -ms-flex-pack: center;
            justify-content: center;
    -webkit-box-align: center;
        -ms-flex-align: center;
            align-items: center;
    width: 100%;
    -webkit-display: flex;
    -moz-display: flex;
    .question {
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex;
      -webkit-display: flex;
      -moz-display: flex;
      -webkit-box-orient: vertical;
      -webkit-box-direction: normal;
          -ms-flex-direction: column;
              flex-direction: column;
      padding: 10px 0px;
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
        -ms-grid-column-align: center;
            justify-self: center;
        border: 1px solid #C4C4C4;
        border-radius: 10px;
        background: #f1f1f1;
      }
      .success {
        background: #02C302;
      }
    }
  }
}
.settings{
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-display: flex;
  -moz-display: flex;
  -webkit-box-align: center;
      -ms-flex-align: center;
          align-items: center;
  -webkit-box-pack: center;
      -ms-flex-pack: center;
          justify-content: center;
  font-size: 1.2em;
}
.settings button{
  border: 1px solid gray
}
.back-button{
  margin-right: 10px;
  color: #02C302;
  padding: 5px;
}
.next-button{
  margin-left: 10px;
  color: #02C302;
  padding: 5px;
}
select{
  width: 100%;
  height: 30px;
  padding: 2px 5px;
  border: 1px solid #C4C4C4;
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
  padding: 8px 5px;
  color: #000;
}
.modal{
  display: none;
  position: absolute;
  top: 40%;
  width: 70%;
  height: 100px;
  border: 1px solid #000;
  left: 50%;
  -webkit-transform: translate(-50%, -90%);
      -ms-transform: translate(-50%, -90%);
          transform: translate(-50%, -90%);
  z-index: 10;
  padding: 20px;
  background: #fff;
  color:#000;
  font-size: 1.1em;
}
.modalBtnDiv{
  position: absolute;
  bottom: 20px;
  right: 20px;
  margin-top: 20px;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
}
.modalBtn{
  padding: 5px;
  border: 1px solid gray;
  color: #fff;
  width: 40px;
  text-align: center;
}
.modalBtn:first-child{
  background: #02C302;
  margin-right: 15px;
  
}
.modalBtn:last-child{
  background: red;

}
</style>

