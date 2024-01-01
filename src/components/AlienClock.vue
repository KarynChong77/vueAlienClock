<script setup>
import { ref, watch } from "vue";

const calError = ref(false);
const alienMinutes = ref(0);
const alienHours = ref(0);
const alienDays = ref(0);
const alienMonths = ref(0);
const alienYear = ref(0);

const ogDateTime = ref({
  year: 2804,
  month: 18,
  day: 31,
  hour: 2,
  minutes: 2,
  seconds: 88,
});

const datetime = ref({
  year: 2804,
  month: 18,
  day: 31,
  hour: 2,
  minutes: 2,
  seconds: 88,
});

const editdatetime = ref({
  year: 2804,
  month: 18,
  day: 31,
  hour: 2,
  minutes: 2,
  seconds: 88,
});

const props = defineProps(["sendTime"]);
const emit = defineEmits(["alienTime"]);

const cal = ref("");

watch(
  () => props.sendTime,
  (newValue) => {
    datetime.value = { year: 2804, month: 18, day: 31, hour: 2, minutes: 2, seconds: 88 };

    alienYear.value = Math.floor(newValue / (770 * 36 * 8100));
    var remainingSecondsYear = newValue % (770 * 36 * 8100);

    datetime.value.year += alienYear.value;

    alienMonths.value = Math.floor(remainingSecondsYear / (55 * 36 * 8100));
    var remainingSecondsMonth = remainingSecondsYear % (55 * 36 * 8100);

    datetime.value.month += alienMonths.value;

    alienDays.value = Math.floor(remainingSecondsMonth / (36 * 8100));
    var remainingSecondsDay = remainingSecondsMonth % (36 * 8100);

    datetime.value.day += alienDays.value;

    alienHours.value = Math.floor(remainingSecondsDay / 8100);
    var remainingSecondsHour = remainingSecondsDay % 8100;

    datetime.value.hour += alienHours.value;

    alienMinutes.value = Math.floor(remainingSecondsHour / 90);
    datetime.value.seconds = remainingSecondsHour % 90;

    datetime.value.minutes += alienMinutes.value;

    // Check date time
    checkDateTime(datetime);

    //Check days
    if (
      ((params.value.month == 1 || 18) && params.value.day >= 38) ||
      ((params.value.month == 2 || 4 || 7 || 10 || 11 || 16) && params.value.day >= 40) ||
      ((params.value.month == 2 || 9 || 12 || 15) && params.value.day >= 42) ||
      ((params.value.month == 1 || 6 || 8 || 13 || 17) && params.value.day >= 44) ||
      ((params.value.month == 3 || 5 || 14) && params.value.day >= 48)
    ) {
      params.value.day = 1;
      params.value.month++;
    } else {
      // If none match
      params.value.day++;
    }
  },
  { deep: true }
);

setInterval(() => {
  datetime.value.seconds++;

  if (datetime.value.seconds == 90) {
    datetime.value.seconds = 0;
    datetime.value.minutes++;
  }

  if (datetime.value.minutes == 90) {
    datetime.value.minutes = 0;
    datetime.value.hour++;
  }

  if (datetime.value.hour == 36) {
    datetime.value.hour = 0;

    checkDateTime(datetime);

    //Check days
    if (
      ((params.value.month == 1 || 18) && params.value.day >= 38) ||
      ((params.value.month == 2 || 4 || 7 || 10 || 11 || 16) && params.value.day >= 40) ||
      ((params.value.month == 2 || 9 || 12 || 15) && params.value.day >= 42) ||
      ((params.value.month == 1 || 6 || 8 || 13 || 17) && params.value.day >= 44) ||
      ((params.value.month == 3 || 5 || 14) && params.value.day >= 48)
    ) {
      params.value.day = 1;
      params.value.month++;
    } else {
      // If none match
      params.value.day++;
    }
  }

  if (datetime.value.month == 19) {
    datetime.value.seconds = 0;
    datetime.value.minutes = 0;
    datetime.value.hour = 0;
    datetime.value.day = 1;
    datetime.value.month = 1;
    datetime.value.year++;
  }
}, 500);

