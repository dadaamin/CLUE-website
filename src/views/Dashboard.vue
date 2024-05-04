<script setup>
import { useLayout } from '@/layout/composables/layout';
import { computed, ref, watch } from 'vue';

const { layoutConfig } = useLayout();

let documentStyle = getComputedStyle(document.documentElement);
let textColor = documentStyle.getPropertyValue('--text-color');
let textColorSecondary = documentStyle.getPropertyValue('--text-color-secondary');
let surfaceBorder = documentStyle.getPropertyValue('--surface-border');

const pastelColors = [
    '#BFD8D2', // Pastel green
    '#F3E5AB', // Pastel yellow
    '#B5B9FF', // Pastel blue
    '#FFC8A2', // Pastel orange
    '#ECCFCF', // Pastel red
    '#C4C6E7', // Pastel purple
    '#E1E6FA', // Pastel gray-blue
    '#D1E8E4', // Pastel teal
    '#FAE1DF', // Pastel peach
    '#EDE7B1', // Pastel lemon
    '#C9C9FF', // Pastel periwinkle
    '#FFDFD3', // Pastel coral
    '#A8E6CF', // Pastel mint
    '#D3A4FF', // Pastel lavender
    '#D5ECC2', // Pastel lime
    '#FFF5BA', // Pastel cream
    '#FFD3B6', // Pastel salmon
    '#DCEDC1', // Pastel spring green
    '#F7D6E0', // Pastel fuchsia
    '#FBE7C6', // Pastel buff
    '#A4B9EF', // Pastel sky blue
    '#FFABAB', // Pastel soft red
    '#CABFAD', // Pastel taupe
    '#B8E0F9', // Pastel sky
    '#9ED2C6', // Pastel sea green
    '#FFDEB4', // Pastel apricot
    '#FAE3D9', // Pastel biscotti
    '#B6CFB6', // Pastel forest
    '#AFCBEB', // Pastel cornflower
    '#C2C2F0' // Pastel periwinkle light
];

const models = ref([
    'Mixtral-8x7B-Instruct-v0.1',
    'Mixtral-8x22B-Instruct-v0.1',
    'Llama3-OpenBioLLM-8B',
    'Meta-Llama-3-8B-Instruct',
    'Llama3-OpenBioLLM-70B',
    'Meta-Llama-3-70B-Instruct',
    'zephyr-7b-beta',
    'Mistral-7B-Instruct-v0.1',
    'BioMistral-7B-DARE',
    'llama2-7b',
    'llama2-70b',
    'meditron-7b',
    'meditron-70b',
    'Mistral-7B-v0.1',
    'BioMistral-7B'
]);
const selectedModels = ref(['Llama3-OpenBioLLM-8B', 'Meta-Llama-3-8B-Instruct', 'Llama3-OpenBioLLM-70B', 'Meta-Llama-3-70B-Instruct']);

const colorMap = models.value.reduce((acc, model, index) => {
    acc[model] = pastelColors[index % pastelColors.length];
    return acc;
}, {});

const modelScores = {
    'Mixtral-8x7B-Instruct-v0.1': [64.44, 41.78],
    'Mixtral-8x22B-Instruct-v0.1': [62.89],
    'Llama3-OpenBioLLM-8B': [46.78],
    'Meta-Llama-3-8B-Instruct': [60.9],
    'Llama3-OpenBioLLM-70B': [59.27],
    'Meta-Llama-3-70B-Instruct': [64.31],
    'zephyr-7b-beta': [60.2, 35.25],
    'Mistral-7B-Instruct-v0.1': [57.96, 27.9],
    'BioMistral-7B-DARE': [60.41, 30.11],
    'llama2-7b': [33.76],
    'llama2-70b': [48.95],
    'meditron-7b': [26.32],
    'meditron-70b': [43.93],
    'BioMistral-7B': [57.77]
};

