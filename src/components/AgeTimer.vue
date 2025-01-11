<script setup>
import { ref, reactive, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  date: { type: String, required: false },
  updateInterval: { type: Number, required: false, default: 100 }
})

const isVisible = ref(true);
const toggleVisibility = () => {
  isVisible.value = !isVisible.value;
};

const date = new Date
if (!props.date && localStorage.birthday) {
  date.setTime(parseInt(localStorage.birthday))
} else {
  date.setTime((new Date(props.date)).getTime())
}

if (!isNaN(date)) {
  toggleVisibility()
}

const birthday = reactive(date)


const nullTimer = {
  year: 0,
  ms: 0,
}

const timer = reactive(nullTimer)
const formData = ref({})

const saveBirthday = () => {
  if (!formData.value.birthday?.length) {
    return
  }

  const date = new Date(formData.value.birthday)
  localStorage.birthday = date.getTime()
  birthday.setTime(date.getTime())
  toggleVisibility()
}

const updateDiff = () => {
  const now = new Date()
  const duration = now - birthday

  if (duration <= 0) {
    Object.assign(timer, nullTimer)
    return
  }

  const years = duration / 31556900000
  const majorMinor = years.toFixed(9).toString().split('.');

  Object.assign(timer, {
    year: majorMinor[0],
    ms: majorMinor[1],
  })
}

const timerID = 0

onMounted(() => {  
  const timerID = setInterval(updateDiff, props.updateInterval)
  updateDiff()
})

onUnmounted(() => {
  clearInterval(timerID)
})
</script>

<template>
  <div class="timer-container">
    <div style="display: block;" v-if="isVisible">
      <h1 class="age-label">When were you born?</h1>
      <form @submit.prevent="saveBirthday">
        <input type="date" v-model="formData.birthday" required>
        <button type="submit">Motivate</button>
      </form>
    </div>
    <div class="timer" v-else>
      <h1 class="age-label">AGE</h1>
      <h2 class="count">{{ timer.year }}<sup>.{{ timer.ms }}</sup></h2>
    </div>
  </div>
</template>

<style scoped>
.timer-container {
  -moz-osx-font-smoothing: grayscale;
  -webkit-align-items: center;
  -webkit-flex-direction: column;
  -webkit-font-smoothing: antialiased;
  -webkit-justify-content: center;
}

.timer {
  display: block;
}

.age-label {
  color: #B0B5B9;
  font-size: 1.2rem;
  line-height: 1;
  margin: 0 0 0 2px;
}

.count {
  color: #494949;
  margin: 0;
  font-size: 6rem;
  line-height: 1;
  font-weight: 600;
}

.count sup {
  font-size: 2.4rem;
  margin-left: 7px;
}

input, button {
  padding: 0.375rem 0.75rem;
  font-size: 1.5rem;
  appearance: none;
  -webkit-appearance: none;
}

input {
  margin-right: 0.5rem;
  box-sizing: border-box;
  border-width: 1px;
  border-style: solid;
  border-radius: 0.25rem;
  /* border-color: #CCC; */
  /* background-color: #FFF; */
}

button {
  display: block;
  cursor: pointer;
  border: none;
  border-radius: 0.25rem;
  background-color: transparent;
  outline: #0BE 0.1rem solid;
}
form {
  padding-top: 0.5rem;
  display: -webkit-flex;
  -webkit-flex-direction: row;
  -webkit-justify-content: center;
}
</style>
