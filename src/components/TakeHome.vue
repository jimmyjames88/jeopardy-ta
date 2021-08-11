<template>
  <article>
    <div id="game">
      <show-question
        v-if="activeQuestion"
        v-bind="activeQuestion"
        @outcome="handleOutcome"
      />
      
      <!-- showOutcome -->
      <div id="showOutcome" v-if="outcome">
        <div v-if="outcome.result">
          CORRECT!
        </div>
        <div v-else>
          INCORRECT!
        </div>
        <button @click="outcome=null">Continue</button>
      </div>

      <!-- -->
      <div v-show="!activeQuestion && !outcome">
        <header>
          <h1 id="score">JEOPARDY</h1>
          <h2>Score: <span>${{ this.score }}</span></h2>
        </header>
        <div id="board">
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
@import url('https://fonts.googleapis.com/css2?family=Merriweather:wght@700&display=swap');
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
  background: linear-gradient(#4949a0, $color-background);
  min-height: 100vh;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 2rem;
  font-family: 'Impact', sans-serif;
  font-weight: normal;
  font-size: 2rem;

  span {
    color: $color-value;
  }
}


#game {
    color: $color-light;
    position: relative;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

#showOutcome {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  font-family: 'Merriweather';
  font-size: 4rem;

  button {
    background-color: transparent;
    font-family: 'Impact', serif;
    font-size: 2rem;
    color: $color-light;
    text-transform: uppercase;
    border: none;
    cursor: pointer;

    &:hover {
      color: $color-value;
    }
  }
}

#board {
  display: grid;
  font-family: 'Impact', sans-serif;

  .category-header,
  .question {
    height: 16.66%;
    background: linear-gradient(transparent, rgba(255, 255, 255, 0.2));
  }

  .category-header {
    color: $color-light;
    text-transform: uppercase;
    text-align: center;
    border: 6px solid $color-border;
    font-size: 1.5rem;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}

@media screen and (min-width: 991px) {
  #board {
    grid-template-columns: repeat(6, 1fr);
    height: 55vw;
    max-height: 85vh;
  }
}
</style>
