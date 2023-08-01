<template>
    <div id="time-frequency-container">
        <div id="time-frequency-top-axis"></div>
        <div id="time-frequency-view"></div>
    </div>
</template>

<script setup lang="ts">
import * as d3 from "d3";
import { onMounted } from "vue";
const width = 1000;
const height = 400;
const margin = {
    top: 0,
    bottom: 30,
    left: 30,
    right: 30,
};
onMounted(() => {
    const svg = d3
        .select("#time-frequency-view")
        .append("svg")
        .attr("width", width)
        .attr("height", height);
    const data = {
        times: [],
        frequencys: [],
        values: [],
    };
    for (let i = 1; i <= 100; i++) {
        data.times.push(i)
    }
    for (let j = 1; j <= 1000; j++) {
        data.frequencys.push(j)
    }
    for (let i = 1; i <= 100; i++) {
        let tmp = []
        for (let j = 1; j <= 1000; j++) {
            tmp.push(Math.random() * 100)
        }
        data.values.push(tmp)
    }
    console.log(data);

    const y = d3
        .scaleBand()
        .domain(data.times)
        .rangeRound([margin.top, margin.top + height]);
    const x = d3
        .scaleLinear()
        .domain([d3.min(data.frequencys), d3.max(data.frequencys) + 1])
        .rangeRound([margin.left, width - margin.right]);
    svg
        .append("g")
        .attr("class", "y-axis")
        .attr("transform", `translate(${margin.left}, 0)`)
        .call(d3.axisLeft(y));
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
        .attr("x", (d, i) => x(data.frequencys[i]) + 1)
        .attr(
            "width",
            (d, i) => x(data.frequencys[i] + 1) - x(data.frequencys[i]) - 1
        )
        .attr("height", y.bandwidth() - 1)
        .attr("fill", "#eee");
});
</script>

<style scoped lang="less"></style>
