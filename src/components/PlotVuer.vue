<template>
  <div class="plotvuer_parent" :title="collapseName">
    <div class="options">
    <el-collapse v-model="ccheckbox">
          <el-collapse-item
            :title="collapseName"
            >
             <el-checkbox-group v-model="ccheckbox" size="small">
              <el-row v-for="item in list2" :key="item">
                <div style= "display: flex;justify-content: space-between;">
                  <el-checkbox
                    style="margin-top:3px;"
                    :label="item"
                    :checked="false"
                    @change="plotAsHeatmap($event)"
                    border
                  >{{item}}</el-checkbox>
                    </div>
              </el-row>
            </el-checkbox-group>
          </el-collapse-item>
    </el-collapse>
    </div>
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
import {Select, Option, Collapse, CollapseItem} from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import CsvManager from "./csv_manager"
import ReziseSensor from 'css-element-queries/src/ResizeSensor'

var csv = new CsvManager()

Vue.use(Select)
Vue.use(Option)
Vue.use(Collapse)
Vue.use(CollapseItem);
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
      channel: 'Select a channel',
      collapseName: 'Options',
      list2: ['Plot As Heatmap', 'Export as CSV'],
      ccheckbox: [1,2,3]
    }
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
      window.pdata = this.pdata
      this.pdata[0].y = csv.getColoumnByName(channel)
    },
    plotAsHeatmap: function(event){
      if (event){
        this.pdata = [{
          z: csv.getAllData(),
          x: csv.getColoumnByIndex(0).shift(),
          y: csv.getHeaders().shift(),
          type: 'heatmap'
        }];
      } else {
        this.pdata[0].x = csv.getColoumnByIndex(0).shift();
        this.pdata[0].y = csv.getColoumnByIndex(1).shift();
        this.pdata[0].type = 'bar'
      }
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
<style scoped>
.options {
  position: absolute;
  z-index: 11000;
  top: 10px;
  left: 10px;
  height: calc(100% - 20px);
  text-align: left;
  overflow:auto;

}
</style>