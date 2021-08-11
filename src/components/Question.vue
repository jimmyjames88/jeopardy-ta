<template>
  <div :class="['question', { answered }]">
    <div v-if="!answered" class="question-cell" @click="activate">{{ value }}</div>
  </div>
</template>

<script>
export default {
  name: 'Question',
  props: {
    active: {
      type: Boolean,
      default: false
    },
    answered: {
        type: Boolean,
        default: false
    },
    answers: {
      type: Array,
      required: true
    },
    question: {
      type: String,
      required: true
    },
    value: {
      type: String,
      required: true
    }
  },
  methods: {
    activate() {
      if (this.answered) { return false; }
      this.$emit('activate');
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../scss/_variables.scss';

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
</style>