<template>
  <div class="plotvuer_parent">
    <vue-plotly
      class ='chart'
      ref="plotly"
      :data="pdata"
      :layout="layout"
      :autoResize="true"
    />
    <el-select ref="selectBox" v-model="channel" @change="plot(channel)"  placeholder="Select a channel">
      <el-option
        v-for="item in items"
        :key="item"
        :label="item"
        :value="item">
      </el-option>
    </el-select>
  </div>
</template>
<script>
import VuePlotly from "@statnett/vue-plotly"
import Vue from "vue"
import {Select, Option} from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import CsvManager from "./csv_manager"
import ReziseSensor from 'css-element-queries/src/ResizeSensor'

var csv = new CsvManager()

Vue.use(Select)
Vue.use(Option)
export default {
  name: "PlotVuer",
  components: { VuePlotly },
  props: ["url", "height"],
  data: function() {
    return {
      items: ['first', 'second', 'third'],
      pdata: [{ x: ['1', '2', '3', '4'], y: [10, 25, 20, 50], type: 'scatter' }],
      layout: {
        title: "edit this title",
        paper_bgcolor:'rgba(0,0,0,0)',
        plot_bgcolor:'rgba(0,0,0,0)'
      },
      channel: 'Select a channel'
    };
  },
  methods: {
    loadURL: function(url) {
      csv.loadFile(url).then(() => {
        this.pdata[0].x = csv.getColoumnByIndex(0).shift();
        this.pdata[0].y = csv.getColoumnByIndex(1).shift();
        this.pdata[0].type = csv.getDataType()
        this.items = csv.getHeaders();
        this.plot(csv.getHeaderByIndex(1))
        return true
      });
    },
    plot: function(channel){
      this.layout.title = channel
      window.pdata = this.pdata
      this.pdata[0].y = csv.getColoumnByName(channel)
    },
    handleResize: function (){
      new ReziseSensor(this.$el, ()=>{
        window.helppp = this.$el
        window.hhhh = this.$refs.selectBox
        window.hh = this.height
        var newHeight = this.height - this.$refs.selectBox.$el.clientHeight
        this.layout.title = 'Width now:' + this.$el.clientWidth + ' Height now: ' + newHeight
        this.$refs.plotly.relayout({
          width: this.$el.clientWidth,
          height: newHeight})
      })
    }
  },
  mounted(){
    this.handleResize()
    
  },
  created() {
    this.loadURL(this.url)
  },
  destroyed() {
  },
};
</script>