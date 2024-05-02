<script setup>
import { useLayout } from '@/layout/composables/layout';
import { computed, ref, watch } from 'vue';

const { layoutConfig } = useLayout();

let documentStyle = getComputedStyle(document.documentElement);
let textColor = documentStyle.getPropertyValue('--text-color');
let textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
let surfaceBorder = documentStyle.getPropertyValue('--surface-border');

const lineData = ref(null);
const dropdownValue = ref(null);


// const tasks = ref(['summarization', 'QA']);
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
        metric: 'R-1',
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: 12.3
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: 18.8
            }
        ]
    },
    {
        name: 'MedNLI',
        metric: 'R-1',
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: 12.3
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: 18.8
            }
        ]
    },
    {
        name: 'LongHealth',
        metric: 'R-1',
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: 12.3
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: 18.8
            }
        ]
    },
    {
        name: 'MeDiSumQA',
        metric: 'R-1',
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: 12.3
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: 18.8
            }
        ]
    },
    {
        name: 'MeDiSumCode',
        metric: 'R-1',
        results: [
            {
                label: 'Mixtral',
                color: '#50514F',
                value: 12.3
            },
            {
                label: 'GPT-4',
                color: '#F25F5C',
                value: 18.8
            }
        ]
    }
]);

const models = computed(() => {
    return tasks.value[0].results.map((val) => ({ name: val.label }));
});


const multiselectValue = ref(null);
const setColorOptions = () => {
    documentStyle = getComputedStyle(document.documentElement);
    textColor = documentStyle.getPropertyValue('--text-color');
    textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
    surfaceBorder = documentStyle.getPropertyValue('--surface-border');
};

const barData = (index) => {
    let labels = [];
    let datasets = [];
    if (multiselectValue.value == null) {
        return {
            labels: labels,
            datasets: datasets
        };
    }

    let selectedModels = multiselectValue.value.map((val) => val.name);

    labels.push(tasks.value[index].name);

    for (var result of tasks.value[index].results) {
        if (selectedModels.includes(result.label)) {
            datasets.push({
                label: result.label,
                data: [result.value],
                backgroundColor: result.color
            });
        }
    }
    return {
        labels: labels,
        datasets: datasets
    };
};

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
        <MultiSelect v-model="multiselectValue" :options="models" optionLabel="name" placeholder="Select Models" :filter="true">
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
        <div class="grid">
            <template v-for="(task, index) in tasks" :key="index">
                <div class="col-6">
                    <div class="card">
                        <Dropdown v-model="dropdownValue" :options="task.metrics" placeholder="Select Metric" />
                        <h5>{{ task.name }}</h5>
                        <Chart type="bar" :data="barData(index)" :options="barOptions"></Chart>
                    </div>
                </div>
            </template>
        </div>
    </div>
</template>
