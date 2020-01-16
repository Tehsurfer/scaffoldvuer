<template>
  <div class="plot-container">
      
      <div class="resize-drag">
        resize me!
        <div class='plot' id="plot" ref="plotRef"></div>
        </div>
  </div>
  
</template>

<script>

import CsvManager from "./csv_manager"
var csv = new CsvManager()
import Plotly from 'plotly.js-dist/plotly'
import interact from 'interactjs'
import ReziseSensor from 'css-element-queries/src/ResizeSensor'

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
      Plotly.plot(this.$refs.plotRef, this.pdata)
    },
    react() {
      return Plotly.react(this.$refs.plotRef, this.pdata)
    },
    handleResize: function (){
      new ReziseSensor(this.$el.querySelector('.resize-drag'), ()=>{
        Plotly.relayout(this.$refs.plotRef,{
          width: this.$el.querySelector('.resize-drag').width,
          height: this.$el.querySelector('.resize-drag').height})
      })
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
    interact('.resize-drag')
       .resizable({
    // resize from all edges and corners
    edges: { left: true, right: true, bottom: true, top: true },

    modifiers: [
      // keep the edges inside the parent
      interact.modifiers.restrictEdges({
        outer: 'parent'
      }),

      // minimum size
      interact.modifiers.restrictSize({
        min: { width: 100, height: 50 }
      })
    ],

    inertia: true
  })
  .draggable({
    onmove: window.dragMoveListener,
    inertia: true,
    modifiers: [
      interact.modifiers.restrictRect({
        restriction: 'parent',
        endOnly: true
      })
    ]
  })
  .on('resizemove', function (event) {
    var target = event.target
    var x = (parseFloat(target.getAttribute('data-x')) || 0)
    var y = (parseFloat(target.getAttribute('data-y')) || 0)

    // update the element's style
    target.style.width = event.rect.width + 'px'
    target.style.height = event.rect.height + 'px'

    // translate when resizing from top or left edges
    x += event.deltaRect.left
    y += event.deltaRect.top

    target.style.webkitTransform = target.style.transform =
        'translate(' + x + 'px,' + y + 'px)'

    target.setAttribute('data-x', x)
    target.setAttribute('data-y', y)
    
    
  })
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.resize-drag {
  /* background-color: #29e;
  color: white; */
  font-size: 20px;
  font-family: sans-serif;
  border-radius: 8px;
  padding: 20px;
  touch-action: none;

  width: 820px;

  /* This makes things *much* easier */
  box-sizing: border-box;
}
</style>
