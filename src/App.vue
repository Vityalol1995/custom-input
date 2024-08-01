<script setup>
import { ref, onMounted } from 'vue';

const inputValue = ref('');
const hasError = ref(false);
const showNotification = ref(false);
const notificationType = ref('');
const inputRef = ref(null);

const handleInput = (event) => {
  let value = event.target.value;
  if (value[0] === '.') {
    inputValue.value = ''
    return
  }

  const filteredValue = value.replace(/[^0-9.]/g, '');

  const parts = filteredValue.split('.');
  if (parts.length > 2) {
    value = parts[0] + '.' + parts.slice(1).join('');
  } else {
    value = filteredValue;
  }

  inputValue.value = value;
  hasError.value = false;
};

const validateInput = () => {
  if (inputRef.value && inputRef.value.value !== inputValue.value) {
    inputValue.value = ''
  }

  const regex = /^\d+(\.\d{1,18})?$/;
  if (!regex.test(inputValue.value)) {
    hasError.value = true;
  } else {
    hasError.value = false;
  }
};

const handleSubmit = () => {
  validateInput();
  if (hasError.value) {
    notificationType.value = 'error';
    showNotification.value = true;
  } else {
    notificationType.value = 'success';
    showNotification.value = true;
  }
};

const restoreInputType = () => {
  if (inputRef.value) {
    if (inputRef.value.type !== 'text') {
      inputRef.value.type = 'text';
    }
  }
};

onMounted(() => {
  restoreInputType();

  const observer = new MutationObserver(() => {
    restoreInputType();
  });

  observer.observe(document.body, {
    attributes: true,
    childList: true,
    subtree: true
  });
});
</script>

<template>
  <div class="w-full h-screen flex items-center justify-center">
    <div class="p-10 bg-gray-900 rounded-2xl w-[600px]">
      <h2 class="text-2xl font-semibold leading-7 text-white text-center mb-5">Custom input</h2>

      <input
          ref="inputRef"
          type="text"
          v-model="inputValue"
          @input="handleInput"
          :class="
          ['bg-slate-800 text-white border-slate-700 rounded-xl border px-3 py-3 w-full focus:outline-none focus:border-blue-500',
           { 'border-red-500': hasError }
           ]"
          placeholder="Введіть число"
      />

      <p v-if="hasError && !inputValue" class="text-red-400 text-sm mt-1">Поле не повинно бути порожнім</p>
      <p v-else-if="hasError" class="text-red-400 text-sm mt-1">Некоректний ввід</p>

      <button
          @click="handleSubmit"
          class="mt-4 px-4 py-3 w-full bg-blue-500 font-bold text-white rounded-xl hover:bg-blue-700"
      >
        Перевірити
      </button>

      <div
          v-if="showNotification"
          :class="[
          'mt-4 p-4 rounded text-white',
          notificationType === 'success' ? 'bg-green-500' : 'bg-red-500'
        ]"
      >
        {{ notificationType === 'success' ? 'Успіх! Значення коректне.' : 'Помилка! Будь ласка, виправте значення.' }}
      </div>
    </div>
  </div>
</template>
