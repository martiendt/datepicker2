<script>
import { defineComponent } from 'vue';
import { format, startOfMonth, lastDayOfMonth, addDays, subDays, startOfDay, addMonths, subMonths, isEqual, getDaysInMonth } from 'date-fns'

export default /*#__PURE__*/defineComponent({
 name: "VuePicker",
  props: {
    modelValue: {
      type: Date,
      required: true,
      default: new Date()
    }
  },
  emits: ["update:modelValue"],
  data() {
    return {
      firstDayInMonth: startOfMonth(new Date(this.modelValue))
    };
  },
  computed: {
    daysInCurrentMonth() {
      let days = [];
      for (let i = 0; i < getDaysInMonth(this.firstDayInMonth); i++) {
        days.push(addDays(this.firstDayInMonth, i));
      }
      return days;
    },
    daysInPreviewPreviousMonth() {
      let days = [];
      for (let i = 1; i <= this.firstDayInMonth.getDay(); i++) {
        days.unshift(subDays(this.firstDayInMonth, i));
      }
      return days;
    },
    daysInPreviewNextMonth() {
      let days = [];
      const gridsize = 42;
      const loop =
        gridsize -
        this.daysInCurrentMonth.length -
        this.daysInPreviewPreviousMonth.length;
      for (let i = 1; i <= loop; i++) {
        days.push(addDays(startOfDay(lastDayOfMonth(this.firstDayInMonth)), i));
      }

      return days;
    },
    days() {
      return [
        ...this.daysInPreviewPreviousMonth,
        ...this.daysInCurrentMonth,
        ...this.daysInPreviewNextMonth
      ];
    }
  },
  methods: {
    format: format,
    goNextMonth() {
      this.firstDayInMonth = startOfMonth(addMonths(this.firstDayInMonth, 1));
    },
    goPrevMonth() {
      this.firstDayInMonth = startOfMonth(subMonths(this.firstDayInMonth, 1));
    },
    selectDate(date) {
      this.$emit("update:modelValue", date);
      if (!this.isCurrentMonth(date)) {
        this.firstDayInMonth = startOfMonth(date);
      }
    },
    isCurrentMonth(date) {
      return (
        new Date(date).getMonth() === new Date(this.firstDayInMonth).getMonth()
      );
    },
    isToday(date) {
      return isEqual(new Date(date), startOfDay(new Date()));
    },
    isSelected(date) {
      return isEqual(new Date(date), new Date(this.modelValue));
    }
  }
});
</script>

<template>
  <div class="flex items-center mb-4">
    <button
      class="block p-2 text-blue-500 focus-visible:ring-inset"
      @click="goPrevMonth"
    >
      &lt;
    </button>
    <div class="mx-auto text-lg font-medium">
      {{ format(firstDayInMonth, "MMMM yyyy") }}
    </div>
    <button
      class="block p-2 text-blue-500 focus-visible:ring-inset"
      @click="goNextMonth"
    >
      &gt;
    </button>
  </div>
  <div class="grid grid-cols-7 gap-y-2">
    <!-- days -->
    <div class="days">
      Mi
    </div>
    <div class="days">
      Se
    </div>
    <div class="days">
      Se
    </div>
    <div class="days">
      Ra
    </div>
    <div class="days">
      Ka
    </div>
    <div class="days">
      Ju
    </div>
    <div class="days">
      Sa
    </div>
    <!-- dates -->
    <button
      v-for="date in days"
      :key="date"
      class="days"
      :class="{
        'not-current-month': !isCurrentMonth(date),
        today: isToday(date),
        selected: isSelected(date)
      }"
      :disabled="false"
      @click="selectDate(date)"
    >
      {{ format(date, "d") }}
    </button>
  </div>
</template>

<style scoped>
  .vue-picker {
    display: block;
    width: 400px;
    margin: 25px auto;
    border: 1px solid #ccc;
    background: #eaeaea;
    text-align: center;
    padding: 25px;
  }
  .vue-picker p {
    margin: 0 0 1em;
  }
</style>
