<script setup>
import { ref, reactive, onMounted, onUnmounted } from 'vue'

const isVisible = ref(true);
const toggleVisibility = () => {
  isVisible.value = !isVisible.value;
};

const birthday = ref('')

if (localStorage.birthday) {
  birthday.value = localStorage.birthday

  if (birthday.value.length > 0) {
    toggleVisibility()
  }
}

const timer = reactive({year: 0, ms: 0})

const saveBirthday = () => {
  if (!birthday.value.length) {
    return
  }

  const date = new Date(birthday.value)
  if (!date || isNaN(date)) {
    return
  }

  localStorage.birthday = birthday.value

  updateDiff()
  toggleVisibility()
}

const updateDiff = () => {
  const now = new Date()
  const fromDate = new Date(birthday.value)
  const duration = now - fromDate

  if (duration <= 0) {
    Object.assign(timer, {year: 0, ms: 0})
    return
  }

  const years = duration / 31556900000
  const majorMinor = years.toFixed(9).toString().split('.');

  Object.assign(timer, {
    year: majorMinor[0],
    ms: majorMinor[1],
  })
}

let intervalId

onMounted(() => {  
  intervalId = setInterval(updateDiff, 100)
  updateDiff()
})

onUnmounted(() => {
  clearInterval(intervalId)
})
</script>

<template>
  <div class="timer-container">
    <div v-if="isVisible">
      <h1 class="form-label">When were you born?</h1>
      <form @submit.prevent="saveBirthday">
        <input type="date" v-model="birthday" required>
        <button type="submit">Motivate</button>
      </form>
    </div>
    <div class="timer" v-else>
      <div class="age-label">
        <h1>AGE</h1>
        <div @click="toggleVisibility">
          <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512"><path fill="currentColor" d="M471.6 21.7c-21.9-21.9-57.3-21.9-79.2 0L362.3 51.7l97.9 97.9 30.1-30.1c21.9-21.9 21.9-57.3 0-79.2L471.6 21.7zm-299.2 220c-6.1 6.1-10.8 13.6-13.5 21.9l-29.6 88.8c-2.9 8.6-.6 18.1 5.8 24.6s15.9 8.7 24.6 5.8l88.8-29.6c8.2-2.7 15.7-7.4 21.9-13.5L437.7 172.3 339.7 74.3 172.4 241.7zM96 64C43 64 0 107 0 160V416c0 53 43 96 96 96H352c53 0 96-43 96-96V320c0-17.7-14.3-32-32-32s-32 14.3-32 32v96c0 17.7-14.3 32-32 32H96c-17.7 0-32-14.3-32-32V160c0-17.7 14.3-32 32-32h96c17.7 0 32-14.3 32-32s-14.3-32-32-32H96z"/></svg>
        </div>
      </div>
      <h2 class="count">{{ timer.year }}<sup>.{{ timer.ms }}</sup></h2>
    </div>
  </div>
</template>

<style scoped>
.timer-container {
  --text-color: #B0B5B9;
  --counter-color: #494949;
  --button-color: #0BE;
}

.form-label {
  color: var(--color);
}

.age-label {
  position: relative;
  z-index: 10;
  display: flex;
  align-items: center;
}

.age-label h1 {
  margin: 0;
  color: var(--text-color);
  font-size: 1.2rem;
  line-height: 1;
}

.age-label svg {
  width: 1rem;
  height: 1rem;
  margin-left: 0.5rem;
  color: var(--text-color);

  cursor: pointer;
  opacity: 0;
  transition: .5s ease;
}

.age-label:hover svg {
    opacity: 1;
}

.count {
  color: var(--counter-color);
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
}

button {
  display: block;
  cursor: pointer;
  border: none;
  border-radius: 0.25rem;
  background-color: transparent;
  outline: var(--button-color) 0.1rem solid;
}
form {
  padding-top: 0.5rem;
  display: flex;
}
</style>
