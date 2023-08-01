<template>
    <div id="time-frequency-container">
        <div id="time-frequency-top-axis"></div>
        <div id="time-frequency-view"></div>
    </div>
</template>

<script setup lang="ts">
import * as d3 from "d3";
import { onMounted } from "vue";
import data from "../data"

//全局变量
const width = 1200;
const p = 16
const height = p*data.times.length;
const freqSlice = 100;
const margin = {
    top: 30,
    bottom: 0,
    left: 30,
    right: 0,
};
const color = d3.scaleSequentialSqrt([0, d3.max(data.values, d => d3.max(d))], d3.interpolatePuRd)
// const datas = {
//     times: [],
//     frequencys: [],
//     values: []
// }
// for(let i=0; i<100; i++){
//     datas.times.push(new Date().getTime()+i*1000)
// }
// for(let i=0; i<=freqSlice; i++){
//     datas.frequencys.push(11950+i*(12000-11950)/freqSlice)
// }
// for(let i=0; i<100; i++){
//     let tmp = []
//     for(let j=0; j<=freqSlice; j++){
//         tmp.push(Math.random()*50)
//     }
//     datas.values.push(tmp)
// }
// console.log(JSON.stringify(datas));


onMounted(() => {
    const svg = d3
        .select("#time-frequency-view")
        .append("svg")
        .attr("width", width)
        .attr("height", height);


    const y = d3
        .scaleBand()
        .domain(data.times)
        .rangeRound([margin.top, margin.top + height]);
    const slice = (12000-11950)/freqSlice/2
    const x = d3
        .scaleLinear()
        .domain([d3.min(data.frequencys)-slice,d3.max(data.frequencys)+slice])
        .rangeRound([margin.left, width-margin.right]);
    svg
        .append("g")
        .attr("class", "y-axis")
        .attr("transform", `translate(${margin.left}, 0)`)
        .call(d3.axisLeft(y));
    svg
        .append("g")
        .attr("class", "x-axis")
        .attr("transform", `translate(0, ${margin.top})`)
        .call(d3.axisTop(x));
    console.log(x(11950));
    
    svg
        .append("g")
        .selectAll("g")
        .data(data.values)
        .join("g")
        .attr("transform", (d, i) => {
            return `translate(0, ${y(data.times[i])})`;
        })
        .selectAll("rect")
        .data((d) => d)
        .join("rect")
        .attr("x", (d, i) => x(data.frequencys[i])-1200/freqSlice/2)
        .attr(
            "width",
            (d, i) => 1200/freqSlice
        )
        .attr("height", y.bandwidth() - 1)
        .attr("fill", d => isNaN(d) ? "#eee" : d === 0 ? "#fff" : color(d))
});
</script>

<style scoped lang="less">
#time-frequency-view{
    max-height: 400px;
    overflow-y: scroll;
}
</style>
