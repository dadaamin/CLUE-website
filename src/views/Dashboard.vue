<script setup>
import { useLayout } from '@/layout/composables/layout';
import { computed, ref, watch } from 'vue';

const { layoutConfig } = useLayout();

let documentStyle = getComputedStyle(document.documentElement);
let textColor = documentStyle.getPropertyValue('--text-color');
let textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
let surfaceBorder = documentStyle.getPropertyValue('--surface-border');

const is_small_device = computed(() => {
    return window.innerWidth < 720;
});

const colors = [
    '#8CB0A4',
    '#C6B786',
    '#8C8CFF',
    '#FF9E7A',
    '#BFA5A5',
    '#9698D2',
    '#B3B9D7',
    '#A2B8B5',
    '#D5B1B0',
    '#BEB888',
    '#9797FF',
    '#FFBFAA',
    '#81BAA4',
    '#A978FF',
    '#A8C697',
    '#CCD488',
    '#FFA78D',
    '#AFC48F',
    '#D0A9B3',
    '#CBB99D',
    '#8290D2',
    '#FF8A8A',
    '#978878',
    '#90B2D7',
    '#7AB8A1',
    '#FFB985',
    '#D4B3A5',
    '#8BA78B',
    '#8AA3C7',
    '#9797E0'
];

const models = ref([
    'llama2-7b',
    'meditron-7b',
    'llama2-7b-hf',
    'Mistral-7B-v0.1',
    'internistai/base-7b-v0.2',
    'BioMistral-7B',
    'BioMistral-7B-DARE',
    'Mistral-7B-Instruct-v0.1',
    'zephyr-7b-beta',
    'Mistral-7B-Instruct-v0.2',
    'Meta-Llama-3-8B-Instruct',
    'Llama3-OpenBioLLM-8B',
    'JSL-Med-Sft-Llama-3-8B',
    'JSL-MedLlama-3-8B-v1.0',
    'JSL-MedLlama-3-8B-v2.0',
    'Llama3-Aloe-8B-Alpha',
    'Phi-3-mini-128k-instruct',
    'Mixtral-8x7B-v0.1',
    'Mixtral-8x7B-Instruct-v0.1',
    'Llama-2-70b',
    'meditron-70b',
    'ClinicalCamel-70B',
    'Llama-2-70b-chat',
    'Meta-Llama-3-70B-Instruct',
    'Llama3-OpenBioLLM-70B',
    'Mixtral-8x22B-Instruct-v0.1'
]);

const detectMobileDevice = () => {
    const toMatch = [/Android/i, /webOS/i, /iPhone/i, /iPad/i, /iPod/i, /BlackBerry/i, /Windows Phone/i];

    return toMatch.some((toMatchItem) => {
        return navigator.userAgent.match(toMatchItem);
    });
};
const isMobile = detectMobileDevice();
const selectedModels = isMobile ? ref(['Llama3-OpenBioLLM-70B', 'Meta-Llama-3-70B-Instruct']) : ref(['Llama3-OpenBioLLM-8B', 'Meta-Llama-3-8B-Instruct', 'Llama3-OpenBioLLM-70B', 'Meta-Llama-3-70B-Instruct']);
const colorMap = models.value.reduce((acc, model, index) => {
    acc[model] = colors[index % colors.length];
    return acc;
}, {});

const modelFamilies = {
    'Mistral-7B-v0.1': ['Mistral-7B-v0.1', 'internistai/base-7b-v0.2', 'zephyr-7b-beta'],
    'Mistral-7B-Instruct-v0.1': ['Mistral-7B-Instruct-v0.1', 'BioMistral-7B', 'BioMistral-7B-DARE'],
    'Mistral-7B-Instruct-v0.2': ['Mistral-7B-Instruct-v0.2'],
    'Meta-Llama-3-8B-Instruct': ['Meta-Llama-3-8B-Instruct', 'Llama3-OpenBioLLM-8B', 'JSL-Med-Sft-Llama-3-8B', 'JSL-MedLlama-3-8B-v1.0', 'JSL-MedLlama-3-8B-v2.0', 'Llama3-Aloe-8B-Alpha'],
    'Phi-3-mini-128k-instruct': ['Phi-3-mini-128k-instruct'],
    'Mixtral-8x7B-Instruct-v0.1': ['Mixtral-8x7B-Instruct-v0.1'],
    'Meta-Llama-3-70B-Instruct': ['Meta-Llama-3-70B-Instruct', 'Llama3-OpenBioLLM-70B'],
    'Mixtral-8x22B-Instruct-v0.1': ['Mixtral-8x22B-Instruct-v0.1']
};

