<template>
    <div>
        <div class="header">
            <button class="back-button" @click="backQuestion">
                Артка
            </button>
            <template>
                <button class="next-button" @click="nextQuestion">
                Алга
                </button>
            </template>
            <button class="back-button" @click="finish">
                Аяқтау
            </button>
        </div>
        <div v-if="$route.query.type == 1">
            <div>
                <div class="subname">{{sub1}}</div>
            </div>
            <div class="container">
                <div class="question" v-for="(i, ind) in questions" :key="ind">
                <template v-if="ind === questionId">
                    <h4>{{ i.text }}</h4>
                    
                    <img v-for="im in i.question_photo" :src="im.photo" :key="im.photo">
                    <div class="answers" v-for="(v, index) in i.question_variant" :key="index+'v'">
                        <button  :id="`button`+v.id" @click="touchButton(ind, v.id)"
                                :class="[v.is_right ? 'active' : '', 'variant-button']">
                            {{ v.text }}
                        </button>
                    </div>
                </template>
                </div>
            </div>
        </div>
        <div v-else>
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
    </div>
</template>

<script>
import axios from "axios"
export default {
    data(){
        return{
            questions: [],
            sub1: '',
            sub2: '',
            select: 1,
            questionId: 0
        }
    },
    mounted(){
        this.questions = this.$route.params.questions
        this.sub1 = this.$route.query.sub1
        this.sub2 = this.$route.query.sub2
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
        finish(){
            let headers = {
                'Authorization': 'Token ' + this.$route.query.key
            }
            let data = {
                "right_answers": this.$route.query.right,
            }
            if (this.$route.query.type == 1){
                data['subject1'] = this.sub1
            }else{
                data['subject1'] = this.$route.query.sub1_id
                data['subject2'] = this.$route.query.sub2_id
            }
            console.log(data)
            axios.post(
                "http://back.bilimcentre.kz/users/history",
                data,
                {headers}
            ).then(r=>{
                console.log(r)
            }, r=> {
                console.log(r)
            })
            window.webkit.messageHandlers.iosListener.postMessage('Finish')
            Android.showToast("done");
        }
    }
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
}
.header {
display: flex;
padding: 5px 20px;
}
.header button {
    width: 100px;
    height: 30px;
    font-size: 14px;
    font-weight: 600;
    text-transform: uppercase;
}
  
.container {
display: flex;
flex-direction: column;
justify-content: center;
align-items: center;
width: 100%;
}
.question {
display: flex;
flex-direction: column;
padding: 20px 0px;
position: absolute;
top: 60px;
width: 90%;
}
.question h4 {
margin-bottom: 5px;
}
.question img {
max-width: 300px;
max-height: 400px;
margin-bottom: 20px;
}
.variant-button {
width: 100%;
height: 35px;
text-align: left;
padding: 5px 10px;
margin: 5px 5px 5px 0;
border: 1px solid #929292;
border-radius: 10px;
background: #f1f1f1;
}
.success {
background: #4fb34f;
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