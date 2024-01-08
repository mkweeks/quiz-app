<template>
  <div class="main">
    <div class="timer"></div>
    <div class="container">
      <h4> Question {{ number }} / 10</h4>

      <div class="question">
        <p>{{ this.question }}</p>
      </div>
      <div class="answers">
        <button class="options" @click="checkAnswer"> {{ this.answers[this.positions[0]] }}</button>
        <button class="options" @click="checkAnswer"> {{ this.answers[this.positions[1]] }}</button>
        <button class="options" @click="checkAnswer"> {{ this.answers[this.positions[2]] }}</button>
        <button class="options" @click="checkAnswer"> {{ this.answers[this.positions[3]] }}</button>
      </div>
    </div>
    <dialog v-if="visible" class="congrats" open>
      <h1>Congrats! You scored {{ this.score }}</h1>
      <button @click="newRound">Next round!</button>
    </dialog>

  </div>
</template>

<script>
import axios from 'axios';

export default {

  data() {
    return {
      number: 1,
      questionNumber: 0,
      score: 0,
      question: null,
      answer: null,
      answers: [],
      positions: [],
      res: null,
      visible: false,
    }
  },

  methods: {
    checkAnswer(e) {
      const answerSelected = e.target.innerHTML

      if (answerSelected == this.answer) {
        this.score += 10
        this.nextQuestion()
      }
      else {
        this.nextQuestion()
      }
    },

    nextQuestion() {

      if (this.number < 10) {
        this.answers = []

        this.number += 1
        this.questionNumber += 1
        this.question = this.res.data[this.questionNumber].question.text
        this.answer = this.res.data[this.questionNumber].correctAnswer

        for (let i = 0; i < this.res.data[this.questionNumber].incorrectAnswers.length; i++) {
          this.answers.push(this.res.data[this.questionNumber].incorrectAnswers[i])
        }

        this.answers.push(this.res.data[this.questionNumber].correctAnswer)

        this.randomiseAnswerPosition()
      }

      else {
        this.visible = true
      }

    },

    randomiseAnswerPosition() {

      let x = Math.floor(Math.random() * 4)

      this.positions.push(x)

      while (this.positions.length < 4) {
        let y = Math.floor(Math.random() * 4)

        if (this.positions.includes(y)) {
          continue
        }
        else {
          this.positions.push(y)
        }
      }
    },

    async newRound() {
      this.number = 1
      this.questionNumber = 0
      this.visible = false
      this.score = 0
      this.answers = []
      this.question = null

      this.res = await axios.get('https://the-trivia-api.com/v2/questions')

      this.question = this.res.data[0].question.text
      this.answer = this.res.data[0].correctAnswer

      for (let i = 0; i < this.res.data[0].incorrectAnswers.length; i++) {
        this.answers.push(this.res.data[0].incorrectAnswers[i])
      }

      this.answers.push(this.res.data[0].correctAnswer)

      this.randomiseAnswerPosition()
    },
  },


  async mounted() {
    this.res = await axios.get('https://the-trivia-api.com/v2/questions')

    this.question = this.res.data[0].question.text
    this.answer = this.res.data[0].correctAnswer

    for (let i = 0; i < this.res.data[0].incorrectAnswers.length; i++) {
      this.answers.push(this.res.data[0].incorrectAnswers[i])
    }

    this.answers.push(this.res.data[0].correctAnswer)

    this.randomiseAnswerPosition()
  }
}
</script>


<style>
.main {
  width: 100%;
  height: 100svh;
  background-color: rgb(32, 25, 77);
  overflow: hidden;
}

dialog {
  width: 100px;
  height: 200px;
}

.timer {
  width: 50vw;
  height: 30px;
  background-color: white;
  margin: auto;
  position: relative;
  top: 50px;
  border-radius: 75px;
}

.container {
  width: 50vw;
  height: 75vh;
  background-color: rgb(32, 25, 77);
  border: 4px;
  border-radius: 12px;
  margin: auto;
  position: relative;
  top: 12vh;
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: space-between;
}

.congrats {
  position: absolute;
  width: 450px;
  top: 50%;
  background-color: yellow;
  display: flex;
  align-items: center;
  flex-direction: column;
  justify-content: space-between;
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
}

.congrats>button {
  width: 50%;
  height: 50px;
  font-family: monospace;
  border-radius: 10px;
}

.container>h4 {
  font-family: 'Courier New', Courier, monospace;
  color: white;
  font-size: 25px;
}

.question {
  position: relative;
  width: 50vw;
  height: 15vh;
  margin: auto;
  color: white;
}

.question>p {
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  font-size: 21px;
}

.answers {
  width: 50vw;
  height: 40vh;
  position: relative;
  margin: auto;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.options {
  width: 40vw;
  height: 5vh;
  background-color: rgb(32, 25, 77);
  color: white;
  position: relative;
  margin: auto;
  border-radius: 100px;
  text-align: center;
}

.options>p {
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  font-size: 15px;
}
</style>
