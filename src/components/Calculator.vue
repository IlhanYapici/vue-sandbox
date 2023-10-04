<style>
.calculator {
  width: fit-content;
  padding: 1rem;
  margin: 0 auto;
}

.result {
  min-height: 4rem;
  height: 100%;
  max-height: 6rem;
  margin-bottom: 0.5rem;
}

.result input {
  text-align: right;
  font-size: 2rem;
}

.grid-container {
  display: grid;
  grid-template-columns: max-content minmax(4rem, 6rem);
  gap: 0.5rem;
}

.btn-grid {
  display: grid;
  gap: 0.5rem;
  grid-template-columns: repeat(3, minmax(4rem, 6rem));
  grid-auto-rows: minmax(4rem, 6rem);
}

.btn-grid button {
  height: 100% !important;
}

.btn-grid .two-col {
  grid-column: span 2;
}

.operations-grid {
  display: grid;
  gap: 0.5rem;
  grid-template-columns: 1fr;
  grid-auto-rows: minmax(4rem, 6rem);
}

.operations-grid button {
  height: 100% !important;
}
</style>

<script lang="ts">
import { ref, watch } from "vue";

type Operation = "+" | "-" | "*" | "/" | ",";

type Calculation =
  | {
      type: "value";
      value: number;
    }
  | {
      type: "operation";
      value: Operation;
    };

const result = ref(0);

const currentValue = ref<number | string>("0");

const calculations = ref<Calculation[]>([]);

watch(calculations, (newVal) => {
  console.log({ calculations: newVal });
});

watch(currentValue, (newVal) => {
  console.log({ currentValue: newVal });
});

function allClear() {
  if (result.value !== 0) {
    result.value = 0;
    calculations.value = [];
  }

  if (currentValue.value !== 0) {
    currentValue.value = 0;
  }
}

// TODO: handle case when we append a value where the last element is not an operation

function handleInput(param: Calculation) {
  const { type, value } = param;

  if (type === "operation" && value === ",") {
    currentValue.value = String(currentValue.value).includes(".")
      ? currentValue.value
      : String(currentValue.value + ".");
  } else if (type === "value") {
    if (currentValue.value === 0) {
      currentValue.value = value;
    } else {
      currentValue.value = Number(`${currentValue.value}${value}`);
    }
  } else {
    if (currentValue.value !== 0) {
      calculations.value = [
        ...calculations.value,
        { type: "value", value: Number(currentValue.value) },
      ];
      currentValue.value = 0;
    }
    calculations.value = [...calculations.value, { type: "operation", value }];
  }
}

function calculate() {
  let copyCalculations = [...calculations.value];

  if (currentValue.value !== 0) {
    copyCalculations = [
      ...copyCalculations,
      { type: "value", value: Number(currentValue.value) },
    ];
    currentValue.value = 0;
  }

  let calcResult = 0;

  while (copyCalculations.length > 1) {
    for (let i = 0; i < copyCalculations.length; i++) {
      const { type, value } = copyCalculations[i];

      if (type === "operation") {
        const prevValue = copyCalculations[i - 1]
          ? copyCalculations[i - 1].value
          : 0;
        const nextValue = copyCalculations[i + 1]
          ? copyCalculations[i + 1].value
          : 0;

        switch (value) {
          case "+":
            calcResult = Number(prevValue) + Number(nextValue);
            break;
          case "-":
            calcResult = Number(prevValue) - Number(nextValue);
            break;
          case "*":
            calcResult = Number(prevValue) * Number(nextValue);
            break;
          case "/":
            calcResult = Number(prevValue) / Number(nextValue);
            break;
          case ",":
            break;
        }

        if (copyCalculations[i - 1] && copyCalculations[i + 1]) {
          const prevElement = copyCalculations[i - 1];

          copyCalculations.splice(
            copyCalculations.findIndex(
              (element) => element.value === prevElement.value
            ),
            3,
            { type: "value", value: calcResult }
          );
        }
      }
    }
  }

  calculations.value = [{ type: "value", value: calcResult }];
  currentValue.value = 0;
  result.value = calcResult;
}
</script>

<template>
  <div class="calculator">
    <v-text-field
      class="result"
      variant="outlined"
      :model-value="result"
      hide-details
      readonly
    ></v-text-field>
    <div class="grid-container">
      <div class="btn-grid">
        <v-btn size="x-large" color="grey" elevation="0" v-on:click="allClear"
          >AC</v-btn
        >
        <v-btn size="x-large" color="grey" elevation="0">+/-</v-btn>
        <v-btn size="x-large" color="grey" elevation="0">%</v-btn>
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 7 })"
          >7</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 8 })"
          >8</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 9 })"
          >9</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 4 })"
          >4</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 5 })"
          >5</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 6 })"
          >6</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 1 })"
          >1</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 2 })"
          >2</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 3 })"
          >3</v-btn
        >
        <v-btn
          class="two-col"
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'value', value: 0 })"
          >0</v-btn
        >
        <v-btn
          variant="tonal"
          size="x-large"
          @click="handleInput({ type: 'operation', value: ',' })"
          >,</v-btn
        >
      </div>
      <div class="operations-grid">
        <v-btn
          size="x-large"
          color="primary"
          elevation="0"
          @click="handleInput({ type: 'operation', value: '/' })"
          >/</v-btn
        >
        <v-btn
          size="x-large"
          color="primary"
          elevation="0"
          @click="handleInput({ type: 'operation', value: '*' })"
          >*</v-btn
        >
        <v-btn
          size="x-large"
          color="primary"
          elevation="0"
          @click="handleInput({ type: 'operation', value: '-' })"
          >-</v-btn
        >
        <v-btn
          size="x-large"
          color="primary"
          elevation="0"
          @click="handleInput({ type: 'operation', value: '+' })"
          >+</v-btn
        >
        <v-btn
          size="x-large"
          color="primary"
          elevation="0"
          v-on:click="calculate"
          >=</v-btn
        >
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
//
</script>
