<template>
  
    <div style="display:flex;flex-direction:column;align-items:center">
        <svg ref="scatter-plot" class="scatter-plot"></svg>
        <b style="margin-top:5px;font-size:20px">{{drawData.name}}</b>
    </div>

</template>

<script>

import * as d3 from "d3";
import eventBus from "@/eventBus.js"

export default {
    name:"sactter",
    props:['drawData'],
    data(){
        return {

        }
    },
    methods:{
        draw(){//绘图
            const width = this.$refs['scatter-plot'].clientWidth;
            const height = this.$refs['scatter-plot'].clientWidth;


            const padding = 10;
            const data = this.drawData.data;
            let minX = Math.min(...data.map(d=>d.x));
            let maxX = Math.max(...data.map(d=>d.x));
            let minY = Math.min(...data.map(d=>d.y));
            let maxY = Math.max(...data.map(d=>d.y));


            const posXScale = d3
                .scaleLinear()
                .domain([minX, maxX])
                .range([padding, width -  padding]);
            const posYScale = d3
                .scaleLinear()
                .domain([minY, maxY])
                .range([padding, height - padding]);
            
            const self = this;

            const svg = d3.select(this.$refs['scatter-plot'])
            svg.selectAll("*").remove()

            const circles = svg.append('g')
                .selectAll('circle')
                .data(data)
                .join('circle')
                .classed('dot',true)
            

            circles
                .attr("cx",(d,i)=>posXScale(d.x))
                .attr("cy",(d,i)=>posYScale(d.y))
                .attr("r",5)
                .attr("fill",'#59ca73')
                .attr("fill-opacity",0.7)
                .attr("stroke",'green')
                .attr("stroke-width","1px")
                .on('mouseover',function(event,d){
                    const e = circles.nodes()
                    const i = e.indexOf(this)
                    console.log(i,'i')
                    d3.selectAll('.bar').dispatch('light',{
                        'detail':i
                    })
                    d3.selectAll('.dot').dispatch('light',{
                        'detail':i
                    })
                })
                .on("mouseout",(event,d)=>{
                    d3.selectAll('.bar').dispatch('unlight')
                    d3.selectAll('.dot').dispatch('unlight')
                })
                .on('light',function(event,d){
                    const e = circles.nodes()
                    const i = e.indexOf(this)
                    if(i == event.detail){
                        d3.select(this).classed('light-dot',true)
                    }
                })
                .on('unlight',function(){
                    d3.select(this).classed('light-dot',false)
                })

        }

    },
    mounted(){

        this.draw()
    },
    watch:{

    }

}
</script>

<style>
    .scatter-plot{
        width:300px;
        height: 300px;
        border: 4px solid gray;
    }
    .light-dot{
        fill:#ff3900;
        fill-opacity:0.7;
        stroke:#d43d11;
        stroke-width:1px;
    }
</style>