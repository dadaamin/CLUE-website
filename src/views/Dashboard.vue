<script setup>
import { useLayout } from '@/layout/composables/layout';
import ScrollPanel from 'primevue/scrollpanel';
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
    'Llama3-OpenBioLLM-8B',
    'Meta-Llama-3-8B-Instruct',
    'Llama3-OpenBioLLM-70B',
    'Meta-Llama-3-70B-Instruct',
    'Mixtral-8x7B-Instruct-v0.1',
    'Mixtral-8x22B-Instruct-v0.1',
    'zephyr-7b-beta',
    'Mistral-7B-Instruct-v0.1',
    'Mistral-7B-Instruct-v0.2',
    'BioMistral-7B-DARE',
    'llama2-7b',
    'llama2-70b',
    'meditron-7b',
    'meditron-70b',
    'BioMistral-7B'
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

const modelScores = {
    'llama2-7b': [33.76],
    'llama2-70b': [48.95],
    'meditron-7b': [26.32],
    'meditron-70b': [43.93],
    'Llama3-OpenBioLLM-8B': [54.3, 30.48],
    'Meta-Llama-3-8B-Instruct': [65.07, 45.96],
    'Llama3-OpenBioLLM-70B': [64.99, 51.13],
    'Meta-Llama-3-70B-Instruct': [68.72, 60.99],
    'zephyr-7b-beta': [60.2, 34.81],
    'Mistral-7B-Instruct-v0.1': [57.96, 28.85],
    'Mistral-7B-Instruct-v0.2': [63.09, 44.72],
    'BioMistral-7B': [57.77],
    'BioMistral-7B-DARE': [60.41, 31.14],
    'Mixtral-8x7B-Instruct-v0.1': [64.44, 48.64],
    'Mixtral-8x22B-Instruct-v0.1': [68.58, 56.68]
};

