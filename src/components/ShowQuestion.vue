<template>
  <div id="showQuestion">
  <p>{{ question }}</p>
  <hr />
  <ul class="answers">
    <li v-for="(answer, i) in randomizedAnswers" :key="`a-${i}`" @click="chooseAnswer(i)">
      {{ answer }}
    </li>
  </ul>
  </div>
</template>

<script>
import JeopardyGame from '../mixins/Jeopardy';

export default {
  name: 'ShowQuestion',
  mixins: [JeopardyGame],
  props: {
    answers: {
      type: Array,
      required: true,
    },
    question: {
      type: String,
      required: true,
    },
    value: {
      type: String,
      required: true
    }
  },
  computed: {
    nvalue() {
      return parseInt(this.value.replace('$', ''), 10);
    },
    randomizedAnswers() {
        const answers = this.answers;
        // https://stackoverflow.com/a/12646864/2312657
        for (let i = answers.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [answers[i], answers[j]] = [answers[j], answers[i]];
        }
        return answers;
    }
  },
  methods: {
    chooseAnswer(i) {
      this.checkAnswer(this.question, this.answers[i])
        .then((result) => {
          this.$emit('outcome', { result, value: this.nvalue });
        })
    }
  }
}
</script>

<style lang="scss">
@import '../scss/_variables.scss';

#showQuestion {
  min-height: 100%;
  text-align: center;
  font-family: 'Merriweather', serif;
  font-weight: 700;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  p {
    font-size: 2.5rem;
    text-transform: uppercase;
  }

  hr {
    margin-top: 2rem;
    margin-bottom: 2rem;
    width: 100%;
  }

  ul.answers {
    font-size: 1.5rem;

    li {
        padding: 1rem;
        cursor: pointer;

        &:hover {
            background-color: rgba(255, 255, 255, 0.2);
        }
    }
  }
}
</style>