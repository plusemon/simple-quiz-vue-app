<script setup>
import { computed } from '@vue/reactivity'
import { ref } from 'vue'

const questions = ref([
  {
    title: 'What is the capital of Bangladesh?',
    options: [
      'Dhaka',
      'Sylhet',
      'Rajshahi',
      'Barishal',
    ],
    correct_ans: 1,
    choosen_ans: null
  },
  {
    title: 'What is the president of Bangladesh?',
    options: [
      'Abu Taleb',
      'Saiful Islam Sabuj',
      'Safiull Islam',
      'Sheikh Hasina',
    ],
    correct_ans: 4,
    choosen_ans: null
  },
  {
    title: 'What is the national flower of Bangladesh?',
    options: [
      'Rose',
      'Sun Flower',
      'Lily',
      'Belly',
    ],
    correct_ans: 3,
    choosen_ans: null
  },
])

const quiz_status = ref('ready')
const question_no = ref(1)

const current_question = computed(() => {
  return questions.value[question_no.value - 1]
})

const correct_answares = computed(() => {
  return questions.value.filter(q => q.choosen_ans == q.correct_ans).length
})


const resetQuiz = () => {
  questions.value = questions.value.map(item => {
    item.choosen_ans = null
    return item
  })
  question_no.value = 1
  quiz_status.value = 'ready'
}

const startQuiz = () => {
  resetQuiz()
  quiz_status.value = 'started'
}

function jumpQuestion(index) {
  question_no.value = index
}

function choosenOption(option) {
  // store the answare
  current_question.value.choosen_ans = option

}

function nextQuestion() {
  if (questions.value.length <= question_no.value) {
    quiz_status.value = 'finished'
  } else {
    question_no.value++
  }
}

function prevQuestion() {
  question_no.value--
}



function isCorrectAnswared(question, index) {

  let cls = ''
  if (question.correct_ans == index) {
    cls += ' ans-right '
    if (question.correct_ans == index && question.choosen_ans == index) {
      cls += ' ans-correct '
    }
  } else if (question.choosen_ans == index) {
    cls += ' ans-wrong '
  }

  return cls

}

</script>

<template>
  <main>

    <!-- On ready state -->
    <div v-if="quiz_status == 'ready'">
      <div class="box">
        <p>Instuctions</p>
        <ul>
          <li>All questions are related to bangladesh</li>
        </ul>
      </div>
      <button @click="startQuiz()">Start Quiz</button>
    </div>

    <!-- running the quiz proccess -->
    <div v-if="quiz_status == 'started'">


      <!-- number items of questions -->
      <div class="number-box-container">
        <div v-for="(question, index) in questions" class="number-box"
          :class="[(question.choosen_ans == null) ? (index+1 == question_no ? 'active':''):'filled']"
          @click="jumpQuestion(index+1)">
          {{ index+1 }}
        </div>
      </div>

      <br>
      <div class="question">
        <p class="title">Q. {{ current_question.title }}</p>
        <ol>
          <li v-for="(option, index) in current_question.options" @click="choosenOption(index + 1)"
            :class="[current_question.choosen_ans == index + 1 ? 'active':'']">{{ option }}</li>
        </ol>
        <br>
        <div class="buttons">
          <button @click="prevQuestion()" v-if="question_no > 1">{{ question_no == questions.length ? 'Prev':'Prev'
          }}</button>
          <button @click="nextQuestion()">{{ question_no == questions.length ? 'Finish':'Next' }}</button>
        </div>
        <br>
        <p>Currently you are in {{ question_no }} of {{ questions.length }}</p>
      </div>
    </div>

    <!-- quize finished -->
    <div v-if="quiz_status == 'finished'">
      <h1>Score Board</h1>
      <p>You Score: {{correct_answares }}/{{ questions.length }}</p>
      <br>

      <div class="question" v-for="question in questions">
        <p>Q. {{ question.title }}</p>
        <ol>
          <li v-for="(option, index) in question.options" :class="[isCorrectAnswared(question, index + 1)]">{{ option }}
          </li>
        </ol>
        <p>Correct Ans: {{ question.options[question.correct_ans-1] }}</p>
      </div>

      <div>
        <button @click="startQuiz()">Play Again</button>
        <button @click="resetQuiz()">Main Again</button>
      </div>
    </div>

  </main>

</template>

<style>
.number-box-container {
  display: flex;
  gap: 10px;
}

.number-box {
  height: 30px;
  width: 30px;
  border: 1px solid rgb(63, 63, 63);
  border-radius: 20px;
  text-align: center;
  font-size: 13pt;
  font-weight: bold;
  cursor: pointer;
}


.number-box.filled {
  background-color: rgb(203, 203, 203);
  color: black;
}

.number-box.active {
  border: 1px solid rgb(205, 205, 205);
}

.buttons {
  display: flex;
}

.box {
  border: 1px solid goldenrod;
  padding: 10px;
  margin-bottom: 20px;
}

button {
  padding: 10px 20px;
  font-size: large;
  width: 100%;
  background-color: rgb(225, 203, 0);
  cursor: pointer;
  transition: 300ms;
}

button:hover {
  background-color: rgb(117, 143, 0);
}

.question {
  border: 1px solid rgb(64, 64, 64);
  padding: 10px;
}

.question .title {
  font-size: medium;
  margin-bottom: 20px;
  font-weight: bold;
}

ol {
  list-style: upper-alpha;
  padding-left: 13px;
}

ol li {
  border: 1px solid gray;
  padding: 10px;
  margin: 5px;
  cursor: pointer;
}

ol li:hover {
  background-color: rgb(183, 146, 0);
}

ol li.active {
  background-color: rgb(0, 149, 100);
}

.ans-right {
  border: 2px solid green;
}

.ans-wrong {
  border: 2px solid red;
}

.ans-correct {
  background-color: green;
}
</style>
