<template>
  <ScoreBoardComponent 
    :winCount="this.winCount"
    :loseCount="this.loseCount"
  />

  <div>
    <template v-if="this.question">
      <h1 v-html="this.question"></h1>
  
      <template v-for="(answer, index) in this.answers" :key="index">
        <input :disabled="this.answerSubmitted" type="radio" name="options" :value="answer" v-model="this.chosenAnswer">
        <label v-html="answer"></label><br/>
      </template>
  
      <button v-if="!this.answerSubmitted" type="button" class="send" @click="submitAnswer()">Send</button>

      <section v-if="this.answerSubmitted" class="result">
        <h4 v-if="this.chosenAnswer == this.correctAnswer" v-html="'&#9989; Congratulations, the answer ' + this.correctAnswer + 'is correct.'"></h4>
        <h4 v-else v-html="'&#10060; Im sorry, you picked the wrond answer. The correct is' + this.correctAnswer + '.'"></h4>

        <button type="button" class="send" @click="this.getNewQuestion()">Next Question</button>
      </section>
    </template>
  </div>
</template>

<script>
import ScoreBoardComponent from './components/ScoreBoardComponent';

export default {
  name: 'App',
  components: {
    ScoreBoardComponent
  },
  data() {
    return {
      question: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      chosenAnswer: undefined,
      answerSubmitted: false,
      winCount: 0,
      loseCount: 0
    }
  },
  computed: {
    answers() {
      var answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice(Math.round(Math.random() * answers.length), 0, this.correctAnswer);

      return answers;
    }
  },
  methods: {
    submitAnswer() {
      if (!this.chosenAnswer) {
        alert('Pick one of the options.');
      } else {
        this.answerSubmitted = true;

        if (this.chosenAnswer == this.correctAnswer) {
          this.winCount++;
        } else {
          this.loseCount++;
        }
      }
    },
    getNewQuestion() {
      this.answerSubmitted = false;
      this.chosenAnswer = undefined;
      this.question = undefined;

      this.axios.get('https://opentdb.com/api.php?amount=1&category=18').then((response) => {
      this.question = response.data.results[0].question;
      this.incorrectAnswers = response.data.results[0].incorrect_answers;
      this.correctAnswer = response.data.results[0].correct_answer;
    });
    }
  },
  created() {
    this.getNewQuestion();
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Arial, Helvetica, sans-serif;
  color: #2c3e50;
  text-align: center;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio] {
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
