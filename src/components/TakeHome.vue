<template>
  <article>
    <h1>Jeopardy!</h1>
    <div id="game">
      <div id="showQuestion" v-if="activeQuestion">
        <p>{{ activeQuestion.question }}</p>
        <ul>
          <li v-for="(answer, i) in activeQuestion.answers" :key="`a-${i}`" @click="activeQuestion=false">
            {{ answer }}
          </li>
        </ul>
      </div>
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
  </article>
</template>

<script>
  import JeopardyGame from '../mixins/Jeopardy';
  import Question from '../components/Question';

  export default {
    name: 'TakeHome',
    mixins:[JeopardyGame],
    components: { Question },

    computed: {
      categorizedQuestions() {
        var sorted = {}
        this.questions.forEach((question) => {
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
        questions: []
      }
    },

    mounted() {
      this.fetchQuestions()
        .then((result) => {
          this.questions = result;
        })
    },

    methods: {
      showQuestion(question) {
        this.activeQuestion = question;
      }
    }
  }
</script>

<style lang="scss">
$color-light: #fff;
$color-background: #000099;
$color-border: #090909;
$color-value: #e9e97e;

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

  .question {
    border: 4px solid $color-border;

    .question-cell {
      color: $color-value;
      font-size: 4vw;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;

      &:hover {
        background-color: rgba(255, 255, 255, 0.2);
      }
    }
  }
}
</style>
