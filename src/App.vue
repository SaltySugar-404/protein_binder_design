<template>
    <div class="common-layout">
        <el-header class="header">Protein Binder Design</el-header>
        <el-main class="main">
            <div class="input">Input
                <div class="model_card" id="AlphaFold2">
                    <el-form :model="alphafold2_payloads" label-width="auto">
                        <h2 class="model_id">Alphafold2</h2>
                        <el-form-item>
                            <h3 class="model_config">Sequence</h3>
                            <el-input v-model="alphafold2_payloads.sequence" type="textarea" rows="1"
                                placeholder="Please input" clearable />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Algorithm</h3>
                            <el-select v-model="alphafold2_payloads.algorithm" placeholder="please select algorithm">
                                <el-option label="jackhmmer" value="jackhmmer" />
                                <el-option label="mmseqs2" value="mmseqs2" />
                            </el-select>
                        </el-form-item>
                        <el-form-item>
                            <el-row :gutter="10" justify="center" style="width: 100%;">
                                <el-col :span="12">
                                    <el-button @click="alert_alphafold2_inputs" plain style="width: 100%">Check
                                        Alphafold2 Inputs</el-button>
                                </el-col>
                                <el-col :span="12">
                                    <el-button @click="post_alphafold2" plain style="width: 100%">Run
                                        Alphafold2</el-button>
                                </el-col>
                            </el-row>
                        </el-form-item>
                    </el-form>
                </div>
                <div class="model_card" id="RFDiffusion">
                    <el-form :model="rfdiffusion_payloads" label-width="auto">
                        <h2 class="model_id">RFDiffusion</h2>
                        <el-form-item>
                            <h3 class="model_config">Sequence</h3>
                            <el-input v-model="rfdiffusion_payloads.input_pdb" type="textarea" rows="1"
                                placeholder="Awating AlphaFold2 response" disabled />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Contigs</h3>
                            <el-input v-model="rfdiffusion_payloads.contigs" type="textarea" rows="1" />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Hotspots</h3>
                            <el-input v-model="rfdiffusion_payloads.hotspot_res" type="textarea" rows="1" />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Diffusion Steps</h3>
                            <el-slider v-model="rfdiffusion_payloads.diffusion_steps" show-input />
                        </el-form-item>
                        <el-form-item>
                            <el-row :gutter="10" justify="center" style="width: 100%;">
                                <el-col :span="12">
                                    <el-button @click="alert_rfdiffusion_inputs" plain style="width: 100%">Check
                                        RFDiffusion Inputs</el-button>
                                </el-col>
                                <el-col :span="12">
                                    <el-button @click="post_rfdiffusion" plain style="width: 100%">Run
                                        RFDiffusion</el-button>
                                </el-col>
                            </el-row>
                        </el-form-item>
                    </el-form>
                </div>
                <div class="model_card" id="ProteinMPNN">
                    <el-form :model="proteinmpnn_payloads" label-width="auto">
                        <h2 class="model_id">ProteinMPNN</h2>
                        <el-form-item>
                            <h3 class="model_config">Input PDB</h3>
                            <el-input v-model="proteinmpnn_payloads.input_pdb" type="textarea" rows="1"
                                placeholder="Awating RFDiffusion response" disabled />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Chains</h3>
                            <el-input v-model="proteinmpnn_payloads.input_pdb_chains" type="textarea" rows="1" />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Number of Sequences per Target</h3>
                            <el-slider v-model="proteinmpnn_payloads.num_seq_per_target" show-input />
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Alpha-Carbon Only</h3>
                            <el-radio-group v-model="proteinmpnn_payloads.ca_only"
                                style="width: 50%;display: flex; justify-content: flex-end;">
                                <el-radio value=true>True</el-radio>
                                <el-radio value=false>False</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item>
                            <h3 class="model_config">Use Soluble Model</h3>
                            <el-radio-group v-model="proteinmpnn_payloads.use_soluble_model"
                                style="width: 50%;display: flex; justify-content: flex-end;">
                                <el-radio value=true>True</el-radio>
                                <el-radio value=false>False</el-radio>
                            </el-radio-group>
                        </el-form-item>
                        <el-form-item>
                            <el-row :gutter="10" justify="center" style="width: 100%;">
                                <el-col :span="12">
                                    <el-button @click="alert_proteinmpnn_inputs" plain style="width: 100%">Check
                                        ProteinMPNN Inputs</el-button>
                                </el-col>
                                <el-col :span="12">
                                    <el-button @click="post_proteinmpnn" plain style="width: 100%">Run
                                        ProteinMPNN</el-button>
                                </el-col>
                            </el-row>
                        </el-form-item>
                    </el-form>
                </div>
            </div>
            <div class="output">Output
                <el-menu class="output_menu" :default-active=menu_index mode="horizontal" @select="select_menu_index"
                    style="height: 50px;">
                    <el-menu-item class="output_menu_item" index="1">3D
                    </el-menu-item>
                    <el-menu-item class="output_menu_item" index="2">Json
                    </el-menu-item>
                </el-menu>
            </div>
        </el-main>
    </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import { ref } from 'vue'
import { ElMessage } from 'element-plus'

