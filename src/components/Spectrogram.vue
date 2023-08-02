<template>
    <div id="spectrogram-container">
        <div id="spectrogram-view"></div>
    </div>
</template>

<script setup lang="ts">
import * as d3 from "d3";
import { onMounted, ref } from "vue";
import data from "../data"

//全局变量
const width = 1400;
const height = 400
const freqSlice = 100;
const margin = {
    top: 30,
    bottom: 0,
    left: 60,
    right: 0,
};
// const color = d3.scaleSequentialSqrt([0, d3.max(data.values, d => d3.max(d))], d3.interpolatePuRd)

function draw(timeSelect: any){
    let index = data.times.indexOf(timeSelect)
    if(index === -1){
        return
    }
    let lineData = []
    for(let i=0;i<data.values[index].length;i++){
        lineData.push({freq: data.frequencys[i], dbm: data.values[index][i]})
    }
    d3.select(".spectrogram-svg").remove()
    const y = d3
    .scaleLinear()
    .domain([d3.min(data.values[index]), d3.max(data.values[index])])
    .rangeRound([margin.top, margin.top + height]);
    const slice = (d3.max(data.frequencys)-d3.min(data.frequencys))/freqSlice/2

    const x = d3
        .scaleLinear()
        .domain([d3.min(data.frequencys)-slice,d3.max(data.frequencys)+slice])
        .rangeRound([margin.left, width-margin.right]);
    const svg = d3
        .select("#spectrogram-view")
        .append("svg")
        .attr("class","spectrogram-svg")
        .attr("width", width)
        .attr("height", height);    

    svg
        .append("g")
        .attr("class", "x-axis")
        .attr("transform", `translate(0, ${margin.top})`)
        .call(d3.axisTop(x));
    svg
        .append("g")
        .attr("class", "y-axis")
        .attr("transform", `translate(${margin.left}, 0)`)
        .call(d3.axisLeft(y));
    const line = d3
        .line()
        .x(d=>x(d.freq))
        .y(d=>y(d.dbm))

    svg.append("path")
      .attr("fill", "none")
      .attr("stroke", "steelblue")
      .attr("stroke-width", 1.5)
      .attr("d", line(lineData));
}
defineExpose({
    draw
})
</script>

<style scoped lang="less">
#spectrogram-container{
    // width: 100vw;
    // max-height: 400px;
    display: flex;
    justify-content: center;
}
</style>