<template>
  <div id="app">
        <ScaffoldVuer url="https://mapcore-bucket1.s3-us-west-2.amazonaws.com/others/a3/a3_metadata.json" ref="scaffold" showColourPicker/>

     <vue-draggable-resizable :w="1000" :h="1000" @dragging="onDrag" @resizing="onResize" :parent="true">
  
      <PlotVuer :url="urlList[0]" :height="height/2" :plotType="plotTypeList[0]"></PlotVuer>
      <PlotVuer :url="urlList[1]" :height="height/2" :plotType="plotTypeList[1]"></PlotVuer>
    
    
    </vue-draggable-resizable>
    <el-input class='element' placeholder="Enter url" v-model="url"></el-input>
  </div>
</template>

<script>
import Input from "element-ui";
import "element-ui/lib/theme-chalk/index.css";
import Vue from 'vue'
import VueDraggableResizable from 'vue-draggable-resizable'

// optionally import default styles
import 'vue-draggable-resizable/dist/VueDraggableResizable.css'
Vue.component('vue-draggable-resizable', VueDraggableResizable)
Vue.use(Input);
import PlotVuer from './components/PlotVuer'
import ScaffoldVuer from './components/ScaffoldVuer.vue'

export default {
  name: 'app',
  components: {
    ScaffoldVuer,
    PlotVuer,
   VueDraggableResizable 
  },
  data: function(){
    return {
      urlList: ['https://mapcore-bucket1.s3-us-west-2.amazonaws.com/ISAN/csv-data/use-case-4/RNA_Seq.csv', 'https://mapcore-bucket1.s3-us-west-2.amazonaws.com/ISAN/csv-data/use-case-2/Sample_1_18907001_channel_1.csv'],
      width: 0,
      height: 1000,
      plotTypeList: ["heatmap2", "scatter"],
      y: 0
    }
  },
    methods: {
    onResize: function (x, y, width, height) {
      this.x = x
      this.y = y
      this.width = width
      this.height = height
    },
    onDrag: function (x, y) {
      this.x = x
      this.y = y
    }
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
html{
    background: url('../images/background.jpg') no-repeat 0 0 scroll;
    background-color:#0C0C0C;
    background-size: 100% 100%;
    height:1080px;
    width:1920px;
}
</style>
