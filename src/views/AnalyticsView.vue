<template>
  <DashboardLayout />

  <div
    class="calendar-container bg-gradient-to-b from-white via-pink-100 to-pink-100 text-gray-700 rounded-lg shadow-md p-8 pb-20">
    <div class="header justify-center mb-8">
      <h1 class="text-2xl text-pink-500 font-bold mb-1"> Your Cycle Tracker</h1>
      <p class="text-gray-500 text-xl">Select any of the moths below</p>
      <div class="flex justify-between mt-4">
        <!-- <button
          class="bg-white text-pink-500 px-4 py-2 rounded-md shadow-md hover:bg-pink-500 hover:text-white"
          @click="toggleAllNotes()">
          {{ showAllNotes ? 'Hide Notes' : 'Show Notes' }}
        </button> -->
        <!-- <button
          class="bg-white text-pink-500 px-4 py-2 rounded-md shadow-md hover:bg-pink-500 hover:text-white"
          @click="showGraphModal = true">
          See Graph Analytics
        </button> -->
      </div>
    </div>
    <div class="calendar">
      <div v-for="month in months" :key="month.name" class="month-section">
        <div class="month-header flex justify-between items-center mb-2">
          <div
            class="month cursor-pointer w-full rounded-full text-center py-2 text-pink-500 bg-white hover:bg-pink-500 hover:text-white"
            :class="{ 'bg-pink-500 text-white': showDates[month.name] }"
            @click="toggleDates(month.name)">
            {{ month.name }}
          </div>
        </div>
        <div v-if="showDates[month.name]" class="dates grid grid-cols-7 gap-2">
          <div
            v-for="date in month.dates"
            :key="date"
            class="date cursor-pointer px-4 py-2 rounded-full text-gray-500 hover:bg-gray-200 hover:text-gray-700"
            :class="{
              'bg-pink-200 text-white': markedDates.includes(date),
              'bg-pink-500 text-white': isLastPeriodStart(date),
              'bg-pink-500 text-white': isPeriodDay(date, month.name),
              'bg-indigo-500 text-white': isOvulationDay(date, month.name)
            }"
            @click="markDate(date)">
            {{ date }}
          </div>
        </div>
        <div v-if="showAllNotes" class="notes bg-white p-4 rounded-md shadow-md mt-4">
          <h2 class="text-xl text-pink-500 font-bold mb-4">Notes</h2>
          <p class="text-gray-500 text-base mb-2">Last Period Start: {{ formatDate(userData.last_period_start) }}</p>
          <p class="text-gray-500 text-base mb-2">Last Cycle Duration: {{ userData.last_cycle_regular }} days</p>
          <p class="text-gray-500 text-base mb-2">Period Duration: {{ userData.duration_period }} days</p>
          <p class="text-gray-500 text-base mb-2">Trying to Conceive: {{ userData.conceiveTrue }}</p>
          <p class="text-gray-500 text-base mb-2">Last Cycle Irregular: {{ userData.last_cycle_irregular }}</p>
        </div>
      </div>
    </div>
  </div>

  <!-- Graph Modal -->
  <div v-if="showGraphModal" class="fixed inset-0 flex items-center justify-center z-50">
    <div class="modal-overlay absolute inset-0 bg-gray-800 opacity-75"></div>
    <div class="modal-container bg-white rounded-lg shadow-lg p-8 z-10">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl text-pink-500 font-bold">Cycle Length Analytics</h2>
        <button
          class="text-gray-500 hover:text-gray-700"
          @click="showGraphModal = false">
          <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
          </svg>
        </button>
      </div>
      <div class="graph-container">
        <!-- Add your chart or graph component here -->
        <p class="text-gray-500">Chart or graph component will be rendered here.</p>
      </div>
    </div>
  </div>
</template>

<script>
import DashboardLayout from '@/layouts/DashboardLayout.vue'
import { getdata } from '@/api/index'