const modelScores = {
    'llama2-7b': [20.11],
    'meditron-7b': [11.46],
    'llama2-7b-hf': [36.94],
    'Mistral-7B-v0.1': [33.99, 9.55],
    'internistai/base-7b-v0.2': [38.24, 28.64],
    'BioMistral-7B': [40.57, 23.83],
    'BioMistral-7B-DARE': [43.24, 25.82],
    'Mistral-7B-Instruct-v0.1': [40.31, 23.12],
    'zephyr-7b-beta': [42.69, 28.17],
    'Mistral-7B-Instruct-v0.2': [46.45, 38.94],
    'Meta-Llama-3-8B-Instruct': [48.37, 40.61],
    'Llama3-OpenBioLLM-8B': [33.2, 25.44],
    'JSL-Med-Sft-Llama-3-8B': [19.0, 15.68],
    'JSL-MedLlama-3-8B-v1.0': [17.0, 29.94],
    'JSL-MedLlama-3-8B-v2.0': [31.26, 18.02],
    'Llama3-Aloe-8B-Alpha': [42.5, 30.31],
    'Phi-3-mini-128k-instruct': [41.7, 24.39],
    'Mixtral-8x7B-Instruct-v0.1': [47.81, 42.57],
    'Llama-2-70b': [35.17],
    'meditron-70b': [30.58],
    'ClinicalCamel-70B': [35.71],
    'Llama-2-70b-chat': [42.9],
    'Meta-Llama-3-70B-Instruct': [52.36, 56.0],
    'Llama3-OpenBioLLM-70B': [47.57, 45.56],
    'Mixtral-8x22B-Instruct-v0.1': [51.88, 51.23]
};

