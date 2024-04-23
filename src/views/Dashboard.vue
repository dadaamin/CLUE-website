<script setup>
import { useLayout } from '@/layout/composables/layout';
import { computed, ref, watch } from 'vue';

const { layoutConfig } = useLayout();

let documentStyle = getComputedStyle(document.documentElement);
let textColor = documentStyle.getPropertyValue('--text-color');
let textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
let surfaceBorder = documentStyle.getPropertyValue('--surface-border');

const lineData = ref(null);

const tasks = ref(["summarization", "QA"])
const results = ref([
    {label: "Mixtral", color: "#50514F",  tasks: [{score: "F1", value: 35.5, name: "summarization"}, {score: "R-1", value: 12.3, name: "QA"}]},
    {label: "GPT-4", color: "#F25F5C", tasks: [{score: "F1", value: 53.2, name: "summarization"}, {score: "R-1", value: 18.8, name: "QA"}]}
])

const models = computed(() => {
    return results.value.map(val => ({name: val.label}))
})

const multiselectValue = ref(null);
const setColorOptions = () => {
    documentStyle = getComputedStyle(document.documentElement);
    textColor = documentStyle.getPropertyValue('--text-color');
    textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
    surfaceBorder = documentStyle.getPropertyValue('--surface-border');
};

const barData = computed(() => {
    let labels = [];
    let datasets = [];
    if (multiselectValue.value == null) {
        return {
        labels: labels,
        datasets: datasets
    };
    }
    
    let selectedModels = multiselectValue.value.map((val) => val.name);

    for (var task of tasks.value) {
                labels.push(task);
            }
    for (var result of results.value) {
        if (selectedModels.includes(result.label)) {
            let task_results = []
            for (var task_result of result.tasks) {
                task_results.push(task_result.value)
            }
            datasets.push({
                label: result.label,
                data: result.value,
                backgroundColor: result.color,
                data: task_results
            });
        }
    }
    return {
        labels: labels,
        datasets: datasets
    };
});

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
    })

watch(
    layoutConfig.theme,
    () => {
        setColorOptions();
    },
    { immediate: true }
);
</script>

<template>
    <MultiSelect v-model="multiselectValue" :options="models" optionLabel="name" placeholder="Select Countries" :filter="true">
        <template #value="slotProps">
            <div class="inline-flex align-items-center py-1 px-2 bg-primary text-primary border-round mr-2" v-for="option of slotProps.value" :key="option.code">
                <div>{{ option.name }}</div>
            </div>
            <template v-if="!slotProps.value || slotProps.value.length === 0">
                <div class="p-1">Select Countries</div>
            </template>
        </template>
        <template #option="slotProps">
            <div class="flex align-items-center">
                <div>{{ slotProps.option.name }}</div>
            </div>
        </template>
    </MultiSelect>
    <div class="col-12 xl:col-6">
        <div class="card">
            <h5>Bar Chart</h5>
            <Chart type="bar" :data="barData" :options="barOptions"></Chart>
        </div>
    </div>
</template>
