<template>
    <div>
        <header>
            <div>Жауаптары</div> 
            <div @click="next">Көру</div>
        </header>
        <div v-if="$route.query.type == 1">
            <div class="sub_name">
                <span>{{sub1}}</span> 
                <div class="my_anwer">{{answer[0]}}/{{count[0]}}</div>
            </div>
        </div>
        <div v-else>
            <div class="sub_name">
                <span>Математикалық сауаттылық</span> 
                <div class="my_anwer">{{answer[0]}}/{{count[0]}}</div>
            </div>
            <div class="sub_name">
                <span>Қазақстан тарихы</span> 
                <div class="my_anwer">{{answer[1]}}/{{count[1]}}</div>
            </div>
            <div class="sub_name">
                <span>Оқу сауаттылығы</span> 
                <div class="my_anwer">{{answer[2]}}/{{count[2]}}</div>
            </div>
            <div class="sub_name sub_name34">
                <span>{{sub1}}</span> <div class="my_anwer">{{answer[3]}}/{{count[3]}}</div>
            </div>
            <div class="sub_name sub_name34">
                <span>{{sub2}}</span> 
                <div class="my_anwer">{{answer[4]}}/{{count[4]}}</div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios"

export default {
    data(){
        return{
            sub1: '',
            sub2: '',
            questions: [],
            type: 1,
            answer: [],
            count: []
        }
    },
    mounted(){
        this.questions = this.$route.params.questions
        this.sub1 = this.$route.query.sub1
        this.sub2 = this.$route.query.sub2
        this.type = this.$route.query.type
        if (this.type == 1){
            if (this.questions[0].subject == 1 || this.questions[0].subject == 2 || this.questions[0].subject == 3){
                this.count = [20]
            }
            else{
                this.count = [40]
            }
        }else{
            this.count = [20,20,20,40,40]
        }
        console.log(this.type, this.sub1)
        this.cheched()
    },
    methods:{
        cheched(){
            if (this.type == 1){
                let answerr = 0
                for (let i in this.questions){
                    let l = 0
                    let cnt = 0
                    for (let s in this.questions[i].question_variant){
                        if (this.questions[i].question_variant[s].is_right){
                            if (this.questions[i].question_variant.length <= 5){
                                if (this.questions[i].selected_id[0] == this.questions[i].question_variant[s].id){
                                    answerr++
                                }
                            }else{
                                cnt ++
                                if (this.questions[i].selected_id.includes(this.questions[i].question_variant[s].id)){
                                    l++
                                }
                            }
                        }
                    }
                    if (cnt != 0){
                        if (cnt == l){
                            answerr += 2
                        }
                        if (cnt == 3 && l == 2){
                            answerr ++
                        }
                        if (cnt == 2 && l == 1){
                            answerr ++ 
                        }
                    }
                }
                this.answer.push(answerr)
            }
            else{
                for (let i in this.questions){
                    let answerr = 0
                    for (let j in this.questions[i]){
                        let l = 0
                        let cnt = 0
                        for (let s in this.questions[i][j].question_variant){
                            if (this.questions[i][j].question_variant[s].is_right){
                                if (this.questions[i][j].question_variant.length == 5){
                                    if (this.questions[i][j].selected_id[0] == this.questions[i][j].question_variant[s].id){
                                        answerr++
                                    }
                                }else{
                                    cnt ++
                                    if (this.questions[i][j].selected_id.includes(this.questions[i][j].question_variant[s].id)){
                                        l++
                                    }
                                }
                            }
                        }
                        if (cnt != 0){
                            if (cnt == l){
                                answerr += 2
                            }
                            if (cnt == 3 && l == 2){
                                answerr ++
                            }
                            if (cnt == 2 && l == 1){
                                answerr ++ 
                            }
                        }
                    }
                    this.answer.push(answerr)
                }
            }
            this.finish()
        },
        next(){
            const summ = this.answer.reduce(function(acc, val) { return acc + val; }, 0)
            this.$router.push({name: "Answers", params: {'questions': this.questions}, 
                        query: {'sub1': this.sub1, "sub2": this.sub2, 'key': this.$route.query.key,
                                'type': this.$route.query.type, 'right': summ,
                                "sub1_id": this.$route.query.sub1_id, "sub2_id": this.$route.query.sub2_id}})
        },
        finish(){
            const summ = this.answer.reduce(function(acc, val) { return acc + val; }, 0)
            let headers = {
                'Authorization': 'Token ' + this.$route.query.key
            }
            let data = {
                "right_answers": summ,
            }
            if (this.$route.query.type == 1){
                data['subject1'] = this.$route.query.sub1_id
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
        }
    }
}
</script>

<style scoped>
header{
    background: #f1f1f1;
    padding: 10px 10px;
    font-size: 1.5em;
    font-weight: bold;
    margin-bottom: 5%;
    display: flex;
    -webkit-display: flex;
    -moz-display: flex;
    align-items: center;
    justify-content: space-between;
}

.sub_name{
    width: 90%;
    margin: 5px auto;
    border-radius: 5px;
    background: #A0E8AF;
    position: relative;
    font-size: 1.1em;
    font-weight: bold;
    padding: 15px 5px;
    overflow: hidden;
    height: 45px;
    display: flex;
    align-items: center;
}
.sub_name34{
    background: #84DCC6;
}
.sub_name span{
    display: flex;
    width: 80%;
    color: #505050;
}

.my_anwer{
    position: absolute;
    top: 0;
    right: 0;
    width: 45px;
    height: calc(100% - 10px);
    font-size: 1.1em;
    background: #F3E9D2;
    color: #020000;
    padding: 5px 15px;
    display: flex;
    -webkit-display: flex;
    -moz-display: flex;
    justify-content: center;
    align-items: center;
    border-top-left-radius: 50%;
    border-bottom-left-radius: 50%;
}
.sub_name34 .my_anwer{
    background: #FF686B;
}
.info{
    display: flex;
    width: 90%;
    margin: 5px auto;
    align-items: center;
    font-size: 1.1em;
}
.circle{
    width: 20px;
    height: 20px;
    margin-right: 10px;
    border-radius: 100%;
    background: #F3E9D2;
}
.red{
    background: #FF686B;
}
</style>