const modelMetrics = {
    MeDiSumCode: {
        'Mistral-7B-v0.1': [0.77, 5.32, 33.21],
        'internistai/base-7b-v0.2': [1.87, 10.25, 53.57],
        'BioMistral-7B': [1.67, 9.92, 54.51],
        'BioMistral-7B-DARE': [1.2, 6.66, 56.04],
        'Mistral-7B-Instruct-v0.1': [0.57, 3.78, 37.25],
        'zephyr-7b-beta': [2.31, 12.0, 71.27],
        'Mistral-7B-Instruct-v0.2': [3.08, 18.23, 68.76],
        'Meta-Llama-3-8B-Instruct': [3.95, 17.55, 61.93],
        'Llama3-OpenBioLLM-8B': [0.84, 4.84, 51.16],
        'JSL-Med-Sft-Llama-3-8B': [3.09, 14.03, 68.37],
        'JSL-MedLlama-3-8B-v1.0': [1.87, 12.8, 67.3],
        'JSL-MedLlama-3-8B-v2.0': [1.41, 11.13, 64.31],
        'Llama3-Aloe-8B-Alpha': [1.77, 12.78, 44.84],
        'Phi-3-mini-128k-instruct': [0.43, 3.63, 86.24],
        'Mixtral-8x7B-Instruct-v0.1': [10.49, 28.99, 82.87],
        'Llama3-OpenBioLLM-70B': [7.37, 20.24, 73.65],
        'Meta-Llama-3-70B-Instruct': [19.65, 39.2, 93.94],
        'Mixtral-8x22B-Instruct-v0.1': [15.95, 35.96, 79.84]
    },
    MeDiSumQA: {
        'Mistral-7B-v0.1': [5.93, 7.16, 1.59, 53.47, 7.47],
        'internistai/base-7b-v0.2': [9.24, 11.94, 3.23, 61.82, 13.0],
        'BioMistral-7B': [14.65, 17.81, 5.46, 59.01, 16.88],
        'BioMistral-7B-DARE': [17.01, 20.87, 6.92, 65.17, 18.45],
        'Mistral-7B-Instruct-v0.1': [16.62, 21.34, 6.95, 65.68, 16.77],
        'zephyr-7b-beta': [13.02, 17.68, 4.98, 64.08, 13.92],
        'Mistral-7B-Instruct-v0.2': [21.8, 27.47, 9.19, 68.43, 20.3],
        'Meta-Llama-3-8B-Instruct': [22.44, 28.15, 9.63, 68.62, 22.74],
        'Llama3-OpenBioLLM-8B': [22.89, 27.95, 10.45, 68.7, 22.15],
        'JSL-Med-Sft-Llama-3-8B': [8.85, 11.74, 3.21, 58.38, 10.54],
        'JSL-MedLlama-3-8B-v1.0': [19.06, 22.7, 7.63, 67.29, 20.83],
        'JSL-MedLlama-3-8B-v2.0': [19.13, 24.15, 8.56, 67.41, 20.49],
        'Llama3-Aloe-8B-Alpha': [11.03, 14.97, 4.57, 63.87, 12.68],
        'Phi-3-mini-128k-instruct': [18.47, 22.45, 6.94, 65.72, 16.52],
        'Mixtral-8x7B-Instruct-v0.1': [20.72, 26.4, 8.96, 67.74, 20.25],
        'Llama3-OpenBioLLM-70B': [21.85, 27.8, 9.54, 68.43, 22.47],
        'Meta-Llama-3-70B-Instruct': [26.2, 32.5, 11.93, 70.24, 25.78],
        'Mixtral-8x22B-Instruct-v0.1': [21.81, 27.75, 9.42, 68.51, 22.7]
    },
    MeQSum: {
        'llama2-7b': [7.16, 8.58, 2.89, 37.5],
        'meditron-7b': [6.17, 7.47, 2.53, 40.53],
        'llama2-7b-hf': [36.45, 39.99, 18.13, 75.98],
        'Mistral-7B-v0.1': [6.28, 7.86, 2.49, 44.6],
        'internistai/base-7b-v0.2': [7.65, 9.54, 3.44, 40.34],
        'BioMistral-7B': [25.89, 28.46, 13.31, 67.93],
        'BioMistral-7B-DARE': [26.16, 29.69, 13.49, 68.68],
        'Mistral-7B-Instruct-v0.1': [21.85, 25.26, 11.17, 66.15],
        'zephyr-7b-beta': [25.66, 29.81, 12.33, 68.85],
        'Mistral-7B-Instruct-v0.2': [33.54, 37.47, 16.61, 73.47],
        'Meta-Llama-3-8B-Instruct': [32.2, 36.49, 16.37, 72.74],
        'Llama3-OpenBioLLM-8B': [26.21, 29.41, 14.03, 62.39],
        'JSL-Med-Sft-Llama-3-8B': [6.57, 7.73, 2.99, 31.7],
        'JSL-MedLlama-3-8B-v1.0': [6.02, 7.19, 2.43, 41.78],
        'JSL-MedLlama-3-8B-v2.0': [30.93, 34.54, 16.31, 71.03],
        'Llama3-Aloe-8B-Alpha': [23.78, 27.02, 12.31, 66.01],
        'Phi-3-mini-128k-instruct': [32.05, 35.85, 15.81, 73.01],
        'Mixtral-8x7B-v0.1': [9.36, 10.35, 4.3, 31.04],
        'Mixtral-8x7B-Instruct-v0.1': [32.47, 36.38, 16.86, 72.8],
        'Llama-2-70b': [3.75, 4.15, 1.05, 33.59],
        'meditron-70b': [2.95, 3.24, 0.76, 31.27],
        'ClinicalCamel-70B': [16.96, 18.8, 8.49, 49.3],
        'Llama-2-70b-chat': [34.91, 38.81, 18.48, 74.37],
        'Meta-Llama-3-70B-Instruct': [36.57, 40.2, 19.3, 75.74],
        'Llama3-OpenBioLLM-70B': [30.72, 34.31, 15.55, 71.99],
        'Mixtral-8x22B-Instruct-v0.1': [36.15, 39.94, 19.45, 75.02]
    },
    'Problem Summary': {
        'llama2-7b': [5.97, 7.35, 2.11, 59.45, 9.06],
        'meditron-7b': [6.49, 7.87, 2.59, 59.57, 12.52],
        'llama2-7b-hf': [17.43, 22.25, 6.93, 66.33, 21.62],
        'Mistral-7B-v0.1': [7.07, 8.99, 3.17, 60.74, 15.67],
        'internistai/base-7b-v0.2': [13.11, 16.79, 5.63, 62.94, 17.22],
        'BioMistral-7B': [16.9, 20.89, 8.4, 59.03, 20.12],
        'BioMistral-7B-DARE': [18.81, 22.92, 8.93, 68.93, 22.65],
        'Mistral-7B-Instruct-v0.1': [14.46, 18.6, 6.26, 65.79, 20.08],
        'zephyr-7b-beta': [14.81, 19.91, 5.98, 67.39, 19.2],
        'Mistral-7B-Instruct-v0.2': [19.57, 25.59, 8.92, 69.64, 22.07],
        'Meta-Llama-3-8B-Instruct': [22.7, 28.52, 9.87, 71.45, 25.32],
        'Llama3-OpenBioLLM-8B': [10.82, 13.62, 4.03, 64.14, 15.67],
        'JSL-Med-Sft-Llama-3-8B': [4.59, 5.75, 1.61, 57.69, 8.75],
        'JSL-MedLlama-3-8B-v1.0': [12.87, 15.4, 4.73, 65.18, 18.42],
        'JSL-MedLlama-3-8B-v2.0': [8.16, 10.61, 2.94, 64.48, 13.17],
        'Llama3-Aloe-8B-Alpha': [9.47, 12.3, 4.06, 65.56, 15],
        'Phi-3-mini-128k-instruct': [19.8, 23.72, 8.47, 70.27, 21.06],
        'Mixtral-8x7B-v0.1': [7.26, 9.37, 3.14, 60.54, 11.94],
        'Mixtral-8x7B-Instruct-v0.1': [17.44, 23.39, 7.7, 68.56, 19.51],
        'Llama-2-70b': [7.13, 8.77, 3.15, 59.61, 14.34],
        'meditron-70b': [7.22, 8.91, 3.21, 60.08, 13.91],
        'ClinicalCamel-70B': [8.62, 10.83, 3.71, 59.94, 12.34],
        'Llama-2-70b-chat': [14.43, 19.81, 6.14, 65.07, 21.37],
        'Meta-Llama-3-70B-Instruct': [25.43, 33.16, 13.01, 73, 29.12],
        'Llama3-OpenBioLLM-70B': [12.1, 16.67, 5.58, 66.51, 17.74],
        'Mixtral-8x22B-Instruct-v0.1': [17.97, 22.6, 8.17, 69.29, 22.31]
    },
    MedNLI: {
        'llama2-7b': [29.51],
        'meditron-7b': [2.39],
        'llama2-7b-hf': [41.27],
        'Mistral-7B-v0.1': [67.54],
        'internistai/base-7b-v0.2': [76.34],
        'BioMistral-7B': [62.75],
        'BioMistral-7B-DARE': [66.76],
        'Mistral-7B-Instruct-v0.1': [64.79],
        'zephyr-7b-beta': [68.45],
        'Mistral-7B-Instruct-v0.2': [69.93],
        'Meta-Llama-3-8B-Instruct': [74.08],
        'Llama3-OpenBioLLM-8B': [44.93],
        'JSL-Med-Sft-Llama-3-8B': [29.08],
        'JSL-MedLlama-3-8B-v1.0': [13.31],
        'JSL-MedLlama-3-8B-v2.0': [35.7],
        'Llama3-Aloe-8B-Alpha': [73.94],
        'Phi-3-mini-128k-instruct': [57.25],
        'Mixtral-8x7B-v0.1': [80.14],
        'Mixtral-8x7B-Instruct-v0.1': [76.48],
        'Llama-2-70b': [76.27],
        'meditron-70b': [63.52],
        'ClinicalCamel-70B': [64.65],
        'Llama-2-70b-chat': [61.69],
        'Meta-Llama-3-70B-Instruct': [79.37],
        'Llama3-OpenBioLLM-70B': [80.85],
        'Mixtral-8x22B-Instruct-v0.1': [84.93]
    },
    LongHealth: {
        'Mistral-7B-v0.1': [9.55, 0.6, 0.55, 0.15],
        'internistai/base-7b-v0.2': [28.64, 52.75, 30.6, 49.2],
        'BioMistral-7B': [23.83, 38.05, 34.25, 7.8],
        'BioMistral-7B-DARE': [25.82, 46.0, 40.85, 4.6],
        'Mistral-7B-Instruct-v0.1': [23.12, 45.75, 40.65, 3.65],
        'zephyr-7b-beta': [28.17, 42.9, 30.5, 26.35],
        'Mistral-7B-Instruct-v0.2': [38.94, 67.2, 62.4, 42.45],
        'Meta-Llama-3-8B-Instruct': [40.61, 68.3, 66.55, 56.25],
        'Llama3-OpenBioLLM-8B': [25.44, 37.55, 41.75, 1.55],
        'JSL-Med-Sft-Llama-3-8B': [15.68, 0.0, 0.0, 0.0],
        'JSL-MedLlama-3-8B-v1.0': [29.94, 52.85, 52.05, 0.05],
        'JSL-MedLlama-3-8B-v2.0': [18.02, 1.1, 0.3, 0.05],
        'Llama3-Aloe-8B-Alpha': [30.31, 66.75, 63.3, 19.05],
        'Phi-3-mini-128k-instruct': [24.39, 27.25, 23.9, 0.0],
        'Mixtral-8x7B-Instruct-v0.1': [42.57, 76.5, 73.65, 24.2],
        'Llama3-OpenBioLLM-70B': [45.56, 80.2, 75.6, 62.9],
        'Meta-Llama-3-70B-Instruct': [56.0, 81.65, 77.9, 91.7],
        'Mixtral-8x22B-Instruct-v0.1': [51.23, 79.0, 73.9, 86.3]
    }
};

