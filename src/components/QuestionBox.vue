<template>
<div class="question-box-container">
  <b-jumbotron>

    <template slot="lead">

      {{ currentQuestion.question }}
    </template>

    <hr class="my-4">


      <b-list-group>

        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>

      </b-list-group>


    <b-button variant="primary"
      @click="submitAnswer"
      :disabled="selectedIndex === null || answered"
    >
      Submit
    </b-button>
    <b-button @click="next" variant="success"
      :disabled="!submitted">Next</b-button>
  </b-jumbotron>
</div>



</template>


<script>
import _ from 'lodash'
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      answered: false,
      correctIndex: null,
      submitted: false
    }
  },

  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers]
      answers.push(this.currentQuestion.correct_answer)
      return answers
    }
  },
  watch: {
    // currentQuestion() {
    //   this.selectedIndex = null
    //   this.shuffleAnswers()
    // }

    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
        this.submitted = false
      }
    }
  },
  methods: {
    selectAnswer(index) {
    this.selectedIndex = index
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    submitAnswer() {
      let isCorrect = false
      if(this.shuffledAnswers[this.selectedIndex] === this.currentQuestion.correct_answer)
      {
        isCorrect = true
      }
      this.answered = true
      this.increment(isCorrect)
      this.submitted = true
    },
    answerClass(index) {
      // !answered && selectedIndex === index ? 'selected' :
      // answered && correctIndex === index ? 'correct' :
      // answered && selectedIndex === index && correctIndex !== index ? 'incorrect' : ''

      if(!this.answered && this.selectedIndex === index) {
        return 'selected';
      }
      else if(this.answered && this.correctIndex === index) {
        return 'correct';
      }
      else if(this.answered && this.selectedIndex === index && this.correctIndex !== index) {
        return 'incorrect';
      }
  }

  }
  // mounted() {
  //   this.shuffleAnswers()
  // }

}
</script>

<style scoped>
  .list-group {
    margin-bottom: 15px;
  }

  .list-group-item:hover {
    background: #EEE;
    cursor: pointer;
  }
  .selected {
    background-color: lightblue;
  }

  .correct {
    background-color: lightgreen;
  }

  .incorrect {
    background-color: red;
  }
  .btn {
    margin: 0 5px;
  }
</style>
