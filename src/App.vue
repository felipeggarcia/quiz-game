<template>
	<div>
        <ScoreBoard :playerScore="this.playerScore" :computerScore="this.computerScore" />
      
        <h1 v-html="this.question"></h1>
        <div v-if="this.answers">
            <div v-for="(answer,index) in this.answers" :key="index">
            <input
            type="radio" 
            name="options"
            :disabled="this.answerSubmitted"
            :value="answer" 
            :id="'radio-'+index"
            v-model="this.chosenAnswer"
            >
            <label v-html="answer" :for="'radio-'+index"></label>
            <br>
        </div>
        <br>
		<button class="send" type="button" @click="this.submitAnswer()" v-if="!this.answerSubmitted">Send</button>
		</div>
        <section class="result" v-if="this.answerSubmitted">
            <template v-if="this.chosenAnswer == this.correctAnswer">
                <h4 v-html="'&#9989; Congratulations, the answer '+ this.correctAnswer + ' is right.'"></h4>
            </template>
            <template v-else>
                <h4 v-html="'&#10060;  I\'m sorry, you picked the wrong answer.The right answer is ' + this.correctAnswer + '.'"
                ></h4>
            </template>
            <button class="send" type="button" @click="this.nextQuestion()">Next question</button>
        </section>
	</div>
</template>


<script>
import ScoreBoard from '@/components/ScoreBoard.vue'

export default{
    name: "App",
    componets: { ScoreBoard },
    data() {
        return {
            question: undefined,
            incorrectAnswer: undefined,
            correctAnswer: undefined,
            chosenAnswer: undefined,
            answerSubmitted: false,
            playerScore: 0,
            computerScore: 0
        };
    },
    computed: {
        answers() {
            if (this.incorrectAnswer) {
                var answers = JSON.parse(JSON.stringify(this.incorrectAnswer));
                answers.splice(Math.floor(Math.random() * (answers.length)), 0, this.correctAnswer);
                return answers;
            }
            return false;
        }
    },
    methods: {
        submitAnswer() {
            if (!this.chosenAnswer) {
                alert("Pick one of the options");
            }
            else {
                this.answerSubmitted = true;
                if (this.chosenAnswer == this.correctAnswer) {
                    this.playerScore++;
                }
                else {
                    this.computerScore++;
                }
            }
        },
        nextQuestion() {
            this.question = undefined;
            this.incorrectAnswer = undefined;
            this.correctAnswer = undefined;
            this.chosenAnswer = undefined;
            this.answerSubmitted = false;
            this.axios.get("https://opentdb.com/api.php?amount=1&type=multiple")
                .then((res) => {
                this.question = res.data.results[0].question;
                this.incorrectAnswer = res.data.results[0].incorrect_answers;
                this.correctAnswer = res.data.results[0].correct_answer;
            })
                .catch((error) => {
                console.warn(error);
            });
        }
    },
    created() {
        this.axios.get("https://opentdb.com/api.php?amount=1&type=multiple")
            .then((res) => {
            this.question = res.data.results[0].question;
            this.incorrectAnswer = res.data.results[0].incorrect_answers;
            this.correctAnswer = res.data.results[0].correct_answer;
        })
            .catch((error) => {
            console.warn(error);
        });
    },
    components: { ScoreBoard }
}
</script>

<style lang="scss">
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin: 60px auto;
    max-width: 960px;
    input[type=radio]{
        margin: 12px 4px;
    }
	button.send{
        box-shadow: 3px 4px 0px 0px #1564ad;
        background:linear-gradient(to bottom, #79bbff 5%, #378de5 100%);
        background-color:#79bbff;
        border-radius:5px;
        border:1px solid #337bc4;
        display:inline-block;
        cursor:pointer;
        color:#ffffff;
        font-family:Arial;
        font-size:17px;
        font-weight:bold;
        padding:12px 44px;
        text-decoration:none;
        text-shadow:0px 1px 0px #528ecc;
	}
    button.send:hover {
        background:linear-gradient(to bottom, #378de5 5%, #79bbff 100%);
        background-color:#378de5;
    }
    button.send:active {
        position:relative;
        top:1px;
    }

    label{
        -webkit-touch-callout: none; 
        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none; 
        -ms-user-select: none;
        user-select: none; 
    }
    hr{
        width:100%
    }
}
</style>