const tasks = ref([
    {
        name: 'MeDiSumCode',
        metrics: ['EM F1', 'AP F1', 'Valid Codes'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['MeDiSumCode'][model] || [] // Default to empty if no data available
        }))
    },
    {
        name: 'MeDiSumQA',
        metrics: ['R-L', 'R-1', 'R-2', 'BERT F1', 'UMLS F1'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['MeDiSumQA'][model] || [] // Default to empty if no data available
        }))
    },
    {
        name: 'MeQSum',
        metrics: ['R-L', 'R-1', 'R-2', 'BERT F1'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['MeQSum'][model] || [] // Default to empty if no data available
        }))
    },
    {
        name: 'Problem Summary',
        metrics: ['R-L', 'R-1', 'R-2', 'BERT F1', 'UMLS F1'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['Problem Summary'][model] || [] // Default to empty if no data available
        }))
    },
    {
        name: 'MedNLI',
        metrics: ['Accuracy'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['MedNLI'][model] || [] // Default to empty if no data available
        }))
    },
    {
        name: 'LongHealth',
        metrics: ['Task 1 Accuracy', 'Task 2 Accuracy', 'Task 3 Accuracy'],
        results: models.value.map((model) => ({
            label: model,
            color: colorMap[model],
            value: modelMetrics['LongHealth'][model] || [] // Default to empty if no data available
        }))
    }
]);

