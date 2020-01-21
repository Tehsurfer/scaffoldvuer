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
    <el-select ref="selectBox" v-model="channel" @change="traceEvent($event)"     
    multiple
    filterable
    allow-create
    default-first-option
    placeholder="Add and remove data to the plot">
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
      csv: new CsvManager(),
      items: ['first', 'second', 'third'],
      pdata: [{ x: [], y: [], type: 'scatter' }],
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
      this.csv.loadFile(url).then(() => {
        let x = this.csv.getColoumnByIndex(0)
        x.shift()
        let y = this.csv.getColoumnByIndex(1)
        y.shift()
        this.pdata[0].x = x;
        this.pdata[0].y = y;
        this.pdata[0].type = this.csv.getDataType()
        this.items = this.csv.getHeaders();
        this.plot(this.csv.getHeaderByIndex(1))
        
        return true
      });
    },
    plot: function(channel){
      window.pdata = this.pdata
      let y = this.csv.getColoumnByName(channel)
      y.shift()
      this.pdata[0].y = y
    },
    traceEvent: function(selectList){
      window.selectList = []
      this.pdata = []
      for(let i in selectList) {
        this.pdata.push({})
        let x = this.csv.getColoumnByIndex(0)
        x.shift()
        let y = this.csv.getColoumnByName(selectList[i])
        y.shift()
        this.pdata[i].x = x;
        this.pdata[i].y = y;
        this.pdata[i].name = selectList[i]
        this.pdata[i].type = this.csv.getDataType()
        window.selectList.push(selectList[i])

      }
      window.ppdata = selectList
    },
    plotAsHeatmap: function(event){
      if (event){
        let x = this.csv.getColoumnByIndex(0)
        let y = this.csv.getHeaders()
        x.shift()
        y.shift()
        this.pdata = [{
          z: [...this.csv.getAllData()],
          x: x,
          y: y,
          type: 'heatmap'
        }];
      } else {
        let x = this.csv.getColoumnByIndex(0)
        x.shift()
        let y = this.csv.getColoumnByIndex(1)
        y.shift()
        this.pdata[0].x = x;
        this.pdata[0].y = y;
        this.pdata[0].type = 'bar'
        this.pdata[0].z = undefined
        this.plot(this.csv.getHeaderByIndex(1))
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