const modelMetrics = {
    MeDiSumCode: {
        'zephyr-7b-beta': [2.31, 12.0, 71.27],
        'Mistral-7B-Instruct-v0.1': [0.57, 3.78, 37.25],
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
        'BioMistral-7B-DARE': [17.54, 19.9, 9.34, 64.6, 18.65],
        'Mixtral-8x7B-Instruct-v0.1': [25.54, 29.26, 13.89, 69.57, 24.47],
        'Llama3-OpenBioLLM-8B': [25.1, 27.84, 13.12, 66.3, 24.79],
        'Meta-Llama-3-8B-Instruct': [24.49, 27.95, 12.75, 69.38, 23.37],
        'Llama3-OpenBioLLM-70B': [24.43, 27.73, 13.29, 68.98, 24.5],
        'Meta-Llama-3-70B-Instruct': [27.24, 30.95, 14.24, 70.71, 25.84],
        'Mixtral-8x22B-Instruct-v0.1': [23.8, 27.62, 12.69, 69.23, 23.55]
    },
    MeQSum: {
        'zephyr-7b-beta': [7.16, 8.58, 2.89, 37.5],
        'Mistral-7B-Instruct-v0.1': [21.85, 25.26, 11.17, 66.15],
        'BioMistral-7B-DARE': [26.16, 29.69, 13.49, 68.68],
        'Mixtral-8x7B-Instruct-v0.1': [32.47, 36.38, 16.86, 72.8],
        'Llama3-OpenBioLLM-8B': [26.21, 29.41, 14.03, 62.39],
        'Meta-Llama-3-8B-Instruct': [32.2, 36.49, 16.37, 72.74],
        'Llama3-OpenBioLLM-70B': [30.72, 34.31, 15.55, 71.99],
        'Meta-Llama-3-70B-Instruct': [36.57, 40.2, 19.3, 75.74],
        'Mixtral-8x22B-Instruct-v0.1': [36.15, 39.94, 19.45, 75.02]
    },
    'Problem Summary': {
        'zephyr-7b-beta': [5.97, 7.35, 2.11, 59.45, 9.06],
        'Mistral-7B-Instruct-v0.1': [14.46, 18.6, 6.26, 65.79, 20.08],
        'BioMistral-7B-DARE': [18.81, 22.92, 8.93, 68.93, 22.65],
        'Mixtral-8x7B-Instruct-v0.1': [17.44, 23.39, 7.7, 68.56, 19.51],
        'Llama3-OpenBioLLM-8B': [10.82, 13.62, 4.03, 64.14, 15.67],
        'Meta-Llama-3-8B-Instruct': [32.2, 36.49, 16.37, 72.74],
        'Llama3-OpenBioLLM-70B': [22.7, 28.52, 9.87, 71.45, 25.32],
        'Meta-Llama-3-70B-Instruct': [25.43, 33.16, 13.01, 73.0, 29.12],
        'Mixtral-8x22B-Instruct-v0.1': [17.97, 22.6, 8.17, 69.29, 22.31]
    },
    MedNLI: {
        'zephyr-7b-beta': [68.45],
        'Mistral-7B-Instruct-v0.1': [64.79],
        'BioMistral-7B-DARE': [66.76],
        'Mixtral-8x7B-Instruct-v0.1': [76.48],
        'Llama3-OpenBioLLM-8B': [44.93],
        'Meta-Llama-3-8B-Instruct': [74.08],
        'Llama3-OpenBioLLM-70B': [80.85],
        'Meta-Llama-3-70B-Instruct': [79.37],
        'Mixtral-8x22B-Instruct-v0.1': [84.93]
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
    indexAxis: 'y'
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
        <div class="w-full lg:w-7">
            <div class="card flex flex-wrap gap-3 justify-content-center header">
                <h1 style="font-family: 'Google Sans', sans-serif">CLUE: A Clinical Language Understanding Evaluation for LLMs</h1>
                <img src="/layout/images/clue-logo.png" alt="Image Description 1" class="clue-logo" />

                <h4>Amin Dada, Marie Bauer, Amanda Butler Contreras, Osman Alperen Koraş, Constantin Marc Seibold, Kaleb E Smith, Jens Kleesiek</h4>

                <a href="https://github.com/TIO-IKIM/CLUE" target="_blank" rel="noopener noreferrer">
                    <Button label="Code" icon="pi pi-github" severity="contrast" />
                </a>
                <a href="https://arxiv.org/abs/2404.04067" target="_blank" rel="noopener noreferrer">
                    <Button label="Paper" icon="pi pi-book" severity="contrast" />
                </a>
            </div>
            <div id="model-select" class="card w-full my-3">
                <h3>Motivation</h3>
                <p class="m-2">
                    <Divider />
                    The medical evaluation of LLMs has primarily relied on datasets consisting of multiple choice questions from sources such as MMLU, MEDQA, MedMCQA, and synthetic questions derived from PubMed articles. However, this approach
                    overlooks a significant aspect: none of these evaluations utilize clinical text. The distinction is crucial because, despite the reliance on extensive medical knowledge for these quizzes, the format and content of clinical
                    documents are markedly different.
                </p>
                <p class="m-2">
                    Clinical texts often feature an irregular structure, abundant jargon and abbreviations that vary across specialties and regions, and are generally lengthier than the quiz questions used in standard datasets, resulting in a diverse
                    and complex domain of texts.
                </p>
                <p class="m-2">
                    Moreover, recent research has uncovered substantial issues related to data contamination in LLM evaluations, which calls into question the validity of previous results. In light of these challenges, new strategies have been
                    proposed to enhance the integrity and relevance of LLM testing.
                </p>
                <p class="m-2">
                    Our project is committed to addressing these gaps by developing more robust and realistic medical evaluation methods for LLMs, ensuring they are better suited for real-world applications. This initiative represents a crucial step
                    forward in understanding and improving the performance of LLMs in medical contexts.
                </p>
            </div>
            <div id="model-select" class="card w-full my-3">
                <h3>Model Selection</h3>
                <Divider />
                <MultiSelect class="w-full" v-model="selectedModels" display="chip" :options="models" placeholder="Select Models" filter> </MultiSelect>
            </div>

            <div class="card w-full my-3">
                <h3 id="results">Average Scores</h3>
                <Divider />
                <Chart type="bar" :data="bar2Data(selectedTask, selectedMetric)" :options="bar2Option" :plugins="barPlugins"></Chart>
            </div>

            <div class="card w-full my-3">
                <h3>Task Scores</h3>
                <Divider />
                <div class="w-full flex align-items-end align-content-start">
                    <h5 class="m-4">{{ selectedTask.name }} ({{ selectedMetric }})</h5>
                    <div class="flex-grow-1"></div>
                    <Dropdown class="mx-3" v-model="selectedTask" :options="tasks" optionLabel="name" placeholder="Select Task" />
                    <Dropdown v-model="selectedMetric" :options="selectedTask.metrics" placeholder="Select Metric" />
                </div>
                <Chart type="bar" :data="barData(selectedTask, selectedMetric)" :options="barOptions" :plugins="barPlugins"></Chart>
            </div>
            <div class="card w-full my-3">
                <h3>Task Descriptions</h3>
                <Divider />
                <TabView>
                    <TabPanel header="MeDiSumCode">
                        <p class="m-0">
                            MeDiSumCode involves coding discharge summaries by assigning International Classification of Diseases (ICD-10) codes to diagnoses and procedures, a critical function for patient record management, billing, and statistics.
                            It challenges language models to accurately extract diagnoses from lengthy, complex clinical texts and match them with the appropriate ICD-10 codes, which comprise over 70,000 options requiring extensive extensive
                            knowledge of medical and coding systems. The task further tests the models' ability to integrate diagnosis identification with a detailed understanding of the ICD-10 structure for accurate code prediction.
                        </p>
                    </TabPanel>
                    <TabPanel header="MeDiSumQA">
                        <p class="m-0">
                            MeDiSumQA is a medical QA dataset that uses discharge summaries from MIMIC IV to generate question-answer pairs about a patient's hospital stay, focusing on extracting and querying key information. The questions are
                            generated that involves identifying critical statements related to the patient’s medical history and hospitalization, then crafting questions that these statements can answer without. You can find a more detailed
                            description in our paper.
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
                            answering multiple-choice questions across various cognitive challenges like information extraction and negation, structured into three distinct evaluation sub-tasks. These tasks progressively increase in complexity by
                            first focusing on relevant documents, then introducing extraneous information to filter out, and finally testing the model's propensity to incorrectly infer unprovided information.
                        </p>
                        <pre style="background-color: #f5f5f5; overflow-x: auto"><code>@article{adams2024longhealth,
  title={LongHealth: A Question Answering Benchmark with Long Clinical Documents},
  author={Adams, Lisa and Busch, Felix and Han, Tianyu and Excoffier, Jean-Baptiste and Ortala, Matthieu and L{\"o}ser, Alexander and Aerts, Hugo JWL and Kather, Jakob Nikolas and Truhn, Daniel and Bressem, Keno},
  journal={arXiv preprint arXiv:2401.14490},
  year={2024}
}</code></pre>
                    </TabPanel>
                </TabView>
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
            <div class="image-container flex justify-content-center w-full">
                <div class="image-container flex justify-content-center w-full">
                    <img src="/layout/images/ume.png" alt="Image Description 1" class="ume-logo" />
                    <img src="/layout/images/NVIDIA_logo.svg" alt="Image Description 2" class="nvidia-logo" />
                </div>
            </div>
        </div>
    </div>
</template>