const selectedTaskIndex = ref(0); // Tracks the selected task index
const selectedTask = computed({
    get: () => tasks.value[selectedTaskIndex.value],
    set: (newTask) => {
        const taskIndex = tasks.value.indexOf(newTask);
        selectedTaskIndex.value = taskIndex >= 0 ? taskIndex : 0;
    }
});

const selectedMetric = ref(tasks.value[selectedTaskIndex.value].metrics[0]);

watch(selectedTask, (newTask) => {
    // Reset the selected metric when the task changes
    selectedMetric.value = newTask.metrics[0];
});

// Watchers to sync changes for dropdowns - might not be necessary if using v-model directly
watch(selectedTaskIndex, () => {
    selectedMetric.value = tasks.value[selectedTaskIndex.value].metrics[0];
});

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

    let modelResults = [];
    let modelColors = [];
    for (var result of task.results) {
        if (selectedModels.value.includes(result.label) && result.value.length > 0) {
            labels.push(result.label);

            let metricIndex = task.metrics.findIndex((elem) => elem == metric);
            modelResults.push(result.value[metricIndex]);
            modelColors.push(result.color);
        }
    }
    datasets.push({
        label: metric,
        data: modelResults,
        backgroundColor: modelColors
    });
    return {
        labels: labels,
        datasets: datasets
    };
};

const scatterData = () => {
    let labels = [];
    let datasets = [];
    if (selectedModels.value == null) {
        return {
            labels: labels,
            datasets: datasets
        };
    }

    // for (let [model, scores] of Object.entries(modelScores)) {
    //     if (selectedModels.value.includes(model) && scores.length == 2) {
    //         datasets.push({
    //             label: model,
    //             data: [{ x: scores[0], y: scores[1] }],
    //             backgroundColor: colorMap[model]
    //         });
    //     }
    // }

    for (let baseModel in modelFamilies) {
        for (let model of modelFamilies[baseModel]) {
            let dataset = {
                label: model,
                data: [],
                backgroundColor: colorMap[model],
                showLine: false,
                pointRadius: 5
            };
            let scores = modelScores[model];
            if (scores.length == 2 && selectedModels.value.includes(model)) {
                dataset.data.push(scores);
            }
            if (dataset.data.length > 0) {
                datasets.push(dataset);
            }
        }
    }
    for (let baseModel in modelFamilies) {
        for (let model of modelFamilies[baseModel]) {
            if (selectedModels.value.includes(baseModel) && selectedModels.value.includes(model) && baseModel != model) {
                datasets.push({
                    type: 'line',
                    data: [modelScores[baseModel], modelScores[model]],
                    label: 'ignore'
                });
            }
        }
    }
    return {
        datasets: datasets
    };
};

const bar2Data = () => {
    let labels = [];
    let datasets = [];
    if (selectedModels.value == null) {
        return {
            labels: labels,
            datasets: datasets
        };
    }

    let score1_results = [];
    let score2_results = [];
    let modelColors = [];
    for (let [model, scores] of Object.entries(modelScores)) {
        if (selectedModels.value.includes(model) && scores.length > 0) {
            labels.push(model);
            modelColors.push(colorMap[model]);
            score1_results.push(scores[0]);
            if (scores.length > 1) {
                score2_results.push(scores[1]);
            } else {
                score2_results.push(0.0);
            }
        }
    }

    return {
        labels: labels,
        datasets: [
            {
                label: 'Level 1 Score',
                data: score1_results,
                backgroundColor: modelColors
            },
            {
                label: 'Level 2 Score',
                data: score2_results,
                backgroundColor: modelColors
            }
        ]
    };
};

