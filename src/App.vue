<script setup lang="ts">
import { computed, ref } from "vue";

import ClipboardIcon from "@/components/icons/ClipboardIcon.vue";

const binInput = ref<string>("");

const result = ref<number>(0);
const resultList = ref<number[]>([]);

const isResultCopied = ref<boolean>(false);

const currentYear = computed(() => {
  return new Date(Date.now()).getFullYear();
});

const inputError = computed(() => {
  return !/^[01]+$/gm.test(binInput.value);
});

const convertToDecimal = () => {
  if (!inputError.value) {
    result.value = 0;
    for (let binVal of binInput.value) {
      result.value = result.value * 2 + parseInt(binVal);
    }

    resultList.value.push(result.value);
  }
};

const copyResult = () => {
  copyText(result.value);
};

const copyText = (value: number) => {
  isResultCopied.value = false;

  navigator.clipboard
    .writeText(value.toString())
    .then(() => {
      isResultCopied.value = true;
      setTimeout(() => (isResultCopied.value = false), 3000);
    })
    .catch((err) => console.error(err));
};
</script>

<template>
  <div class="wrapper">
    <main class="container">
      <h1>Binary - Decimal Converter</h1>
      <div class="form">
        <label class="label" for="binInput">Input your binary number here:</label>
        <input
          class="input"
          name="binInput"
          v-model="binInput"
          type="text"
          @keydown.enter="convertToDecimal"
        />
        <small class="error" v-if="inputError && binInput.length > 0">
          The input only accepts series of 0 and 1
        </small>
        <button
          class="form-button"
          @click="convertToDecimal"
          :disabled="inputError || binInput.length <= 0"
        >
          Convert
        </button>
      </div>

      <h3 v-if="result > 0" class="result-text">The result is: {{ result }}</h3>

      <div class="result" v-if="result > 0">
        <span class="result-copy">
          <button class="result-button" @click="copyResult">Copy result</button>
        </span>
      </div>

      <div v-if="resultList.length > 0" class="result-list">
        <h4>Resultados anteriores</h4>
        <span
          v-for="(result, idx) in resultList"
          :key="idx"
          class="list-item"
          @click="copyText(result)"
          >{{ result }} <ClipboardIcon class="copy-icon">Copy</ClipboardIcon></span
        >
      </div>
    </main>
    <footer class="footer">
      <h5>
        Made by <a href="https://github.com/MHGuitarte">mhgdev</a> as part of florinpop17's
        <a href="https://github.com/florinpop17/app-ideas">app-ideas</a> - @{{ currentYear }}
      </h5>
    </footer>

    <div class="toast" :class="{ visible: isResultCopied }">
      <h3 class="copy-confirmation">Copied!</h3>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  width: 100%;
  height: 100vh;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
}

.container {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 2.5rem;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

label {
  font-size: 0.85rem;
}

.input {
  width: 380px;
  background-color: transparent;
}

.error {
  color: red;
}

.form-button {
  background-color: cornflowerblue;
  color: white;
  font-weight: 700;
}

.form-button:active {
  background-color: dodgerblue;
}

.form-button:disabled {
  background-color: lightgray;
  color: rgba(128, 128, 128, 0.3);
}

.result {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 1rem;
}

.result-copy {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 0.25rem;
}

.result-list {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: flex-start;
  gap: 1rem;
  overflow-y: auto;
  height: 200px;
}

.list-item {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  width: 300px;
  border: 1px solid black;
  padding: 1rem;
  cursor: pointer;
}
.copy-icon {
  width: 3rem;
}

a {
  font-size: 0.75rem;
}

.toast {
  z-index: 10;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  padding: 1rem;
  background-color: limegreen;
  color: white;
  font-weight: 700;

  visibility: hidden;
}

.toast.visible {
  visibility: visible;
  animation:
    fadein 0.5s,
    fadeout 0.5s 2.5s;
}

@keyframes fadein {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes fadeout {
  from {
    opacity: 1;
  }
  to {
    opacity: 0;
  }
}
</style>
