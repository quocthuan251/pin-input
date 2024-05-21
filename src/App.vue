<template>
  <!-- <header>
    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />

      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header> -->

  <!-- <RouterView /> -->
  <div class="flex flex-col items-center gap-4">
    <h2 class="text-2xl font-bold">PIN Input</h2>
    <div class="flex">
      <input
        v-for="(_, index) in length"
        :key="index"
        ref="inputs"
        v-model="pin[index]"
        @input="handleInput($event, index)"
        @focus="handleFocus(index)"
        @keydown.delete="handleDelete(index)"
        @paste="handlePaste($event, index)"
        :type="isSecretMode ? 'password' : 'text'"
        maxlength="1"
        class="w-10 h-10 text-center border border-gray-300 rounded mr-1 focus:outline-none text-gray-950"
        :class="{ mask: isSecretMode }"
        :style="{ caretColor: isSecretMode ? 'transparent' : 'auto' }"
        autocomplete="one-time-code"
        autofocus
        :disabled="isSecretMode && pin[index]"
      />
    </div>
    <div class="flex items-center gap-2">
      <input
        type="checkbox"
        v-model="isSecretMode"
        class="w-4 h-4 border border-gray-300 rounded-md cursor-pointer focus:ring-2 focus:ring-blue-500"
      />
      <label for="secretMode" class="text-sm font-medium">Chế độ bí mật</label>
    </div>
    <button @click="onSubmit">Submit</button>
  </div>
</template>
<script setup>
import { ref } from 'vue'
const pin = ref(Array(4).fill(''))
const inputs = ref([])
const length = ref(4)
const isSecretMode = ref(false)

const handleInput = (event, index) => {
  const input = event.target
  const value = input.value.replace(/[^0-9]/g, '')
  pin.value[index] = value

  if (value.length === 1 && index < pin.value.length - 1) {
    inputs.value[index + 1].focus()
  }
}

const handleFocus = (index) => {
  if (index > 0) {
    inputs.value[index - 1].blur()
  }
}

const handleDelete = (index) => {
  if (index > 0 && pin.value[index] === '') {
    inputs.value[index - 1].focus()
  }
}

const handlePaste = (event, index) => {
  event.preventDefault()
  const pasteText = event.clipboardData?.getData('text') || ''
  const digits = pasteText.replace(/[^0-9]/g, '')

  if (digits.length > 0) {
    pin.value.splice(index, digits.length, ...digits.split(''))
    inputs.value[index + digits.length - 1].focus()
  }
}
const onSubmit = () => {
  console.log('PIN submitted:', pin.value.join(''))
}
</script>
<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;

    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
