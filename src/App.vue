<template>
  <div id="app">
    <PlotVuer url='https://mapcore-bucket1.s3-us-west-2.amazonaws.com/ISAN/csv-data/stellate/sample_2/cell_1/18525003_channel_1.csv'></PlotVuer>
  </div>
</template>

<script>

import PlotVuer from './components/PlotVuer'
export default {
  name: 'app',
  components: {
    PlotVuer
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
  height:100%;
  width: 100%;
  position:absolute;
}

body {
  margin: 0px;
}
</style>