async function fetchData() {
  try {
    const response = await getdata();
    const backendData = response.data;
    return backendData;
  } catch (error) {
    console.error('Error fetching data:', error);
    return null;
  }
}

export default {
  data() {
    return {
      months: [
        { name: 'January', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'February', dates: [...Array(28).keys()].map((x) => x + 1) },
        { name: 'March', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'April', dates: [...Array(30).keys()].map((x) => x + 1) },
        { name: 'May', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'June', dates: [...Array(30).keys()].map((x) => x + 1) },
        { name: 'July', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'August', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'September', dates: [...Array(30).keys()].map((x) => x + 1) },
        { name: 'October', dates: [...Array(31).keys()].map((x) => x + 1) },
        { name: 'November', dates: [...Array(30).keys()].map((x) => x + 1) },
        { name: 'December', dates: [...Array(31).keys()].map((x) => x + 1) }
      ],

      showDates: {
        January: false,
        February: false,
        March: false,
        April: false,
        May: false,
        June: false,
        July: false,
        August: false,
        September: false,
        October: false,
        November: false,
        December: false
      },

      showAllNotes: false,
      showGraphModal: false,

      markedDates: [],
      userData: {},
    };
  },
  components: {
    DashboardLayout
  },
  methods: {
    toggleDates(monthName) {
      this.showDates[monthName] = !this.showDates[monthName];
      // If the clicked month is now visible, hide all other months
      if (this.showDates[monthName]) {
        Object.keys(this.showDates).forEach(month => {
          if (month !== monthName) {
            this.showDates[month] = false;
          }
        });
      }
    },
    toggleAllNotes() {
      this.showAllNotes = !this.showAllNotes;
    },
    markDate(date) {
      if (this.markedDates.includes(date)) {
        this.markedDates = this.markedDates.filter((d) => d !== date);
      } else {
        this.markedDates.push(date);
      }
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      return date.toLocaleDateString();
    },
    isLastPeriodStart(date) {
      const lastPeriodStart = new Date(this.userData.last_period_start);
      return (
        date === lastPeriodStart.getDate() &&
        lastPeriodStart.getMonth() === new Date().getMonth()
      );
    },
    isPeriodDay(date, monthName) {
      const periodData = this.userData.periods.find(item => item.month === monthName);
      if (periodData) {
        return periodData.days.includes(date);
      }
      return false;
    },
    isOvulationDay(date, monthName) {
      const ovulationData = this.userData.ovulationDays.find(item => item.month === monthName);
      if (ovulationData) {
        return date === ovulationData.day;
      }
      return false;
    },
    getMonthName(monthNumber) {
      const months = [
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
      ];
      return months[monthNumber - 1];
    }
  },
  async mounted() {
    const data = await fetchData();
    if (data) {
      // Initialize periods and ovulationDays arrays
      const periods = [];
      const ovulationDays = [];

      // Process the period_dates from the backend data
      data.period_dates.forEach(periodData => {
        const periodDays = periodData.period_dates[0].split(',');
        const periodMonth = periodData.period_month;
        const periodYear = periodData.period_year;

        // Add the period days to the periods array
        periods.push({
          month: this.getMonthName(periodMonth),
          days: periodDays.map(Number)
        });

        // Add the ovulation day to the ovulationDays array (if available)
        if (periodData.ovulation_date) {
          const ovulationDay = Number(periodData.ovulation_date.split(',')[0]);
          ovulationDays.push({
            month: this.getMonthName(periodMonth),
            day: ovulationDay
          });
        }
      });

      // Update the userData with the processed data
      this.userData = {
        userName: data.user_name,
        DOB: data.dob,
        last_period_start: data.last_period_start,
        last_cycle_regular: data.last_cycle_regular,
        duration_period: data.duration_period,
        conceiveTrue: data.conceive_true,
        last_cycle_irregular: data.last_cycle_irregular,
        periods,
        ovulationDays
      };
    }
  },
};
</script>