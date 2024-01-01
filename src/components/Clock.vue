<script setup>
import { ref,watch } from "vue";

const calError = ref(false);
const humanMinutes = ref(0);
const humanHours = ref(0);
const humanDays = ref(0);
const humanMonths = ref(0);
const humanYear = ref(0);

const ogDateTime = ref({ year: 1970, month: 1, day: 1, hour: 12, minutes: 0, seconds: 0 });
const datetime = ref({ year: 1970, month: 1, day: 1, hour: 12, minutes: 0, seconds: 0 });
    
const props = defineProps(["sendAlienTime"]);
const emit = defineEmits(["humanTime"]);

const cal = ref("");

console.log(props)

watch(
  () => props.sendAlienTime,
  (newValue) => {

    datetime.value = { year: 1970, month: 1, day: 1, hour: 12, minutes: 0, seconds: 0 };

    const alienDate = new Date(newValue * 1000);

    datetime.value.year = alienDate.getFullYear();
    datetime.value.month = alienDate.getMonth() + 1;
    datetime.value.day = alienDate.getDate();
    datetime.value.hour = alienDate.getHours();
    datetime.value.minutes = alienDate.getMinutes();
    datetime.value.seconds = alienDate.getSeconds();

  },
  { deep: true }
);

setInterval(() => {
  datetime.value.seconds++;

  if (datetime.value.seconds == 60) {
    datetime.value.seconds = 0;
    datetime.value.minutes++;
  }

  if (datetime.value.minutes == 60) {
    datetime.value.minutes = 0;
    datetime.value.hour++;
  }

  if (datetime.value.hour == 24) {
    datetime.value.hour = 0;

    checkDay();
  }

  if (datetime.value.month == 13) {
    datetime.value.seconds = 0;
    datetime.value.minutes = 0;
    datetime.value.hour = 0;
    datetime.value.day = 1;
    datetime.value.month = 1;
    datetime.value.year++;
  }
}, 1000);

function checkDay() {
  if (
    ((datetime.value.month == 1 || 3 || 5 || 7 || 8 || 10 || 12) &&
      datetime.value.day == 31) ||
    (datetime.value.month == 2 && datetime.value.day == 28) ||
    ((datetime.value.month == 4 || 6 || 9 || 11) && datetime.value.day == 30)
  ) {
    datetime.value.day = 1;
    datetime.value.month++;
  } else {
    // If none match
    datetime.value.day++;
  }
}

function getCal() {
  const selectedDate = new Date(cal.value);

  calError.value = isNaN(selectedDate.getTime());

  if (calError.value == false) {

    const orginalDate = new Date(ogDateTime.value.year, ogDateTime.value.month - 1, ogDateTime.value.day, ogDateTime.value.hour, ogDateTime.value.minutes)

    // // get date differences
    const millisecondsDifference = Math.abs(selectedDate - orginalDate);
    const totalSeconds = millisecondsDifference / 1000;

    console.log(totalSeconds)

    datetime.value.year = selectedDate.getFullYear();
    datetime.value.month = selectedDate.getMonth() + 1;
    datetime.value.day = selectedDate.getDate();
    datetime.value.hour = selectedDate.getHours();
    datetime.value.minutes = selectedDate.getMinutes();
    datetime.value.seconds = selectedDate.getSeconds();

    cal.value = "";


    emit("humanTime", totalSeconds);
  }
}
</script>

<template>
  <h1>Human Clock</h1>
  <br />
  <p>
    Date: <span v-if="datetime.day < 10">0</span>{{ datetime.day }} /
    <span v-if="datetime.month < 10">0</span>{{ datetime.month }} / {{ datetime.year }}
  </p>
  <p>
    Time: <span v-if="datetime.hour < 10">0</span>{{ datetime.hour }}:<span
      v-if="datetime.minutes < 10"
      >0</span
    >{{ datetime.minutes }}:<span v-if="datetime.seconds < 10">0</span
    >{{ datetime.seconds }}
  </p>

  <div>
    <label for="date">
      <p>Set Date for Human Time</p>
    </label>
    <input name="date" type="datetime-local" v-model="cal" required />
    <button @click="getCal">Change Human Date</button>

    <p v-if="calError" style="color: red">Please enter a valid date and time</p>
  </div>


  <br />
</template>

<style scoped></style>
