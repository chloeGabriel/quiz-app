<template>
    <div class="question-box-container mt-5">
	<b-alert dismissible :show="alertShow" variant="danger">{{ errMsg }}</b-alert> 
        <b-jumbotron>
            <template slot="lead">
                {{ decoder(currentQuestion.question) }}
            </template>
            <hr class="my-4">
            <b-list-group>
                <b-list-group-item
                    v-for="(answer, index) in shuffledAnswers"
                    :key="index"
                    @click.prevent="selectAnswer(index)"
                    :class="answerClass(index)"
                >
                    {{ decoder(answer) }}
                </b-list-group-item>
            </b-list-group>
            <b-button
                variant="primary"
                @click="submitAnswer"
                :disabled="selectedIndex === null || answered"
            >
                Submit
            </b-button>
            <b-button @click="selectedIndex === null ? showAlert : next" variant="success" >Next Question</b-button>
        </b-jumbotron>
    </div>
</template>

<script>
import _ from 'lodash'
export default {
    props: {
        currentQuestion: Object,
        next: Function,
        increment: Function,
    },
    data(){
        return {
            selectedIndex: null,
            correctIndex: null,
            shuffledAnswers: [],
            answered: false,
            errMsg: 'You must select an answer to continue the quiz.',
            alertShow: false
        }
    },
    watch: {
        currentQuestion: {
            immediate: true,
            handler(){
                this.selectedIndex = null
                this.answered = false
                this.shuffleAnswers()
            }
        }
    },
    methods: {
        // eslint-disable-next-line vue/no-dupe-keys
        showAlert() {
        this.alertShow = !this.alertShow
        },
        selectAnswer(index) {
            this.selectedIndex = index
        },
        submitAnswer() {
            let isCorrect = false
            if(this.selectedIndex === this.correctIndex){
                isCorrect = true
            }
            this.answered = true
            this.increment(isCorrect)
        },
        shuffleAnswers() {
            let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
            this.shuffledAnswers = _.shuffle(answers)
            this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },
        answerClass(index) {
            let answerClass = ''

            if(!this.answered && this.selectedIndex === index) {
                answerClass = 'selected'
            } else if (this.answered && this.correctIndex === index) {
                answerClass = 'correct'
            } else if(this.answered && this.selectedIndex === index && this.correctIndex !==index) {
                answerClass = 'incorrect'
            }
            return answerClass
        },
        decoder (str) {
            var textArea = document.createElement('textarea')
            textArea.innerHTML = str
            return textArea.value
        }
    },

}
</script>

<style scoped>
    .list-group {
        margin-bottom: 20px;
    }
    .list-group-item:hover {
        background: #EEE;
        cursor: pointer;
    }
    .btn {
        margin: 0 5px;
    }
    .selected {
        background-color: lightblue;
    }
    .correct {
        background-color: lightgreen;
    }
    .incorrect {
        background-color: lightcoral;
    }

</style>