//configs
const NVIDIA_API_KEY = "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";
const headers = {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${NVIDIA_API_KEY}`,
    "poll-seconds": "900"
};

const model_urls = {
    alphafold2: "http://172.16.200.109:8181/protein-structure/alphafold2/predict-structure-from-sequence",
    rfdiffusion: "http://172.16.200.109:8182/biology/ipd/rfdiffusion/generate",
    proteinmpnn: "http://172.16.200.109:8182/biology/ipd/proteinmpnn/predict",
    alphafold2_multimer: "http://172.16.200.109:8184/protein-structure/alphafold2/multimer/predict-structure-from-sequences"
};

//AlphaFold2
const alphafold2_payloads = ref({
    sequence: "",
    algorithm: "mmseqs2"
});

function check_alphafold2_inputs() {
    let sequence = alphafold2_payloads.value.sequence;
    return /^[A-Z]+$/.test(sequence);
}

const alert_alphafold2_inputs = function () {
    ElMessage({
        type: 'info',
        message: 'Sequence: ' + alphafold2_payloads.value.sequence + '\nAlgorithm: ' + alphafold2_payloads.value.algorithm,
        showClose: true
    })
}

function post_alphafold2() {
    if (!check_alphafold2_inputs()) {
        ElMessage({
            type: 'error',
            message: 'Invalid sequence input. Please enter a valid sequence.',
            showClose: true
        });
        return;
    }
    axios({
        method: 'post',
        url: model_urls.alphafold2,
        headers: headers,
        data: {
            sequence: alphafold2_payloads.value.sequence,
            algorithm: alphafold2_payloads.value.algorithm
        }
    }).then((res) => {
        console.log(res);
    })
}

//RFDiffusion
const rfdiffusion_payloads = ref({
    input_pdb: "",
    contigs: "",
    hotspot_res: [],
    diffusion_steps: 15
});

function alert_rfdiffusion_inputs() {
    ElMessage({
        type: 'info',
        message: 'Sequence: ' + rfdiffusion_payloads.value.input_pdb
            + '\nContigs: ' + rfdiffusion_payloads.value.contigs
            + '\nHotspots: ' + rfdiffusion_payloads.value.hotspot_res
            + '\nDiffusion Steps: ' + rfdiffusion_payloads.value.diffusion_steps,
        showClose: true
    })
}

function post_rfdiffusion() {
    axios({
        method: 'post',
        url: model_urls.rfdiffusion,
        headers: headers,
        data: {
            input_pdb: rfdiffusion_payloads.value.input_pdb,
            contigs: rfdiffusion_payloads.value.contigs,
            hotspot_res: rfdiffusion_payloads.value.hotspot_res,
            diffusion_steps: rfdiffusion_payloads.value.diffusion_steps
        }
    }).then((res) => {
        console.log(res);
    })
}

//ProteinMPNN
const proteinmpnn_payloads = ref({
    input_pdb: "",
    ca_only: false,
    input_pdb_chains: ['A'],
    use_soluble_model: false,
    num_seq_per_target: 20,
    sampling_temp: 0.5,
});

function alert_proteinmpnn_inputs() {
    ElMessage({
        type: 'info',
        message: 'Input PDB: ' + proteinmpnn_payloads.value.input_pdb
            + '\nChains: ' + proteinmpnn_payloads.value.input_pdb_chains
            + '\nNumber of Sequences per Target: ' + proteinmpnn_payloads.value.num_seq_per_target
            + '\nAlpha-Carbon Only: ' + proteinmpnn_payloads.value.ca_only
            + '\nUse Soluble Model: ' + proteinmpnn_payloads.value.use_soluble_model,
        showClose: true
    })
}

function post_proteinmpnn() {
    axios({
        method: 'post',
        url: model_urls.proteinmpnn,
        headers: headers,
        data: {
            input_pdb: proteinmpnn_payloads.value.input_pdb,
            ca_only: proteinmpnn_payloads.value.ca_only,
            input_pdb_chains: proteinmpnn_payloads.value.input_pdb_chains,
            use_soluble_model: proteinmpnn_payloads.value.use_soluble_model,
            num_seq_per_target: proteinmpnn_payloads.value.num_seq_per_target
        }
    }).then((res) => {
        console.log(res);
    })
}

//Output
const menu_index = ref("1");
const select_menu_index = (index: string) => {
    menu_index.value = index;
    console.log(index);
}
</script>


<style>
.header {
    background-color: #dfdfdf;
    margin: 0;
    padding: 0;
    height: 60px;

    color: #333;
    text-align: left;
    font-size: 30px;
    font-weight: bold;
}

.main {
    background-color: white;
    margin: 0;
    padding: 0;

    display: flex;
    justify-content: space-between;
}

.input {
    width: 50%;
    border: 2px solid #949494;

    color: #333;
    text-align: left;
    font-size: 24px;
    font-weight: bold;
}

.output {
    width: 50%;
    border: 2px solid #949494;

    color: #333;
    text-align: left;
    font-size: 24px;
    font-weight: bold;
}

.model_card {
    width: 95%;
    margin: 0 auto;
    padding: 10px;
    margin-top: 20px;
    margin-bottom: 20px;
    background-color: white;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border: 2px solid #949494;
}

.model_id {
    font-size: 20px;
    font-weight: bold;
    margin-left: 5px;
    margin-top: 0px;
    margin-bottom: 5px;
}

.model_config {
    font-size: 16px;
    font-weight: bold;
    margin-left: 5px;
    margin-top: 5px;
    margin-bottom: 5px;
}



.el-row {
    margin-bottom: 10px;
}

.el-col {
    border-radius: 4px;
}
</style>