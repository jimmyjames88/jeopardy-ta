<template>
  <article>
    <h1>Score: ${{ this.score }}</h1>
    <div id="game">
      <show-question
        v-if="activeQuestion"
        v-bind="activeQuestion"
        @outcome="handleOutcome"
      />
      <div id="showOutcome" v-if="outcome">
        <div v-if="outcome.result">
          CORRECT!
        </div>
        <div v-else>
          INCORRECT!
        </div>
        <button @click="outcome=null">Continue</button>
      </div>
      <div id="board" v-show="!activeQuestion && !outcome">
        <div v-for="(category, key, catIndex) in categorizedQuestions" :key="`c-${catIndex}`" class="category-column">
          <div class="category-header">{{ key }}</div>
          <question
            v-for="(question, index) in category"
            :key="`q-${catIndex}-${index}`"
            v-bind="question"
            @activate="showQuestion(question)"
          />
        </div>
      </div>
    </div>
  </article>
</template>

<script>
  import JeopardyGame from '../mixins/Jeopardy';
  import Question from '../components/Question';
  import ShowQuestion from '../components/ShowQuestion';

  export default {
    name: 'TakeHome',
    mixins:[JeopardyGame],
    components: { Question, ShowQuestion },

    computed: {
      categorizedQuestions() {
        var sorted = {}
        this.questions.forEach((question) => {
          question['answered'] = false;
          if (sorted[question.category]) {
            sorted[question.category].push(question)
          } else {
            sorted[question.category] = [question];
          }
        })
        return sorted;
      }

    },

    data() {
      return {
        activeQuestion: false,
        outcome: null,
        questions: [],
        score: 0
      }
    },

    mounted() {
      this.fetchQuestions()
        .then((result) => {
          this.questions = result;
        })
    },

    methods: {
      addScore(value) {
        this.score += value;
      }, 
      subScore(value) {
        this.score -= value;
      },
      handleOutcome(outcome) {
        this.activeQuestion = false;
        if (outcome.result) {
          this.addScore(outcome.value);
        } else {
          this.subScore(outcome.value);
        }
        this.outcome = outcome;
      },

      showQuestion(question) {
        question.answered = true;
        this.activeQuestion = question;
      }
    }
  }
</script>

<style lang="scss">
@import '../scss/_variables.scss';

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  font-size: 12pt;
}

body {
  background-color: $color-border;
}

#game {
    background-color: $color-background;
    color: $color-light;
    position: relative;
}

#showQuestion {
  position: absolute;
  z-index: 2;
  left: 0;
  top: 0;
  bottom: 0;
  right: 0;
  background-color: $color-background;
}

#board {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  font-family: 'Impact', sans-serif;
  height: 55vw;

  .category-header,
  .question {
    height: 16.66%;
    display: flex;
    background: linear-gradient(transparent, rgba(255, 255, 255, 0.2));
  }

  .category-header {
    color: $color-light;
    text-align: center;
    text-transform: uppercase;
    border: 6px solid $color-border;
    font-size: 1.5rem;
  }
}
</style>
