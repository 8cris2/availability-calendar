<template>

  <div>

  <table width="100%">
    <thead>
      <th></th>
      <th v-for="(day, index) in availablityData" :key="index">
        {{ day.Date }}
      </th>
    </thead>
    <tbody>
      <tr v-for="(time, index) in dailyWorkHours">
        <td class="time"><span v-if="time < '10'">0</span><span>{{ time++ }}:00 - {{ time }}:00 </span></td>
        <td v-for="(day, index) in availablityData" :startBuffer="startBuffer" :endBuffer="endBuffer" :availability="availability"
            @click="$emit('checkSlotAvailability', time, day)"
            
            :class="[ { 'available': availablityData[index].HoursAvailable.includes(time) }, 
                    { 'unavailable': !availablityData[index].HoursAvailable.includes(time) }, 
                    
                    { 'selected': jobLength > 1 } ]">
          <!--{ 'full': !availablityData[index].HoursAvailable.includes(time) },-->
          <div v-if="availablityData[index].HoursAvailable.includes(time)"> Available </div>
          <div v-else-if="!availablityData[index].HoursAvailable.includes(time)"> Unavailable </div>
          <!--<div v-else-if="!availablityData[index].HoursAvailable.includes(time)"> Full </div>-->
          <div v-else-if="jobLength > 1"> Selected </div>
          
        </td>
      </tr>
    </tbody>
  </table>

  </div>

</template>

<script type="text/javascript">
import jsonData from "@/json/availability_data.json";

export default {
  name: "availability-table-selector",
  props: ["startBuffer", "endBuffer", "availability"],
  data() {
    return {
      availablityData: jsonData,
      dailyWorkHours: [9, 10, 11, 12, 13, 14, 15, 16, 17],
      availableHours: "",
      availableDates: "",
      date: "",
      jobLength: ""
    };
  },
  computed: {
    availabilityDataComputed() {
      this.availablityData.map(avData => avData.HoursAvailable);
    }
  },
  methods: {},
  components: {}
};
</script>

<style lang="scss">
$charcoal-black: #333333;
$border-blue: #27cdf1;
$white: #ffffff;

thead {
  display: table;
  width: 100%;
  table-layout: fixed;
  color: $white;
  border-top: 7px solid $border-blue;
  background-color: $charcoal-black;
  th {
    text-align: center;
    line-height: 4;
  }
}

tbody {
  display: block;
  width: 100%;
  height: 284px;
  overflow: auto;
  tr {
    display: flex;
    width: 100%;
  }
  td,
  td.time:first-child {
    width: 25%;
    text-align: center;
    line-height: 4;
    border: 2px solid #e9eaee;
    cursor: pointer;
    border-collapse: collapse;
  }
  td.time:first-child {
    width: 25%;
    pointer-events: none;
  }
}

td.unavailable {
  color: #000000;
  font-style: italic;
  pointer-events: none;
  background-color: #f6f6f6;
}

td.full {
  color: #ffffff;
  font-style: italic;
  pointer-events: none;
  background-color: #df2536;
}

.selected {
  color: #ffffff;
  font-weight: 700;
  background-color: #459d49;
}
</style>
