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
    data() {
        return {
            answered: false,
        }
    },
    methods: {
        activate() {
            if (this.answered) { return false; }
            this.answered = true;
            this.$emit('activate');
        }
    }
}
</script>

<style lang="scss" scoped>
    // .question {
    //     .answered {
    //         opacity: 0
    //     }
    // }
</style>