function checkDateTime(params) {
  // If there is more than 18 months
  if (params.value.month > 19) {
    params.value.month -= 18;
    params.value.year++;
  }

  // Check hours
  if (params.value.hour > 37) {
    params.value.hour -= 36;
    params.value.month++;
  }

  // check minutes
  if (params.value.minutes > 91) {
    params.value.month -= 90;
    params.value.hour++;
  }

  // check seconds
  if (params.value.month > 91) {
    params.value.month -= 90;
    params.value.year++;
  }
}

function getCal() {
  if (
    editdatetime.value.year != "" &&
    editdatetime.value.month != "" &&
    editdatetime.value.day != "" &&
    editdatetime.value.hour != "" &&
    editdatetime.value.minutes != "" &&
    editdatetime.value.seconds != ""
  ) {
    var totalSeconds =
      (editdatetime.value.seconds - ogDateTime.value.seconds) +
      ((editdatetime.value.minutes * 90) - (ogDateTime.value.minutes * 90)) +
      ((editdatetime.value.hour * 8100) - (ogDateTime.value.hour * 8100)) +
      ((editdatetime.value.day * (36 * 8100)) - (ogDateTime.value.day * (36 * 8100))) +
      (((editdatetime.value.month + 1) * (55 * 36 * 8100)) -
        ((ogDateTime.value.month + 1) * (55 * 36 * 8100))) +
      ((editdatetime.value.year * (770 * 36 * 8100)) -
        (ogDateTime.value.year * (770 * 36 * 8100)));

    emit("alienTime", totalSeconds);

    // Put new data in
    datetime.value.year = editdatetime.value.year;
    datetime.value.month = editdatetime.value.month;
    datetime.value.day = editdatetime.value.day;
    datetime.value.hour = editdatetime.value.hour;
    datetime.value.minutes = editdatetime.value.minutes;
    datetime.value.seconds = editdatetime.value.seconds;

    // Check days
    checkDateTime(datetime);
    checkDateTime(editdatetime);

  } else {
    alert("Missing Data in Alien Time input");
    emit("alienTime", 0);
  }
}
</script>

<template>
  <h1>Alien Clock</h1>
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
    <label for="year">
      <p>Set Year for Alien Time</p>
    </label>
    <input name="year" placeholder="Enter year" v-model="editdatetime.year" required />
    <p v-if="editdatetime.year == ''" style="color: red">Missing input for year</p>

    <label for="month">
      <p>Set Month for Alien Time</p>
    </label>
    <input name="month" placeholder="Enter month" v-model="editdatetime.month" required />
    <p v-if="editdatetime.month == ''" style="color: red">Missing input for month</p>

    <label for="day">
      <p>Set Day for Alien Time</p>
    </label>
    <input name="day" placeholder="Enter day" v-model="editdatetime.day" required />
    <p v-if="editdatetime.day == ''" style="color: red">Missing input for day</p>

    <label for="hour">
      <p>Set Hours for Alien Time</p>
    </label>
    <input name="hour" placeholder="Enter hour" v-model="editdatetime.hour" required />
    <p v-if="editdatetime.hour == ''" style="color: red">Missing input for hour</p>

    <label for="minutes">
      <p>Set Minutes for Alien Time</p>
    </label>
    <input
      name="minutes"
      placeholder="Enter minutes"
      v-model="editdatetime.minutes"
      required
    />
    <p v-if="editdatetime.minutes == ''" style="color: red">Missing input for minutes</p>

    <label for="seconds">
      <p>Set Seconds for Alien Time</p>
    </label>
    <input
      name="seconds"
      placeholder="Enter seconds"
      v-model="editdatetime.seconds"
      required
    />
    <p v-if="editdatetime.seconds == ''" style="color: red">Missing input for seconds</p>

    <br />
    <button @click="getCal">Change Alien Date</button>
  </div>
</template>

<style scoped></style>
