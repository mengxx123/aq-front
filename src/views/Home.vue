<template>
    <my-page title="首页">
        <div class="ui-loading" v-if="loading">
            <ui-circular-progress :size="24"/>
        </div>
        <div class="question-box" v-if="question">
            <div class="title">
                <ui-icon class="icon" value="help" />
                {{ question.title }}</div>
            <ul class="option-list">
                <li class="item" :class="itemClass(index)" @click="selectOption(index)" v-for="item, index in question.options">
                    <div class="index">{{ indexText(index) }}</div>
                    <div class="text">{{ item }}</div>
                </li>
            </ul>
            <div class="btns">
                <ui-raised-button class="btn" label="查看答案" @click="viewAnswer" />
                <ui-raised-button class="btn" label="下一题" @click="nextQuestion" />
            </div>
            <!-- <div>{{ question.answer }}</div> -->
        </div>
    </my-page>
</template>

<script>
    export default {
        data () {
            return {
                loading: false,
                dataPage: 0,
                questionIndex: -1,
                question: null,
                userAnswer: -1,
                questions: [],
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/help'
                        }
                    ]
                }
            }
        },
        computed: {
        },
        mounted() {
            this.loadData()
        },
        methods: {
            loadData() {
                this.dataPage++
                this.loading = true
                this.$http.get(`/aqs?page=${this.dataPage}`).then(
                    response => {
                        let data = response.data
                        console.log(data)
                        this.questions = data
                        this.questionIndex = -1
                        this.nextQuestion()
                        this.loading = false
                    },
                    response => {
                        console.log(response)
                        this.loading = false
                    })
            },
            selectOption(index) {
                this.userAnswer = index
                if (index === this.question.answer - 1) {
                    // alert('对了')
                } else {
                    // alert('错了')
                }
            },
            nextQuestion() {
                this.userAnswer = -1
                this.questionIndex++
                if (this.questionIndex === 20) {
                    this.loadData()
                    return
                }
                this.question = this.questions[this.questionIndex]
                this.question.options = JSON.parse(this.question.list).filter(item => item)
            },
            viewAnswer() {
                window.open('https://www.baidu.com/s?wd=' + this.question.title)
            },
            indexText(index) {
                return 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.charAt(index)
            },
            itemClass(index) {
                if (this.userAnswer === -1) {
                    return []
                }
                if (index === this.question.answer - 1) {
                    return ['success']
                } else if (index === this.userAnswer) {
                    return ['error']
                }
                return []
            }
        }
    }
</script>

<style scoped>
</style>
