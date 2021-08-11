<template>
  <div id="showQuestion">
    <p>{{ question }}</p>
    <ul>
      <li v-for="(answer, i) in answers" :key="`a-${i}`" @click="chooseAnswer(i)">
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