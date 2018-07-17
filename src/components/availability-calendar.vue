<template>

  <div>
    <div class="container">
      <div class="col-lg-12 col-md-12 col-sm-12 col-xs-12">
        <div class="u-calendar-wrap">
          <div class="col-lg-6 col-md-6 col-sm-12 col-xs-12">
            <h3 class="u-calendar-wrap__header"> Step 1. Use the slider to estimate how long the job will take. </h3>
            <div class="c-slide">
              <div class="c-slide__value">{{ jobLength }}
                HR<span v-if="jobLength > 1">/S</span>
                <span v-else-if="jobLength === 0">/S</span>
              </div>
              <vue-slider ref="slider" :dot-width="27" :dot-height="27" :height="8" :min="0" :max="9" :tooltip="false" v-model="jobLength" :options="options"></vue-slider>
            </div>
          </div>
          <div class="col-lg-6 col-md-6 col-sm-12 col-xs-12">
            <h3 class="u-calendar-wrap__header">Step 2. Select an arrival time</h3>
          </div>
          <div class="col-lg-12 col-md-12 col-sm-12 col-xs-12">
            <availabilityTableSelector startBuffer="1" endBuffer="1" availability="availablityDataComputed" @checkSlotAvailability="checkSlotAvailability"></availabilityTableSelector>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script type="text/javascript">
import vueSlider from "vue-slider-component";
import availabilityTableSelector from "../components/availability-table/availability-table-selector.vue";

import jsonData from "@/json/availability_data.json";

export default {
  name: "availability-calendar",
  data() {
    return {
      time: "",
      jobLength: 1,
      date: "",
      availability: "",
      selectedSlot: "",
      selectedTime: "",
      // value: 1,
      options: {
        eventType: "auto",
        width: "auto",
        height: 10,
        dotSize: 25,
        dotHeight: null,
        dotWidth: null,
        min: 0,
        max: 9,
        interval: 1,
        show: true,
        speed: 0.5,
        disabled: false,
        piecewise: false,
        piecewiseStyle: false,
        piecewiseLabel: false,
        tooltip: false,
        tooltipDir: "top",
        reverse: false,
        data: null,
        clickable: true,
        realTime: false,
        lazy: false,
        formatter: null,
        bgStyle: null,
        sliderStyle: null,
        processStyle: null,
        piecewiseActiveStyle: null,
        piecewiseStyle: null,
        tooltipStyle: null,
        labelStyle: null,
        labelActiveStyle: null
      },
      availablityData: jsonData
    };
  },
  computed: {},
  mounted() {
    this.checkSlotAvailability();
  },
  methods: {
    checkSlotAvailability(time, jobLength, date, availability) {
      console.log("time", time);
      let hoursArray = [9, 10, 11, 12, 13, 14, 15, 16, 17];

      var moment = require("moment");
      var currentDay = "2016-05-18T11:27:00"; // set to fake date in the brief

      date = moment(currentDay)
        .add(1, "hours")
        .startOf("hour")
        .toString();

      console.log("Checking date: ", date);

      let startOfDay = moment()
        .startOf("day")
        .toString();
      let endOfDay = moment()
        .endOf("day")
        .toString();

      console.log("startOfDay", startOfDay);
      console.log("endOfDay", endOfDay);

      //Check for available dates within the availablityData json object
      let availableDates = this.availablityData.map(
        availablityData => availablityData.Date
      );
      console.log("availableDates", availableDates);

      // Check for available hours in the data for a specific date.
      let availableHours = this.availablityData.filter(availableHours => {
        console.log("availableHours: ", availableHours["HoursAvailable"]);
        return availableHours["HoursAvailable"] === date;
      });

      if (time < hoursArray[0] || time > hoursArray.pop()) {
        return "Unavailable";
      }

      if (!availableDates.includes(moment(date).format("YYYY-MM-DD"))) {
        return "Unavailable";
      }

      console.log("jobLength: ", this.jobLength);
      if (this.jobLength < 1 || this.jobLength > 5) {
        console.log("jobLength is out of range");
        return "Unavailable";
      }

      // add a minimum of 1 hour either side of job start and end
      let startBuffer = 1;
      let endBuffer = 1;

      // buffer stuffs here
      // is the desired date, today? if so, add another '1' to startBuffer
      console.log("Checking if %s = %s", time.date, date);
      if (time.date === date) {
        console.log("date: ", date);
        startBuffer++;
      }

      console.log("Your buffers are now: %s and %s", startBuffer, endBuffer);

      let jobLengthWithBuffers = (startBuffer += this.jobLength += endBuffer);

      console.log("jobLengthWithBuffers: ", jobLengthWithBuffers);

      if (jobLengthWithBuffers === availableHours["HoursAvailable"].date) {
        return "Selected";
      }

      availability = ["Available", "Unavailable", "Full"];
      console.log("availability", availability);

      /*// Return some status according to what is available in the data
        if (availableHours['HoursAvailable'] === date) {
          this.availability = availability[0];
          return this.availability; // Available
        } else if (availableHours['HoursAvailable'] !== date) {
          this.availability = availability[1];
          return this.availability; // Unavailable
        } else {
          this.availability = availability[2];
          return this.availability; // Full
        }*/
    }
  },
  components: {
    vueSlider,
    availabilityTableSelector
  }
};
</script>

<style lang="scss">
$charcoal-black: #333333;
$border-blue: #27cdf1;
.u-calendar-wrap {
  padding: 15px 30px;
  margin: auto;
  -webkit-box-shadow: 2px 2px 10px 5px rgba(0, 0, 0, 0.1);
  -ms-box-shadow: 2px 2px 10px 5px rgba(0, 0, 0, 0.1);
  box-shadow: 2px 2px 10px 5px rgba(0, 0, 0, 0.1);
  background-color: #ffffff;
  &__header {
    margin-bottom: 20px;
    text-align: left;
    color: $charcoal-black;
    font-size: 25px;
    font-weight: 700;
    line-height: 35px;
  }
}

.c-slide {
  padding: 24px 20px;
  margin-bottom: 43px;
  background-color: #e9eaee;
  &__value {
    width: 100px;
    height: 17px;
    margin: auto;
    color: $charcoal-black;
    font-size: 16px;
    font-weight: 700;
    text-align: center;
    line-height: 0;
  }
} // override styles for vue slider component
.vue-slider-component .vue-slider-dot {
  background-color: $charcoal-black;
}

.vue-slider-component .vue-slider-process {
  background-color: $charcoal-black;
}

.vue-slider-component .vue-slider {
  background-color: $border-blue;
} // override styles for vue slider component
</style>
