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
        }
    },
    methods: {
        chooseAnswer(i) {
            this.checkAnswer(this.question, this.answers[i])
                .then((result) => {
                    console.log('checkAnswer', result);
                })
        }
    }
}
</script>