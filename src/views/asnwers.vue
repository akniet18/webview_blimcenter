<template>
    <div class="home">
        <div class="header">
            <div> </div>
            <div style="font-weight: bold; justify-self: center">Тестілеу</div>
            <div class="finish" @click="finish">
                Аяқтау
            </div>
        </div>
        <div v-if="$route.query.type == 1">
            <div>
                <div class="subname">{{sub1}}</div>
            </div>
            <div class="container">
                <div class="question">
                    <template >
                        <h4>{{ questions[questionId].text }}</h4>
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
                    <div class="answers" v-for="(v, index) in questions[questionId].question_variant" :key="index+'v'">
                        <button  :id="`button`+v.id"
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
                <div class="question">
                    <template >
                        <h4>{{ questions[select-1][questionId].text }}</h4>
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
                    <div class="answers" v-for="(v, index) in questions[select-1][questionId].question_variant" :key="index+'v'">
                        <button  :id="`button`+v.id"
                                :class="[v.is_right ? 'active' : '', 'variant-button']">
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
            Android.showToast("done");
            window.webkit.messageHandlers.iosListener.postMessage('Finish')
        }
    }
}
</script>