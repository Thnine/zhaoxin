<template>
  
    <div style="display:flex;flex-direction:column;align-items:center">
        <svg ref="histo-plot" class="histo-plot"></svg>
        <b style="margin-top:5px;font-size:20px">{{drawData.name}}</b>
    </div>

</template>

<script>

import * as d3 from "d3";
import eventBus from "@/eventBus.js"


export default {
    name:"histo",
    props:['drawData'],
    data(){
        return {
            intervalNum:10,//划分的区间数
        }
    },
    methods:{
        draw(){//绘图
            const width = this.$refs['histo-plot'].clientWidth;
            const height = this.$refs['histo-plot'].clientHeight;
            const padding = 30;
            let data = this.drawData.data;

            const self = this

            const svg = d3.select(this.$refs['histo-plot']);
            svg.selectAll('*').remove()


            //准备数据
            const maxValue = Math.max(...data.map(d=>d.value));
            const minValue = Math.min(...data.map(d=>d.value));
            // const interval = 1.0 * (maxValue - minValue) / this.intervalNum;
            let intervals = []
            for(let i = 0;i < this.intervalNum;i++){
                intervals.push(
                    [
                        1.0 * (maxValue - minValue) * i / this.intervalNum + minValue,
                        1.0 * (maxValue - minValue) * (i + 1) / this.intervalNum + minValue,
                    ]
                )
            }

            function judgeIndex(value){
                if(value == maxValue){
                    return self.intervalNum - 1;
                }
                else
                    return Math.floor( 1.0 * (value - minValue) * self.intervalNum /  (maxValue - minValue) )

            }

            for(let item of data){//index标识了元素位于index-1个区间  右闭左开
                item.index = judgeIndex(item.value)
            }

            let groupby = Array.from(d3.group(data,d=>d.index),([key,value])=>({key,value}))


            const minCount = 0
            const maxCount =  Math.max(...groupby.map(d=>d.value.length))

            //准备坐标轴
            const intervalScale = d3.scaleLinear()
                .domain([minValue,maxValue])
                .range([0,width-2 * padding])
            const countScale = d3.scaleLinear()
                                .domain([maxCount,minCount])
                                .range([0,height-2*padding])

            const xtickValues = intervals.map(d=>d[0])
            xtickValues.push(maxValue)

            const xAxis = d3.axisBottom(intervalScale).tickValues(xtickValues); //TODO ticks防止错误
            const yAxis = d3.axisLeft(countScale);
            const xAxisS = svg.append('g')
                .classed("xAxis",true)
                .call(xAxis)
            const yAxisS = svg.append('g')
                .classed("yAxis",true)
                .call(yAxis)

            //move
            const xMove = padding
            const yMove = padding + yAxisS.select('.domain').node().getBoundingClientRect().height
            xAxisS.attr('transform',`translate(${xMove},${yMove})`)
            yAxisS.attr('transform',`translate(${xMove},${padding})`)


            //绘制柱状
            const bars = svg.append('g')
                .selectAll('rect')
                .data(groupby)
                .join('rect')
            
            bars
                .classed('bar',true)
                .attr('x',d=>intervalScale(intervals[d.key][0]))
                .attr('y',d=>countScale(d.value.length))
                .attr('width',1.0 * (width - 2 * padding) / this.intervalNum)
                .attr('height',d=>yMove - countScale(d.value.length) - padding)
                .attr('transform',`translate(${xMove},${padding})`)
                .attr("fill",'#59ca73')
                .attr("fill-opacity",0.7)
                .attr("stroke",'green')
                .attr("stroke-width","1px")
                .on('light',function(event,d){
                    let index = judgeIndex(data[event.detail].value)
                    //点亮第index个柱子
                    if(index == d.key){
                        d3.select(this).classed('light-bar',true)
                    }
                })
                .on('unlight',function(){
                        d3.select(this).classed('light-bar',false)
                })


        }

    },
    mounted(){
        this.draw();

    },
    watch:{

    }

}
</script>

<style>
    .histo-plot{
        width:500px;
        height: 300px;
        border: 4px solid gray;
    }
    .light-bar{
        fill:#ff3900;
        fill-opacity:0.7;
        stroke:#d43d11;
        stroke-width:1px;
    }
</style>