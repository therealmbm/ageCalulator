<script setup>
import { ref } from "vue";

// Reactive state for form inputs and errors
const day = ref("");
const month = ref("");
const year = ref("");
const errors = ref({ day: "", month: "", year: "" });
const age = ref({ years: "--", months: "--", days: "--" });

function calculateAge() {
  // Reset errors
  errors.value = { day: "", month: "", year: "" };

  const currentDate = new Date();
  const inputDate = new Date(`${year.value}-${month.value}-${day.value}`);

  // Validation
  if (!day.value || day.value < 1 || day.value > 31) {
    errors.value.day = "Invalid day. Must be between 1 and 31.";
  }
  if (!month.value || month.value < 1 || month.value > 12) {
    errors.value.month = "Invalid month. Must be between 1 and 12.";
  }
  if (!year.value || year.value > currentDate.getFullYear()) {
    errors.value.year = "Invalid year. Must not exceed the current year.";
  }
  if (Object.values(errors.value).some((e) => e)) {
    return;
  }

  // Check if the date is valid
  if (isNaN(inputDate.getTime())) {
    errors.value.year = "Invalid date.";
    return;
  }

  if (inputDate > currentDate) {
    errors.value.year = "Date must not be in the future.";
    return;
  }

  // Calculate age
  let diffYears = currentDate.getFullYear() - inputDate.getFullYear();
  let diffMonths = currentDate.getMonth() - inputDate.getMonth();
  let diffDays = currentDate.getDate() - inputDate.getDate();

  if (diffDays < 0) {
    diffMonths--;
    diffDays += new Date(currentDate.getFullYear(), currentDate.getMonth(), 0).getDate(); // Days in the previous month
  }
  if (diffMonths < 0) {
    diffYears--;
    diffMonths += 12;
  }

  age.value = {
    years: diffYears,
    months: diffMonths,
    days: diffDays,
  };
}
</script>

<template>
  <div class="wrapper">
    <div class="container">
      <form @submit.prevent="calculateAge">
        <div class="input-container">
          <label for="day">D a y</label>
          <input type="number" id="day" placeholder="DD" v-model="day" :class="{ error: errors.day }" />
          <p v-if="errors.day" class="error-message">{{ errors.day }}</p>
        </div>
        <div class="input-container">
          <label for="month">M o n t h</label>
          <input type="number" id="month" placeholder="MM" v-model="month" :class="{ error: errors.month }" />
          <p v-if="errors.month" class="error-message">{{ errors.month }}</p>
        </div>
        <div class="input-container">
          <label for="year">Y e a r</label>
          <input type="number" id="year" placeholder="YYYY" v-model="year" :class="{ error: errors.year }" />
          <p v-if="errors.year" class="error-message">{{ errors.year }}</p>
        </div>
      </form>
      <div class="button-wrapper">
        <button @click="calculateAge">
          <div class="arrow-wrapper">
            <img src="./assets/icon-arrow.svg" alt="arrow" />
          </div>
        </button>
      </div>
      <div class="info">
        <h1><span>{{ age.years }}</span> years</h1>
        <h1><span>{{ age.months }}</span> months</h1>
        <h1><span>{{ age.days }}</span> days</h1>
      </div>
    </div>
  </div>
</template>

<style>
.wrapper {
  display: grid;
  place-items: center;
  height: 100vh;
}

.container {
  max-width: 650px;
}

form {
  display: flex;
  gap: 20px;
}

.input-container {
  display: flex;
  flex-direction: column;
}

label {
  color: var(--smokey-grey);
  text-transform: uppercase;
  margin-bottom: 10px;
}

input {
  padding: 10px;
  font-size: 30px;
  max-width: 150px;
  border: 1px solid var(--light-grey);
  margin-bottom: 10px;
}

input.error {
  border-color: var(--light-red);
}

.error-message {
  color: var(--light-red);
  font-size: 12px;
  font-weight: 100;
  margin: 0;
}

.button-wrapper {
  display: grid;
  place-items: end;
}

button {
  border: 0;
  cursor: pointer;
}

.arrow-wrapper {
  background-color: var(--purple);
  padding: 20px;
  border-radius: 50%;
}

.arrow-wrapper:hover {
  background-color: var(--off-black);
}

h1 {
  font-size: 100px;
  font-weight: 900;
  font-style: italic;
  margin: 0;
  line-height: 1;
}

span {
  color: var(--purple);
}
</style>
