<template>
    <div class="bp-bar-chart" ref="chart"></div>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue';
import * as echarts from 'echarts';

const props = defineProps({
    line_name: {
        type: String,
        required: true,
    },
    data1: {
        type: Number,
        required: true,
    },
    data2: {
        type: Number,
        required: true,
    },
    obj1: {
        type: String,
        required: true,
    },
    obj2: {
        type: String,
        required: true,
    },
    color1: {
        type: String,
        required: true,
    },
    color2: {
        type: String,
        required: true,
    },
    selectedItem: {
        type: Number,
        required: true, // 传递过来的选中项
    }
});

const chart = ref(null);
const myChart = ref(null);

const renderChart = () => {
    if (myChart.value) {
        myChart.value.dispose();
    }

    const chartDom = chart.value;
    myChart.value = echarts.init(chartDom);

    const option = {
        background: '',
        tooltip: {
            trigger: 'axis',
            axisPointer: { type: 'shadow' }
        },
        legend: {
            data: [props.obj1, props.obj2],
            orient: 'horizontal',
            itemGap: 10,
            bottom: '30%',
            left: '50vw',
            show: false, // 初始隐藏 legend
        },
        grid: {
            left: '3%',
            right: '4%',
            top: '20%',
            bottom: '10%',
            containLabel: true
        },
        xAxis: {
            type: 'value',
            axisLabel: { show: false },
            splitLine: { show: false },
            min: 0,
            max: props.data1 + props.data2
        },
        yAxis: {
            type: 'category',
            data: [props.line_name],
            axisTick: { show: false },
            axisLabel: {
                color: '#ffffff', // 设置 y 轴标签字体颜色为白色
                fontSize: 16,
            }
        },
        series: [
            {
                name: props.obj1,
                type: 'bar',
                stack: 'total',
                label: {
                    show: true,
                    fontSize: 16,
                    formatter: (params) => params.value === 0 ? '' : params.value,
                },
                emphasis: { focus: 'series' },
                data: [props.data1],
                itemStyle: {
                    color: props.color1,
                }
            },
            {
                name: props.obj2,
                type: 'bar',
                stack: 'total',
                label: {
                    show: true,
                    fontSize: 16,
                    formatter: (params) => params.value === 0 ? '' : params.value,
                },
                emphasis: { focus: 'series' },
                data: [props.data2],
                itemStyle: {
                    color: props.color2,
                }
            }
        ]
    };

    myChart.value.setOption(option);
    window.addEventListener('resize', () => myChart.value.resize());
};

// 监听 selectedItem，触发图例切换
watch(() => props.selectedItem, (newVal) => {
    if (newVal) {
        handleLegendToggle(newVal);
    }
});
// 监听 obj1 或 obj2 的变化，重新绘制图表
watch([() => props.obj1, () => props.obj2], () => {
    renderChart(); // 当 obj1 或 obj2 改变时重新绘制图表
});

const handleLegendToggle = (objid) => {
    if (objid === 1 || objid === 3) {
        myChart.value.dispatchAction({
            type: 'legendToggleSelect',
            name: props.obj1
        });
    } else {
        myChart.value.dispatchAction({
            type: 'legendToggleSelect',
            name: props.obj2
        });
    }
};

onMounted(() => {
    renderChart();
});
</script>

<style scoped>
.bp-bar-chart {
    margin-left: 2vw;
    width: 27vw;
    height: 5vh;
}
</style>
