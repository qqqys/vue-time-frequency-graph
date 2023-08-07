<template>
    <div id="time-frequency-container">
        <div id="time-frequency-top-axis"></div>
        <div id="time-frequency-view"></div>
    </div>
</template>

<script setup lang="ts">
import * as d3 from "d3";
import { onMounted,} from "vue";
//全局变量
const props = defineProps({
    data:{
        type: Object,
        default: null
    },
    width:{
        type: Number,
        default: 1400
    },
    height:{
        type: Number,
        default: 400
    },
    marginTop:{
        type: Number,
        default: 30
    }
})
const $emit = defineEmits(['timeSelect'])


const width = 1400;
const p = 16
const height = p*data.times.length;
const freqSlice = 100;
const margin = {
    top: 30,
    bottom: 0,
    left: 60,
    right: 0,
};
const color = d3.scaleSequentialSqrt([0, d3.max(data.values, d => d3.max(d))], d3.interpolatePuRd)

const draw = ()=>{

}

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
    const slice = (d3.max(data.frequencys)-d3.min(data.frequencys))/freqSlice/2
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
    
    svg
        .append("g")
        .attr("class",'matrix')
        .on("mousemove",(e, i)=>{
            d3.select(".scan").remove()
            d3.select(`.matrix`).append("rect").attr("class", "scan").attr("x", `${margin.left}`).attr("y", d3.pointer(e, this)[1] + 1).attr("width", width).attr("height", 2)
        })
        .selectAll("g")
        .data(data.times)
        .join("g")
        .attr("transform", (d, i) => {
            return `translate(0, ${y(data.times[i])})`;
        })
        .attr("class", (d, i)=>{return `time${data.times[i]}`})
        .on("click", (e, i)=>{
            $emit("timeSelect", i)
        })
        .selectAll("rect")
        .data((d, i) => data.values[i].map((e, i)=>{return {time: data.times[i], freq: data.frequencys[i], value: e}}))
        .join("rect")
        .attr("x", (d, i) => {
            return x(d.freq)-width/freqSlice/2
        })
        .attr("width", width/freqSlice)
        .attr("height", y.bandwidth() - 1)
        .attr("fill", d => isNaN(d.value) ? "#eee" : d.value === 0 ? "#fff" : color(d.value))
        .on("mousemove",(e, d)=>{
        })
        .on("mouseoute",(e, d)=>{

        })
});
</script>

<style scoped lang="less">
#time-frequency-container{
    display: flex;
    justify-content: center;
    #time-frequency-view{
    max-height: 400px;
    overflow-y: scroll;
    #time-frequency-view{
    }
}

}

</style>
