<template>
  <div id="app">
    <div class="histo-row">
      <histo
          v-for="measure in measures"
          :key="measure.name"
          :drawData="measure">
      </histo>
    </div>
    <div class="scatter-row">
      <scatter 
          v-for="method in methods"
          :key="method.name"
          :drawData="method"
          class="scatter">
      </scatter>
    </div>

  </div>
</template>

<script>
import scatter from '@/components/scatter.vue'
import histo from "@/components/histo.vue"
import * as d3 from "d3";

export default {
  name: 'App',
  data(){
    return {
      measures:{},
      methods:{},
    }
  },
  
  components: {
    scatter,
    histo,
  },
  mounted(){
      //读取数据
      d3.json("static/data/method.json").then(data=>{
        this.methods = data
      })

      d3.json("static/data/measure.json").then(data=>{
        this.measures = data
        console.log(this.measures)
      })

      

  }
}

</script>


<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.histo-row{
  display: flex;
  justify-content:space-around;
  margin-bottom:100px;
  flex-wrap: wrap;
}

.scatter-row{
  display: flex;
  justify-content:space-around;
  flex-wrap: wrap;
}


.scatter{
}
</style>