const modelMetrics = {
    MeDiSumCode: {
        'zephyr-7b-beta': [2.31, 12.0, 71.27],
        'Mistral-7B-Instruct-v0.1': [0.57, 3.78, 37.25],
        'Mistral-7B-Instruct-v0.2': [3.08, 18.23, 68.76],
        'BioMistral-7B-DARE': [1.2, 6.66, 56.04],
        'Mixtral-8x7B-Instruct-v0.1': [10.49, 28.99, 82.87],
        'Llama3-OpenBioLLM-8B': [0.84, 0.48, 51.16],
        'Meta-Llama-3-8B-Instruct': [3.95, 17.55, 61.93],
        'Llama3-OpenBioLLM-70B': [7.37, 20.24, 73.65],
        'Meta-Llama-3-70B-Instruct': [19.65, 39.2, 93.94],
        'Mixtral-8x22B-Instruct-v0.1': [15.95, 35.96, 79.84]
    },
    MeDiSumQA: {
        'zephyr-7b-beta': [18.2, 21.3, 8.91, 66.27, 19.06],
        'Mistral-7B-Instruct-v0.1': [18.78, 21.5, 10.05, 66.71, 18.63],
        'Mistral-7B-Instruct-v0.2': [24.39, 28.0, 13.2, 69.53, 24.07],
        'BioMistral-7B-DARE': [17.54, 19.9, 9.34, 64.6, 18.65],
        'Mixtral-8x7B-Instruct-v0.1': [25.54, 29.26, 13.89, 69.57, 24.47],
        'Llama3-OpenBioLLM-8B': [25.1, 27.84, 13.12, 66.3, 24.79],
        'Meta-Llama-3-8B-Instruct': [24.49, 27.95, 12.75, 69.38, 23.37],
        'Llama3-OpenBioLLM-70B': [24.43, 27.73, 13.29, 68.98, 24.5],
        'Meta-Llama-3-70B-Instruct': [27.24, 30.95, 14.24, 70.71, 25.84],
        'Mixtral-8x22B-Instruct-v0.1': [23.8, 27.62, 12.69, 69.23, 23.55]
    },
    MeQSum: {
        'llama2-7b': [7.16, 8.58, 2.89, 37.5],
        'llama2-70b': [3.75, 4.15, 1.05, 33.59],
        'meditron-7b': [6.17, 7.47, 2.53, 40.53],
        'meditron-70b': [2.95, 3.24, 0.76, 31.27],
        'zephyr-7b-beta': [7.16, 8.58, 2.89, 37.5],
        'Mistral-7B-Instruct-v0.1': [21.85, 25.26, 11.17, 66.15],
        'Mistral-7B-Instruct-v0.2': [33.54, 37.47, 16.61, 73.47],
        'BioMistral-7B': [24.58, 27.38, 12.1, 67.05],
        'BioMistral-7B-DARE': [26.16, 29.69, 13.49, 68.68],
        'Mixtral-8x7B-Instruct-v0.1': [32.47, 36.38, 16.86, 72.8],
        'Llama3-OpenBioLLM-8B': [26.21, 29.41, 14.03, 62.39],
        'Meta-Llama-3-8B-Instruct': [32.2, 36.49, 16.37, 72.74],
        'Llama3-OpenBioLLM-70B': [30.72, 34.31, 15.55, 71.99],
        'Meta-Llama-3-70B-Instruct': [36.57, 40.2, 19.3, 75.74],
        'Mixtral-8x22B-Instruct-v0.1': [36.15, 39.94, 19.45, 75.02]
    },
    'Problem Summary': {
        'llama2-7b': [5.97, 7.35, 2.11, 59.45, 9.06],
        'llama2-70b': [7.13, 8.77, 3.15, 59.61, 14.34],
        'meditron-7b': [6.49, 7.87, 2.59, 59.57, 12.52],
        'meditron-70b': [7.22, 8.91, 3.21, 60.08, 13.91],
        'zephyr-7b-beta': [5.97, 7.35, 2.11, 59.45, 9.06],
        'Mistral-7B-Instruct-v0.1': [14.46, 18.6, 6.26, 65.79, 20.08],
        'Mistral-7B-Instruct-v0.2': [19.57, 25.59, 8.92, 69.64, 22.07],
        'BioMistral-7B': [18.0, 21.85, 8.63, 66.96, 20.92],
        'BioMistral-7B-DARE': [18.81, 22.92, 8.93, 68.93, 22.65],
        'Mixtral-8x7B-Instruct-v0.1': [17.44, 23.39, 7.7, 68.56, 19.51],
        'Llama3-OpenBioLLM-8B': [10.82, 13.62, 4.03, 64.14, 15.67],
        'Meta-Llama-3-8B-Instruct': [32.2, 36.49, 16.37, 72.74],
        'Llama3-OpenBioLLM-70B': [22.7, 28.52, 9.87, 71.45, 25.32],
        'Meta-Llama-3-70B-Instruct': [25.43, 33.16, 13.01, 73.0, 29.12],
        'Mixtral-8x22B-Instruct-v0.1': [17.97, 22.6, 8.17, 69.29, 22.31]
    },
    MedNLI: {
        'llama2-7b': [29.51],
        'llama2-70b': [76.27],
        'meditron-7b': [2.39],
        'meditron-70b': [63.52],
        'zephyr-7b-beta': [68.45],
        'Mistral-7B-Instruct-v0.1': [64.79],
        'Mistral-7B-Instruct-v0.2': [69.93],
        'BioMistral-7B': [62.32],
        'BioMistral-7B-DARE': [66.76],
        'Mixtral-8x7B-Instruct-v0.1': [76.48],
        'Llama3-OpenBioLLM-8B': [44.93],
        'Meta-Llama-3-8B-Instruct': [74.08],
        'Llama3-OpenBioLLM-70B': [80.85],
        'Meta-Llama-3-70B-Instruct': [79.37],
        'Mixtral-8x22B-Instruct-v0.1': [84.93]
    },
    LongHealth: {
        'Mistral-7B-Instruct-v0.1': [45.75, 40.65, 3.65],
        'BioMistral-7B-DARE': [46.0, 40.85, 4.6],
        'Mistral-7B-Instruct-v0.2': [67.2, 62.4, 42.45],
        'Mixtral-8x7B-Instruct-v0.1': [76.5, 73.65, 24.2],
        'Mixtral-8x22B-Instruct-v0.1': [79.0, 73.9, 86.3],
        'zephyr-7b-beta': [42.9, 30.5, 26.35],
        'Llama3-OpenBioLLM-8B': [37.55, 41.75, 1.55],
        'Meta-Llama-3-8B-Instruct': [68.3, 66.55, 56.25],
        'Llama3-OpenBioLLM-70B': [80.2, 75.6, 62.9],
        'Meta-Llama-3-70B-Instruct': [81.65, 77.9, 91.7]
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
            <Panel class="w-full my-3 p-3 text-lg" toggleable>
                <template #header><h3 class="m-0">Motivation</h3></template>

                <p>
                    <Divider />
                    Despite the advancements promised by biomedical LLMs for patient care, a significant gap exists in their evaluation, particularly concerning their application in real-world clinical settings. Existing assessments, focused on
                    medical knowledge through constructed questions, fall short of capturing the complexity and diversity of clinical tasks. Additionally, the rapid pace at which LLMs evolve further complicates selecting the most appropriate models
                    for healthcare applications. In response to these challenges, <span style="font-family: 'EB Garamond'">CLUE</span> aims to offer a comprehensive and standardized framework for assessing the performance of both specialized
                    biomedical and advanced general-domain LLMs in practical healthcare tasks.
                </p>
                <p>
                    Clinical texts, often characterized by their irregular structure, abundant jargon, and region-specific abbreviations, present a complex domain that significantly differs from the concise quiz questions typically used in standard
                    datasets. Recent research has also highlighted significant issues with data contamination in LLM evaluations, casting doubt on the reliability of past findings. In response, our project aims to develop more robust and realistic
                    medical evaluation methods for LLMs to enhance their integrity and applicability in real-world scenarios, thereby addressing these challenges and proposing new strategies for more effective testing.
                </p>
            </Panel>
            <Panel class="w-full my-3 p-3" toggleable>
                <template #header>
                    <h3 class="m-0">Key Takeaways</h3>
                </template>
                <divider></divider>
                <ul class="custom-style text-lg mb-0">
                    <li>
                        To date, specialized biomedical LLMs do not show any performance advantage over general purpose LLMs according to this evaluation. Conversely, certain models adapted for the medical domain show inferior performance compared to
                        their non-specialized counterparts.
                    </li>
                    <li>The context length is an important factor, as clinical documents quickly exceed a length of 4k tokens. If few-shot learning is also used, context lengths of 8k tokens are quickly no longer sufficient.</li>
                    <li>
                        It is possible to generate new data sets for evaluation on the basis of clinical documents that contain a summary, such as doctor's letters. We think that this represents an opportunity to counteract benchmark contamination by
                        applying this approach to private clinical data.
                    </li>
                </ul>
            </Panel>

            <Panel class="w-full my-3 p-3" toggleable>
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
                            inquiries into concise, medically accurate summaries. The ability to effectively interpret and summarize patient inquiries, despite not involving clinical documents, is highly valuable for applications in healthcare
                            settings.
                        </p>
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@inproceedings{abacha2019summarization,
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
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@inproceedings{gao2022summarizing,
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
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@article{romanov2018lessons,
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
                            The LongHealth dataset features 20 intricate fictional patient records to test language models on their ability to handle extended texts, as these models typically underperform with longer inputs. It tasks models with
                            answering multiple-choice questions, structured into three distinct evaluation sub-tasks. These tasks progressively increase in complexity by first focusing on relevant documents, then introducing extraneous information to
                            filter out, and finally testing the model's ability to identify unprovided information. In contrast to the original paper, we have set the maximum context length to 8k tokens.
                        </p>
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@article{adams2024longhealth,
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
                <Divider />
                <MultiSelect class="w-full" v-model="selectedModels" display="chip" :options="models" placeholder="Select Models" filter> </MultiSelect>
            </div>

            <div class="card w-full my-3">
                <h3>Average Scores</h3>
                <Divider />
                <p>
                    We provide two averaged scores: Level 1 and Level 2. Level 1 includes the tasks MedNLI, MeQSum and Problem Summary, which feature shorter examples. Level 2 includes the remaining tasks: MeDiSumCode, MeDiSumQA and LongHealth. We
                    compute the average across all F1 and Accuracy scores, excluding ROUGE scores. Further details can be found in our <a href="https://arxiv.org/abs/2404.04067" target="_blank" rel="noopener noreferrer">paper</a>.
                </p>
                <Chart type="bar" :data="bar2Data(selectedTask, selectedMetric)" :options="bar2Option" :plugins="barPlugins"></Chart>
            </div>

            <div class="card w-full">
                <h3>Individual Task Scores</h3>
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
                    <div class="container is-max-desktop content">
                        <p>If you find our work useful, please cite our paper:</p>
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@article{dada2024clue,
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
            <div class="flex align-items-center justify-content-center">
                <img src="/layout/images/ume.png" class="ume-logo" />
                <img src="/layout/images/NVIDIA_logo.svg" class="nvidia-logo" />
            </div>
        </div>
    </div>
</template>
