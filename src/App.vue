<template>
  <div class="gameScore" >
    Player
    <strong id="pScore" class="score">{{score.P}}</strong>
    X
    <strong id="pScore" class="score">{{score.C}}</strong>
    Computers
  </div>
  <hr>
  <div >
    <template v-if="this.questions">
      <h1 v-html="questions"></h1>
      <template v-for="answer in this.answers" :key="answer">
        <input type="radio" name="option" :value="answer" v-model="selectedAnswer" :disabled="submitted"> 
        <label v-html="answer"></label><br>
      </template>
      <button @click="checkAnswer()"  id="submit" class="button" v-if="!this.submitted">Submit</button>
      <span id="result" :class="(this.correctAnswer == this.selectedAnswer) ? 'correct' : 'wrong'">{{ result }}</span>
      <button @click="nextQuestion()" id="next" class="button" v-if="this.submitted">Next Question</button>
    </template>
  </div>

</template>

<script>

import axios from 'axios';

export default {
  name: 'App',
  

  data() {
    return {
      questions: undefined,
      incorrectAnswers: undefined,
      correctAnswer: undefined,
      selectedAnswer: undefined,
      result: undefined,
      submitted: false,
      score: {P: 0, C: 0}
    }
  },

  created() {
    axios.get('https://opentdb.com/api.php?amount=1&difficulty=easy')
      .then((response) => {
        this.questions = response.data.results[0].question;
        this.incorrectAnswers = response.data.results[0].incorrect_answers;
        this.correctAnswer = response.data.results[0].correct_answer;
        console.log(response.data.results[0]);
    })

    if(localStorage.getItem("score")){
      this.score = JSON.parse(localStorage.getItem("score"));
    }
  },

  computed: {
    answers() {
      var answers;
      answers = JSON.parse(JSON.stringify(this.incorrectAnswers));
      answers.splice((Math.random()*answers.length).toFixed(0),0,this.correctAnswer);
      return answers;
    }
  },

  methods: {
    checkAnswer: function() {

      if (this.selectedAnswer){

        if(this.correctAnswer == this.selectedAnswer){

          this.score.P++;
          localStorage.setItem("score",JSON.stringify(this.score));
          this.result = "Hurrah!! Correct Answer";

        } else {

          this.score.C++;
          localStorage.setItem("score",JSON.stringify(this.score));
          this.result = "Oh no!! It's a Wrong Answer. Correct Answer is " + this.correctAnswer;

        }

        document.getElementById("submit").style.display = "none";
        this.submitted = true;

      } else {
        alert("Please select an Answer...")
      }
    },

    nextQuestion: function() {
      window.location.reload();
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;
}

.gameScore {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 15px;
}

.score {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid black;
  height: 35px;
  width: 25px;
  margin: auto 5px;
}

#app input[type=radio] {
  margin-top: 20px;
}

#app .button {
  height: 35px;
  width: 100px;
  color: white;
  background-color: darkblue;
  border: none;
  margin: 20px auto;
}

button[id=next] {
  display: block;
}

#result {
  display: inline-block;
  margin-top: 20px;
  font-weight: bold;
}

.wrong {
  color: darkred;
}

.correct {
  color: green;
}

</style>
