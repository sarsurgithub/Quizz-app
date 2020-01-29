<template>
    <div class='question-box-contrainer'>
        <b-jumbotron>
            <template v-slot:lead>
                {{currentQuestion.question}}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item
                v-for='(answer, index) in shuffledAnswers'
                :key='index'
                @click='selectAnswer(index)'
                :class="answerClass(index)"
                >
                {{answer}}
                </b-list-group-item>
                
            </b-list-group>

            <b-button variant="primary" 
                @click='submitAnswer'
                :disabled='selectedIndex===null || answered'
            >
                Submit</b-button>
            <b-button @click='next' variant="success" href="#">
                Next
            </b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props:{
        currentQuestion: Object,
        next: Function,
        increment: Function
    },
    data: function() {
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler(){
                this.answered = false
                this.selectedIndex = null
                this.suffleAnswers()
            }
        }
    },
    methods: {
        selectAnswer(index){
            this.selectedIndex = index
        },
        
        submitAnswer() {
            let isCorrect = false
            if (this.selectedIndex === this.correctIndex) {
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
    },
        
        suffleAnswers(){
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)

        },
        answerClass(index){
            let answerClass = ''
            if (!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if (this.answered &&
                this.selectedIndex === index &&
                this.correctIndex !== index
            ) {
                answerClass = 'incorrect'
            }
            return answerClass
        }
    }
}
</script>

<style scoped>
    .list-group{
        margin-bottom: 15px;
    }
    .list-group-item:hover {
        background: #EEE;
        cursor: pointer;
    }
    .btn{
        margin: 0 5px;
    }
    .selected{
        background-color: lightblue;
    }
    .correct {
        background-color: lightgreen;
    }
    .incorrect {
        background-color: crimson
    }
</style>