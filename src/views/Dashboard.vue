<script setup>
import { useLayout } from '@/layout/composables/layout';
import { computed, ref, watch } from 'vue';

const { layoutConfig } = useLayout();

let documentStyle = getComputedStyle(document.documentElement);
let textColor = documentStyle.getPropertyValue('--text-color');
let textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
let surfaceBorder = documentStyle.getPropertyValue('--surface-border');

const models = ref(['Mixtral', 'GPT-4']);
const selectedModels = ref(['Mixtral', 'GPT-4']);

const tasks = ref([
    {
        name: 'MeQSum',
        metrics: ['R-L', 'R-1', 'R-2', 'BERTScore'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [35.5, 35.5, 35.5, 35.5]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [53.2, 53.2, 53.2, 53.2]
            }
        ]
    },
    {
        name: 'Problem Summary',
        metrics: ['R-1'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [12.3]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [18.8]
            }
        ]
    },
    {
        name: 'MedNLI',
        metrics: ['R-1'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [12.3]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [18.8]
            }
        ]
    },
    {
        name: 'LongHealth',
        metrics: ['R-1'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [12.3]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [18.8]
            }
        ]
    },
    {
        name: 'MeDiSumQA',
        metrics: ['R-1'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [12.3]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [18.8]
            }
        ]
    },
    {
        name: 'MeDiSumCode',
        metrics: ['R-1'],
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: [12.3]
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: [18.8]
            }
        ]
    }
]);

const selectedTask = ref(tasks.value[0]);
const selectedMetric = ref(selectedTask.value.metrics[0]);

const setColorOptions = () => {
    documentStyle = getComputedStyle(document.documentElement);
    textColor = documentStyle.getPropertyValue('--text-color');
    textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
    surfaceBorder = documentStyle.getPropertyValue('--surface-border');
};

const barData = (task, metric) => {
    let labels = [];
    let datasets = [];
    if (selectedModels.value == null) {
        return {
            labels: labels,
            datasets: datasets
        };
    }

    labels.push(task.name);

    for (var result of task.results) {
        if (selectedModels.value.includes(result.label)) {
            let metricIndex = task.metrics.findIndex((elem) => elem == metric);
            console.log(task);
            console.log(metric);
            console.log(metricIndex);
            console.log([result.value[metricIndex]]);
            datasets.push({
                label: result.label,
                data: [result.value[metricIndex]],
                backgroundColor: getColor(task, metric, result)
            });
        }
    }
    return {
        labels: labels,
        datasets: datasets
    };
};

function getColor(task, metric, result) {
    return result.color;
}

const barOptions = ref({
    plugins: {
        legend: {
            labels: {
                fontColor: textColor
            }
        }
    },
    scales: {
        x: {
            ticks: {
                color: textColorSecondary,
                font: {
                    weight: 500
                }
            },
            grid: {
                display: false,
                drawBorder: false
            }
        },
        y: {
            ticks: {
                color: textColorSecondary
            },
            grid: {
                color: surfaceBorder,
                drawBorder: false
            }
        }
    }
});

watch(
    layoutConfig.theme,
    () => {
        setColorOptions();
    },
    { immediate: true }
);
</script>

<template>
    <div class="w-full h-full flex flex-column align-items-center">
        <div class="w-full lg:w-6">
            <MultiSelect class="w-full" v-model="selectedModels" display="chip" :options="models" placeholder="Select Models" filter> </MultiSelect>
            <div class="card w-full my-3">
                <div class="w-full flex align-items-end align-content-start">
                    <h5 class="m-0">{{ selectedTask.name }} ({{selectedMetric}})</h5>
                    <div class="flex-grow-1"></div>
                    <Dropdown class="mx-3" v-model="selectedTask" :options="tasks" optionLabel="name" placeholder="Select Task" />
                    <Dropdown v-model="selectedMetric" :options="selectedTask.metrics" placeholder="Select Metric" />
                </div>
                <Chart type="bar" :data="barData(selectedTask, selectedMetric)" :options="barOptions"></Chart>
            </div>
        </div>
    </div>
</template>
