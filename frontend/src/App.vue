
<template>
  <div class="main-container" id="app">
        <header>
            <div class="container">
                <h1>Count my
                    <span>
                        <small>Bike</small>
                    </span>
                </h1>
                <p class="intro">
                    Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
                    Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
                    Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
                </p>
                <ul class="stats-list">
                    <li class="day-counter">
                        <h3>
                            <strong class="nbr">{{ todayCount }}</strong>
                            <small>today</small>
                        </h3>
                    </li>
                    <li class="week-counter">
                        <h3>
                            <strong class="nbr">{{ weekCount }}</strong>
                            <small>this week</small>
                        </h3>
                    </li>
                    <li class="month-counter">
                        <h3>
                            <strong class="nbr">{{ monthCount }}</strong>
                            <small>this month</small>
                        </h3>
                    </li>
                </ul>
            </div>
        </header>
        <section class="section-stats">
            <div class="container">
                <article class="daily-stats chart-01">
                    <h2>Daily stats</h2>

                    <DayToDayChart class="chart-area" :serie-data="chartData" :selected-day="selectedDay" v-on:select="selectDateDetail"></DayToDayChart>

                </article>
                <article class="daily-stats chart-02">
                    <h2>Details
                        <template v-if="selectedDay"> of {{ selectedDayString }}</template>
                        <template v-else>(select a day above)</template>
                    </h2>
                    <div class="chart-prev-next">
                        <button class="btn-action btn-detail-prev" v-if="selectedDay" @click="selectPrevious">&laquo;</button>

                        <detail-chart class="chart-area" :serie-data="detailDayData" :selected-day="selectedDay" v-if="detailDayData && detailDayData.length > 0"></detail-chart>
                        <div class="no-data" v-if="! detailDayData || detailDayData.length == 0">
                            No data for this day ¯\_(ツ)_/¯
                        </div>
                        <button class="btn-action btn-detail-next" v-if="selectedDay" @click="selectNext">&raquo;</button>
                    </div>

                </article>
                <article class="daily-stats info-01">
                    <h2>Daily stats</h2>
                    <p>
                        Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
                        Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
                        consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat
                        nulla pariatur.
                    </p>
                    <ul class="one-number-list">
                        <li>
                            <strong class="trends-day">{{ trendsDay }} %</strong>
                            <p> differences of bikes between last
                                <em>24 hours</em> and last 48 hours</p>
                        </li>
                        <li>
                            <strong class="trends-rain"> -21%</strong>
                            <p>there is arround 21% less cycles on rainy days</p>
                        </li>
                    </ul>
                </article>
            </div>
        </section>
        <section class="section-interactive-chart">
            <div class="container">
                <YearlyChart id="year-chart"></YearlyChart>
                <ul class="year-facts">
                    <li>
                        <picture>
                            <img src="" alt="">
                        </picture>
                        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod</p>
                    </li>
                    <li>
                        <picture>
                            <img src="" alt="">
                        </picture>
                        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod</p>
                    </li>
                    <li>
                        <picture>
                            <img src="" alt="">
                        </picture>
                        <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod</p>
                    </li>
                </ul>
            </div>
            <footer>
                <div class="inner">
                    <a href="https://github.com/eMerzh/count-my-bike">
                        <svg class="githubico" xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16">
                            <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38 0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13-.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66.07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15-.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0 1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82 1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01 1.93-.01 2.2 0 .21.15.46.55.38A8.013 8.013 0 0 0 16 8c0-4.42-3.58-8-8-8z"
                            />
                        </svg>
                    </a>
                    <a href="http://opendatastore.brussels/">
                        Using opendatastore.brussels
                    </a>
                </div>
            </footer>
        </section>
    </div>
</template>

<script>
import format from "date-fns/format";
import parse from "date-fns/parse";
import addDays from "date-fns/add_days";
import subDays from "date-fns/sub_days";
import { prepareData } from "./utils";

import DayToDayChart from "./components/DayToDayChart";
import YearlyChart from "./components/YearlyChart";
import DetailChart from "./components/DetailChart";

export default {
  data: () => ({
    apiResponse: { day: {}, week: {}, month: {} },
    statByDay: {},
    statByMinute: {},
    selectedDay: null
  }),
  components: {
    DayToDayChart,
    YearlyChart,
    DetailChart
  },
  computed: {
    chartData() {
      return this.apiResponse.ts ? prepareData(this.apiResponse.ts) : {};
    },
    selectedDayString() {
      return format(this.selectedDay, "YYYY-M-D");
    },

    /**
    Select the details into the drilldowns of the main chart
     */
    detailDayData() {
      if (!this.selectedDay) return null;
      var dayStr = format(this.selectedDay, "YYYY-M-D");

      return this.chartData["drilldowns"].filter(function(value) {
        return value.name == dayStr;
      });
    },
    todayCount() {
      return this.apiResponse.day.counter;
    },
    weekCount() {
      return this.apiResponse.week.counter;
    },
    monthCount() {
      return this.apiResponse.month.counter;
    },
    trendsDay() {
      return this.apiResponse.day.trend;
    }
  },
  methods: {
    selectDateDetail(data) {
      this.selectedDay = parse(data.day);
    },
    selectPrevious() {
      this.selectedDay = subDays(this.selectedDay, 1);
    },
    selectNext() {
      this.selectedDay = addDays(this.selectedDay, 1);
    }
  },
  created() {
    this.$http.get("data.json").then(response => {
      this.apiResponse = response.data;
    });
  }
};
</script>

<style lang="scss">
@import "./assets/scss/styles.scss";
</style>
