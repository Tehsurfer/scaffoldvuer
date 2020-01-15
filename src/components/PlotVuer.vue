<template>
  <div class="plot-container">
      <div id='plot'></div>
  </div>
</template>

<script>

import CsvManager from "./csv_manager"
var csv = new CsvManager()
import Plotly from 'plotly.js-dist/plotly'

export default {
  name: "PlotVuer",
  beforeCreate: function() {
  },
  methods: {
    loadURL: function(url) {
      csv.loadFile(url).then( () => {
        this.pdata[0].x = csv.getColoumnByIndex(0).shift();
        this.pdata[0].y = csv.getColoumnByIndex(1).shift();
        this.pdata[0].type = csv.getDataType()
        this.items = csv.getHeaders();
        this.plot_channel(csv.getHeaderByIndex(1))
        return true
      });
    },
    plot_channel: function(channel){
      this.layout.title = channel
      window.pdata = this.pdata
      this.pdata[0].y = csv.getColoumnByName(channel)
      window.ppplot = this.plot
      Plotly.plot('plot', this.pdata)
    },
    react() {
      return Plotly.react('plot', this.pdata)
    }
  },
  props: { url: String},
  data: function() {
    return {
      items: ['first', 'second', 'third'],
      pdata: [{ x: ['1', '2', '3', '4'], y: [10, 25, 20, 50], type: 'scatter' }],
      layout: {
        title: "edit this title"
      },
      channel: 'Select a channel',
      plot: undefined
    };
  },
  computed: {
  },
  created(){
    this.loadURL(this.url)
  },
  mounted(){
    this.$watch('data', () => {
      this.react()
    }, { deep: !this.watchShallow })
    this.$watch('url', () => {
      this.loadURL(this.url)
    }, { deep: !this.watchShallow })
  }
};
</script>