const barOptions = ref({
    plugins: {
        legend: {
            display: false
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

const bar2Option = ref({
    plugins: {
        legend: {
            display: false
        }
    },
    indexAxis: is_small_device.value ? 'x' : 'y'
});

const scatterOption = ref({
    plugins: {
        tooltip: {
            callbacks: {
                label: function (ctx) {
                    return ctx.dataset.label;
                }
            },
            filter: function (tooltipItem, data) {
                console.log(tooltipItem);
                return data == 0 && tooltipItem.dataset.label != 'ignore';
            }
        },
        legend: {
            display: true,
            labels: {
                filter: function (legendItem, data) {
                    return legendItem.text != 'ignore';
                }
            },
            onClick: () => {
                return;
            }
        },
        annotation: {
            annotations: {
                line: {
                    arrowHeads: {
                        end: {
                            display: true
                        }
                    }
                }
            }
        }
    },
    scales: {
        x: {
            title: {
                text: 'Average Level 1 Score',
                display: true
            }
        },
        y: {
            title: {
                text: 'Average Level 2 Score',
                display: true
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

const scrollToAnchor = (selector) => {
    const offset = 70; // Height of the topbar in pixels
    const element = document.querySelector(selector);
    if (element) {
        const elementPosition = element.getBoundingClientRect().top + window.pageYOffset;
        const offsetPosition = elementPosition - offset;

        window.scrollTo({
            top: offsetPosition,
            behavior: 'smooth'
        });
    }
};

const bibtex = ref(null);

const copyBibTex = (event) => {
    const preElement = event.target.nextElementSibling;
    if (preElement) {
        const text = preElement.innerText;
        navigator.clipboard.writeText(text);
    }
};
</script>

<template>
    <div class="w-full h-full flex flex-column align-items-center">
        <div class="w-full lg:w-7">
            <div class="card flex flex-wrap gap-3 justify-content-center header">
                <h1 style="font-family: 'Google Sans', sans-serif"><span style="font-family: 'EB Garamond'">CLUE</span>: A Clinical Language Understanding Evaluation for LLMs</h1>
                <img src="/layout/images/clue-logo.png" alt="Image Description 1" class="clue-logo" />

                <h4>
                    <span class="white-space-nowrap">Amin Dada</span>, <span class="white-space-nowrap">Marie Bauer</span>, <span class="white-space-nowrap">Amanda Butler Contreras</span>, <span class="white-space-nowrap">Osman Alperen Koraş</span>,
                    <span class="white-space-nowrap">Constantin Marc Seibold</span>, <span class="white-space-nowrap">Kaleb E Smith</span>, <span class="white-space-nowrap">Jens Kleesiek</span>
                </h4>

                <a href="https://github.com/TIO-IKIM/CLUE" target="_blank" rel="noopener noreferrer">
                    <Button label="Code" icon="pi pi-github" severity="contrast" />
                </a>
                <a href="https://arxiv.org/abs/2404.04067" target="_blank" rel="noopener noreferrer">
                    <Button label="Paper" icon="pi pi-book" severity="contrast" />
                </a>
                <Button label="Results" icon="pi pi-chart-bar" @click="scrollToAnchor('#model-select')" severity="contrast" />
            </div>
            <Panel class="w-full my-3 p-3" v-bind="{ ...(isMobile ? { collapsed: true } : {}), toggleable: true }">
                <template #header><h3 class="m-0">Motivation</h3></template>

                <p>
                    <Divider />
                    Biomedical LLMs promise significant advancements in patient care, yet their real-world evaluation remains lacking. Current assessments focus primarily on medical knowledge via constructed questions, which do not fully capture the
                    complexity and diversity of clinical tasks. Furthermore, the rapid evolution of LLMs makes it challenging to choose the most suitable models for healthcare applications. To address these issues,
                    <span style="font-family: 'EB Garamond'">CLUE</span> is a comprehensive and standardized framework to assess the performance of specialized biomedical and advanced general-domain LLMs in practical healthcare tasks.
                </p>
                <p>
                    Clinical texts are known for their irregular structure, abundant jargon, and region-specific abbreviations, making them substantially different from the concise quiz questions used in standard datasets. Recent studies have also
                    identified significant data contamination in LLM evaluations, raising concerns about the reliability of previous results. In response, <span style="font-family: 'EB Garamond'">CLUE</span> is focusing on creating more robust and
                    realistic medical evaluation methods for LLMs. This initiative aims to improve their integrity and real-world applicability, proposing new strategies for more effective testing.
                </p>
            </Panel>
            <Panel class="w-full my-3 p-3" v-bind="{ ...(isMobile ? { collapsed: true } : {}), toggleable: true }">
                <template #header>
                    <h3 class="m-0">Key Takeaways</h3>
                </template>
                <divider></divider>
                <ul class="custom-style mb-0">
                    <li>Specialized biomedical LLMs do not show performance advantages over general purpose LLMs.</li>
                    <li>Conversely, certain models adapted for the medical domain show inferior performance compared to their non-specialized counterparts.</li>
                    <li>Context length is an important factor, as clinical documents quickly exceed a length of 4k tokens. When few-shot learning is applied, context lengths of 8k tokens are quickly no longer sufficient.</li>
                    <li>
                        It is possible to generate new data sets for evaluation on the basis of clinical documents that contain a summary, such as discharge summaries. We think that this represents an opportunity to counteract benchmark contamination
                        by applying this approach to private clinical data.
                    </li>
                </ul>
            </Panel>

            <Panel class="w-full my-3 p-3" v-bind="{ ...(isMobile ? { collapsed: true } : {}), toggleable: true }">
                <template #header><h3 class="m-0">Task Descriptions</h3></template>
                <TabView class="mt-3">
                    <TabPanel header="MeDiSumCode">
                        <p class="m-0">
                            MeDiSumCode requires coding discharge summaries by assigning International Classification of Diseases (ICD-10) codes to diagnoses and procedures. The task challenges language models to extract diagnoses from complex
                            clinical texts and match them with the correct ICD-10 codes, which include over 70,000 options and necessitate a thorough knowledge of the coding system. Additionally, it tests the models' ability to combine diagnosis
                            identification with an understanding of the ICD-10 structure to predict codes effectively.
                        </p>
                    </TabPanel>
                    <TabPanel header="MeDiSumQA">
                        <p class="m-0">
                            MeDiSumQA is a medical QA dataset derived from discharge summaries in the MIMIC IV database, designed to generate question-answer pairs about patient hospital stays using a structured Questions Under Discussion framework.
                            The process includes parsing discharge letters, generating questions with minimal answer leakage through similarity metrics, and emphasizing clinical reasoning. You can find a more detailed description in our
                            <a href="https://arxiv.org/abs/2404.04067" target="_blank" rel="noopener noreferrer">paper</a>.
                        </p>
                    </TabPanel>
                    <TabPanel header="MeQSum">
                        <p class="m-0">
                            MeQSum comprises 1,000 consumer health inquiries from the U.S. National Library of Medicine that have been manually summarized by medical experts. This task challenges models to condense these often verbose and vague
                            inquiries into concise, medically accurate questions. The ability to effectively interpret and summarize patient inquiries, despite not involving clinical documents, is highly valuable for applications in healthcare
                            settings.
                        </p>
                        <pre style="background-color: #f9fafb; overflow-x: auto; position: relative">
<button @click="copyBibTex($event)" class="copy-btn pi pi-copy" style="position: absolute; top: 5px; right: 5px;"></button>
<code ref="bibtex">@inproceedings{abacha2019summarization,
  title={On the summarization of consumer health questions},
  author={Abacha, Asma Ben and Demner-Fushman, Dina},
  booktitle={Proceedings of the 57th Annual Meeting of the Association for Computational Linguistics},
  pages={2228--2234},
  year={2019}
}</code></pre>
                    </TabPanel>
                    <TabPanel header="Problem Summary">
                        <p class="m-0">
                            This downstream task, involves using the Subjective and Assessment sections of clinical notes, structured by the SOAP principle, to predict a patient's current health problems. The Objective section is excluded from
                            inputs, while the Plan section provides the ground truth for the models' predictions. This task is a form of information extraction that challenges models to identify specific health issues from condensed, preprocessed
                            clinical text.
                        </p>
                        <pre style="background-color: #f9fafb; overflow-x: auto; position: relative">
<button @click="copyBibTex($event)" class="copy-btn pi pi-copy" style="position: absolute; top: 5px; right: 5px;"></button>
<code ref="bibtex">@inproceedings{gao2022summarizing,
  title={Summarizing patients’ problems from hospital progress notes using pre-trained sequence-to-sequence models},
  author={Gao, Yanjun and Miller, Timothy and Xu, Dongfang and Dligach, Dmitriy and Churpek, Matthew M and Afshar, Majid},
  booktitle={Proceedings of COLING. International Conference on Computational Linguistics},
  volume={2022},
  pages={2979},
  year={2022},
  organization={NIH Public Access}
}</code></pre>
                    </TabPanel>
                    <TabPanel header="MedNLI">
                        <p class="m-0">
                            MedNLI, derived from the MIMIC III dataset, features clinical notes from which sentences are extracted and used as premises for clinicians to generate three corresponding hypotheses: contradictory, neutral, and entailed.
                            The core task involves predicting the logical relationship—whether it is contradiction, neutrality, or entailment—between each premise and its hypotheses. With its focus on short input lengths, MedNLI effectively measures
                            a model's clinical reasoning capabilities without the complexity of processing long text inputs.
                        </p>
                        <pre style="background-color: #f9fafb; overflow-x: auto; position: relative">
<button @click="copyBibTex($event)" class="copy-btn pi pi-copy" style="position: absolute; top: 5px; right: 5px;"></button>
<code ref="bibtex">@article{romanov2018lessons,
	title = {Lessons from Natural Language Inference in the Clinical Domain},
	url = {http://arxiv.org/abs/1808.06752},
	abstract = {State of the art models using deep neural networks have become very good in learning an accurate mapping from inputs to outputs. However, they still lack generalization capabilities in conditions that differ from the ones encountered during training. This is even more challenging in specialized, and knowledge intensive domains, where training data is limited. To address this gap, we introduce {MedNLI} - a dataset annotated by doctors, performing a natural language inference task ({NLI}), grounded in the medical history of patients. We present strategies to: 1) leverage transfer learning using datasets from the open domain, (e.g. {SNLI}) and 2) incorporate domain knowledge from external data and lexical sources (e.g. medical terminologies). Our results demonstrate performance gains using both strategies.},
	journaltitle = {arXiv:1808.06752 [cs]},
	author = {Romanov, Alexey and Shivade, Chaitanya},
	urldate = {2018-08-27},
	date = {2018-08-21},
	eprinttype = {arxiv},
	eprint = {1808.06752},
}</code></pre>
                    </TabPanel>
                    <TabPanel header="LongHealth">
                        <p class="m-0">
                            The LongHealth dataset features 20 fictional patient records to test language models on their ability to handle extended texts, as these models typically underperform with longer inputs. It tasks models with answering
                            multiple-choice questions, structured into three distinct evaluation sub-tasks. These tasks progressively increase in complexity by first focusing on relevant documents, then introducing extraneous information to filter
                            out, and finally testing the model's ability to identify unprovided information. In contrast to the original paper, we have set the maximum context length to 8k tokens.
                        </p>
                        <pre style="background-color: #f9fafb; overflow-x: auto; position: relative">
<button @click="copyBibTex($event)" class="copy-btn pi pi-copy" style="position: absolute; top: 5px; right: 5px;"></button>
<code ref="bibtex">@article{adams2024longhealth,
  title={LongHealth: A Question Answering Benchmark with Long Clinical Documents},
  author={Adams, Lisa and Busch, Felix and Han, Tianyu and Excoffier, Jean-Baptiste and Ortala, Matthieu and L{\"o}ser, Alexander and Aerts, Hugo JWL and Kather, Jakob Nikolas and Truhn, Daniel and Bressem, Keno},
  journal={arXiv preprint arXiv:2401.14490},
  year={2024}
}</code></pre>
                    </TabPanel>
                </TabView>
            </Panel>
            <div id="model-select" class="card w-full my-3">
                <h3>Model Selection</h3>
                <p>Select one or more models from the list below to view their benchmark results.</p>

                <Divider />
                <MultiSelect class="w-full" v-model="selectedModels" display="chip" :options="models" placeholder="Select Models" filter> </MultiSelect>
            </div>

            <div class="card w-full my-3">
                <h3>Average Scores</h3>

                <Divider />
                <p>
                    We provide two averaged scores: Level 1 and Level 2. Level 1 includes the tasks MedNLI, MeQSum and Problem Summary, which feature shorter examples. Level 2 includes the remaining tasks: MeDiSumCode, MeDiSumQA and LongHealth.
                    Further details can be found in our <a href="https://arxiv.org/abs/2404.04067" target="_blank" rel="noopener noreferrer">paper</a>.
                </p>
                <Chart type="scatter" :data="scatterData(selectedTask, selectedMetric)" :options="scatterOption"></Chart>
                <Divider />

                <Chart type="bar" :data="bar2Data(selectedTask, selectedMetric)" :options="bar2Option" :plugins="barPlugins"></Chart>
            </div>

            <div class="card w-full">
                <h3>Individual Task Scores</h3>
                <p>Explore detailed performance results for each model by selecting a specific task and metric.</p>
                <Divider />
                <div class="w-full mb-3">
                    <h5 class="mb-3">{{ selectedTask.name }} ({{ selectedMetric }})</h5>
                    <div class="flex-grow-1"></div>
                    <Dropdown v-model="selectedTask" :options="tasks" optionLabel="name" placeholder="Select Task" />
                    <Dropdown v-model="selectedMetric" :options="selectedTask.metrics" placeholder="Select Metric" />
                </div>
                <Chart type="bar" :data="barData(selectedTask, selectedMetric)" :options="barOptions" :plugins="barPlugins"></Chart>
            </div>
            <div class="card w-full my-3">
                <h3>BibTeX</h3>
                <Divider />
                <section class="section" id="BibTeX">
                    <div class="container is-max-desktop content" style="position: relative">
                        <p>If you find our work useful, please cite our paper:</p>
                        <pre style="background-color: #f9fafb; overflow-x: auto; position: relative">
                            <button @click="copyBibTex($event)" class="copy-btn pi pi-copy" style="position: absolute; top: 5px; right: 5px;"></button>
<code ref="bibtex">@article{dada2024clue,
  title={CLUE: A Clinical Language Understanding Evaluation for LLMs},
  author={Dada, Amin and Bauer, Marie and Contreras, Amanda Butler and Kora{\c{s}}, Osman Alperen and Seibold, Constantin Marc and Smith, Kaleb E and Kleesiek, Jens},
  journal={arXiv preprint arXiv:2404.04067},
  year={2024}
}</code></pre>
                    </div>
                </section>
            </div>
            <div class="card w-full my-3">
                <h3>Contact</h3>
                <Divider />
                <p>Please reach out to <a class="link blue" href="mailto:amin.dada@uk-essen.de">Amin Dada</a> if you have any comments, questions, or suggestions.</p>
            </div>
            <div class="flex flex-column md:flex-row align-items-center justify-content-center">
                <img src="/layout/images/ume.png" class="ume-logo" />
                <img src="/layout/images/NVIDIA_logo.svg" class="nvidia-logo" />
            </div>
        </div>
    </div>
</template>
