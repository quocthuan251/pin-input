<template>
  <div>
    <div>số ô input:</div>
    <input
      @input="changeLengthInput"
      class="text-center border border-gray-300 rounded mr-1 focus:outline-none text-gray-950"
      type="number"
      min="1"
      max="100"
    />
  </div>
  <div class="flex flex-col items-center gap-4 h-[100%] justify-center">
    <h2 class="text-2xl font-bold">PIN Input</h2>
    <div class="flex flex-wrap" v-if="pin.length">
      <input
        v-for="(_, index) in inputLength"
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
        :disabled="index > pin.join('').length"
      />
    </div>
    <div class="flex items-center gap-2">
      <input
        type="checkbox"
        v-model="isSecretMode"
        class="w-4 h-4 border border-gray-300 rounded-md cursor-pointer focus:ring-2 focus:ring-blue-500"
      />
      <label for="secretMode" class="text-sm font-medium">Secret mode</label>
    </div>
    <div class="flex items-center" v-if="isInputSuccess">
      <div
        class="bg-green-200 px-6 py-4 mx-2 my-4 rounded-md text-lg flex items-center mx-auto max-w-lg"
      >
        <svg viewBox="0 0 24 24" class="text-green-600 w-5 h-5 sm:w-5 sm:h-5 mr-3">
          <path
            fill="currentColor"
            d="M12,0A12,12,0,1,0,24,12,12.014,12.014,0,0,0,12,0Zm6.927,8.2-6.845,9.289a1.011,1.011,0,0,1-1.43.188L5.764,13.769a1,1,0,1,1,1.25-1.562l4.076,3.261,6.227-8.451A1,1,0,1,1,18.927,8.2Z"
          ></path>
        </svg>
        <span class="text-green-800">Success! </span>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, watch } from 'vue'
const inputLength = ref(5)
const pin = ref(Array(inputLength.value).fill(''))
const inputs = ref([])
const isSecretMode = ref(false)
const isInputSuccess = ref(false)
const regex = ref(/[^0-9]/g)
// const regex = ref(/[^a-zA-Z]/g)

const handleInput = (event, index) => {
  const input = event.target
  const value = input.value.replace(regex.value, '')
  pin.value[index] = value
  if (value.length === 1 && index < pin.value.length - 1) {
    inputs.value[index + 1].focus()
  }
}
const changeLengthInput = (event) => {
  if (event.target.value) {
    pin.value = Array(Number(event.target.value)).fill('')
    inputLength.value = Number(event.target.value)
  }
}

const handleFocus = (index) => {
  if (index > 0) {
    inputs.value[index - 1].blur()
  }
}

const handleDelete = (index) => {
  if (index > 0 && !pin.value[index]) {
    inputs.value[index - 1].focus()
  }
  for (let idx = index; idx < pin.value.length; idx++) {
    pin.value[idx] = ''
  }
}

const handlePaste = (event, index) => {
  event.preventDefault()
  const pasteText = event.clipboardData?.getData('text') || ''
  const digits = pasteText.replace(regex.value, '')

  if (digits.length > 0) {
    console.log('🚀 ~ handlePaste ~ digits.length:', digits.length)
    pin.value.splice(index, digits.length, ...digits.split(''))
    inputs.value[index + digits.length - 1].focus()
  }
}

watch(
  () => pin,
  () => {
    if (inputLength.value && pin.value && pin.value?.join('').length == inputLength.value) {
      console.log('PIN submitted:', pin.value.join(''))
      isInputSuccess.value = true
    } else {
      isInputSuccess.value = false
    }
  },
  { deep: true }
)
</script>
<style scoped></style>
