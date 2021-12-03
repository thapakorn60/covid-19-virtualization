<template>
  <div class="container">
    <div class="row mt-5">
      <div class="col">

      <select v-model="selected" placeholder="เลือก">

        <option v-for="no in days" :key="no" @click="created()">{{no}}</option>

      </select>
              <button type="success" @click="selectNum($event)">Enter</button>
      </div>
      <!-- <div id="preview"> -->
        <div class="col">
      <span><h3 class="text-center">{{num}} วันที่แล้ว</h3></span>
      </div>
      <!-- </div> -->
    </div>
    <div>

    </div>
    <div class="row mt-5" v-if="arrCases.length > 0">
      <div class="col">
        <h2 class="text-center">Cases</h2>
        <line-chart
          :chartData="arrCases"
          :options="chartOptions"
          :chartColors="casesChartColors"
          label="Cases"
        />
      </div>
    </div>

    <div class="row mt-5" v-if="arrDeaths.length > 0">
      <div class="col">
        <h2 class="text-center">Deaths</h2>
        <line-chart
          :chartData="arrDeaths"
          :options="chartOptions"
          :chartColors="deathsChartColors"
          label="Deaths"
        />
      </div>
    </div>

    <div>
      <div >
        <h2 class="text-center">Table</h2>
        <table class="table">
    <thead class="rows">
      <tr>
        <th >No.</th>
        <th >Day/Month/Year</th>
        <th >Cases</th>
        <th >Deaths</th>
      </tr>
    </thead>
    <tbody>  
   <!-- v-for="(day, index) in arrPositive" :key="index" -->
      <tr v-for="(d, index) in list" :key="index">

        <td>{{index+1}}</td>
        <td>{{d.A.date}}</td>
        <td>{{d.A.total}}</td>
        <td>{{d.B.total}}</td>  
      </tr>
    
    </tbody>
  </table>

      </div>

      </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

import LineChart from "./components/LineChart";

export default {
  components: {
    LineChart
  },
  data() {
    return {
      arrCases: [],
      casesChartColors: {
        borderColor: "#077187",
        pointBorderColor: "#0E1428",
        pointBackgroundColor: "#AFD6AC",
        backgroundColor: "#A1A5FF"
      },
      arrDeaths: [],
      deathsChartColors: {
        borderColor: "#FF3B3B",
        pointBorderColor: "#FF0000",
        pointBackgroundColor: "#FB7575",
        backgroundColor: "#FB7575"
      },
      chartOptions: {
        responsive: true,
        maintainAspectRatio: false
      },
        days: [10,20,30,60,90,120,150,180],
      selected:'',
      num : localStorage.getItem('numB'),
      // dataP:[]
      url: ''
 
    };
  },
  methods:{
    selectNum: function(){
      // return this.selected;
      console.log(this.selected);
    localStorage.setItem('numB',this.selected)
    window.location.reload();
    },
  },
  computed:{
    list() {
      return this.arrCases.map((item, i) => {
        return {A: item, B: this.arrDeaths[i]}
      })
    }
  },
  // watch:{
  async created() {
    const getNum = localStorage.getItem('numB')
    const {data} = await axios.get("https://disease.sh/v3/covid-19/historical/all?lastdays="+getNum);
    let dataP = [];
    dataP.push(data);
    // console.log('P',dataP);
    // dataP.push(data);
    dataP.forEach(d => {
      let listCase = [];
      let listDeath = [];
      listCase.push(d.cases);
      listDeath.push(d.deaths);
        let key = Object.keys(listCase[0]);
        let val = Object.values(listCase[0]);
        let deathKey = Object.keys(listDeath[0]);
        let deathVal = Object.values(listDeath[0]);

      for(let i in key){
      const date = moment(key[i], 'MM/DD/YY').format("DD/MM/YY");
      this.arrCases.push({date: date, total: val[i]})
      }
      for(let i in deathKey){
      const date = moment(deathKey[i], 'MM/DD/YY').format("DD/MM/YY");
      this.arrDeaths.push({date: date, total: deathVal[i]})

      }
console.log('Positive',this.arrCases);
console.log('Hos',this.arrDeaths);


});


  },

};

</script>

<style>
button {
  padding: 10px 20px;
  border: 1px solid #ddd;
  color: #fff;
  background-color:#13ce66;
  border-radius: 4px;
  font-size: 14px;
  font-family: '微软雅黑',arail;
  cursor: pointer;
}
select {
  position: relative;
  width: 30%;
  text-align: left;
  outline: none;
  height: 47px;
  line-height: 47px;
}

</style>
