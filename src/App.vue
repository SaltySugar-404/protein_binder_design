<template>
    <div class="common-layout">
        <el-header class="header">Protein Binder Design</el-header>
        <el-main class="main">
            <div class="input_area">
                <el-col><!--inputs-->
                    <div class="sub_title"><!--sub_title-->
                        <el-row :gutter="10">
                            <h2 style="font-size: 24px; margin-left: 5px;">Inputs</h2>
                        </el-row>
                    </div>
                    <div class="model_area"><!--AlphaFold2-->
                        <el-row :gutter="10">
                            <h2 class="model_id">Alphafold2</h2>
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Sequence</h3>
                            <el-input v-model="alphafold2_payloads.sequence" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Algorithm</h3>
                            <el-select v-model="alphafold2_payloads.algorithm" placeholder="please select algorithm">
                                <el-option label="jackhmmer" value="jackhmmer" />
                                <el-option label="mmseqs2" value="mmseqs2" />
                            </el-select>
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-button @click="check_model_status('alphafold2')" plain style="width: 100%">Alert
                                    Status</el-button>
                            </el-col>
                            <el-col :span="12">
                                <el-button @click="post_model_service('alphafold2', alphafold2_payloads)" plain
                                    style="width: 100%">Run</el-button>
                            </el-col>
                        </el-row>
                    </div>
                    <div class="model_area"><!--RFDiffusion-->
                        <el-row :gutter="10">
                            <h2 class="model_id">RFDiffusion</h2>
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Sequence</h3>
                            <el-input v-model="rfdiffusion_payloads.input_pdb" type="textarea" :rows="1"
                                placeholder="Awating AlphaFold2 response" disabled />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Contigs</h3>
                            <el-input v-model="rfdiffusion_payloads.contigs" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Hotspots</h3>
                            <el-input v-model="rfdiffusion_payloads.hotspot_res" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Diffusion Steps</h3>
                            <el-slider v-model="rfdiffusion_payloads.diffusion_steps" show-input :min="10" :max="50" />
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-button @click="check_model_status('rfdiffusion')" plain style="width: 100%">Alert
                                    Status</el-button>
                            </el-col>
                            <el-col :span="12">
                                <el-button @click="post_model_service('rfdiffusion', rfdiffusion_payloads)" plain
                                    style="width: 100%">Run
                                </el-button>
                            </el-col>
                        </el-row>
                    </div>
                    <div class="model_area"><!--ProteinMPNN-->
                        <el-row :gutter="10">
                            <h2 class="model_id">ProteinMPNN</h2>
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Input PDB</h3>
                            <el-input v-model="proteinmpnn_payloads.input_pdb" type="textarea" :rows="1"
                                placeholder="Awating RFDiffusion response" disabled />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Chains</h3>
                            <el-input v-model="proteinmpnn_payloads.input_pdb_chains" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Number of Sequences per Target</h3>
                            <el-slider v-model="proteinmpnn_payloads.num_seq_per_target" show-input :min="1"
                                :max="30" />
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-checkbox class="model_config" v-model="proteinmpnn_payloads.ca_only">Alpha-Carbon
                                    Only</el-checkbox>
                            </el-col>
                            <el-col :span="12">
                                <el-checkbox class="model_config" v-model="proteinmpnn_payloads.use_soluble_model">Use
                                    Soluble Model</el-checkbox>
                            </el-col>
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-button @click="check_model_status('proteinmpnn')" plain style="width: 100%">Alert
                                    Status</el-button>
                            </el-col>
                            <el-col :span="12">
                                <el-button @click="post_model_service('proteinmpnn', proteinmpnn_payloads)" plain
                                    style="width: 100%">Run
                                </el-button>
                            </el-col>
                        </el-row>
                    </div>
                </el-col>
            </div>
            <div class="output_area">
                <el-col><!--outputs-->
                    <div class="sub_title"><!--sub title-->
                        <el-row :gutter="10">
                            <h2 style="font-size: 24px; margin-left: 5px;">Outputs</h2>
                        </el-row>
                    </div>
                    <div class="output_tabs"><!--output_tabs-->
                        <el-row :gutter="10">
                            <el-col style="border: 2px solid #949494;">
                                <el-tabs v-model="tab_index" class="output_tab" @tab-click="select_tab_index">
                                    <el-tab-pane label="3D" name="3d">
                                        <div ref="molContainer" class="mol-container"></div>
                                    </el-tab-pane>
                                    <el-tab-pane label="Json" name="json">
                                    </el-tab-pane>
                                </el-tabs>
                            </el-col>
                        </el-row>
                    </div>
                </el-col>
            </div>
        </el-main>
    </div>
</template>

<script setup lang="ts">
import axios from 'axios'
import { ref, onMounted, h } from 'vue'
import { ElMessage } from 'element-plus'
import type { TabsPaneContext } from 'element-plus'

//configs
const NVIDIA_API_KEY = "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";
const headers = {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${NVIDIA_API_KEY}`,
    "poll-seconds": "900"
};

const model_status_urls: { [key: string]: string } = {
    alphafold2: "http://172.16.200.109:8181/v1/health/ready",
    rfdiffusion: "http://172.16.200.109:8182/v1/health/ready",
    proteinmpnn: "http://172.16.200.109:8183/v1/health/ready",
    alphafold2_multimer: "http://172.16.200.109:8184/v1/health/ready"
}

const model_service_urls: { [key: string]: string } = {
    alphafold2: "http://172.16.200.109:8181/protein-structure/alphafold2/predict-structure-from-sequence",
    rfdiffusion: "http://172.16.200.109:8182/biology/ipd/rfdiffusion/generate",
    proteinmpnn: "http://172.16.200.109:8182/biology/ipd/proteinmpnn/predict",
    alphafold2_multimer: "http://172.16.200.109:8184/protein-structure/alphafold2/multimer/predict-structure-from-sequences"
};

async function check_model_status(model_id: string) {
    let message = {}
    try {
        const response = await axios.get(model_status_urls[model_id]);

        if (response.data['status'] == 'ready') {
            message = { type: 'success', message: 'Service is ready', showClose: true }
        } else {
            message = { type: 'error', message: 'Service is not ready', showClose: true }
        }
    } catch (error) {
        message = { type: 'error', message: 'Error:' + error, showClose: true }
    }
    ElMessage(message)
    return message
}

async function post_model_service(model_id: string, payloads: any) {
    const response = await axios.get(model_status_urls[model_id]);
    console.log('response:' + response.data);
    if (check_model_status(model_id).type == 'success') {
        axios.post(model_service_urls[model_id], payloads, { headers: headers })
            .then(response => {
                console.log(response.data);
            })
    }
}

//AlphaFold2
let alphafold2_payloads = ref({
    sequence: "",
    algorithm: "mmseqs2"
});

function check_alphafold2_inputs() {
    let sequence = alphafold2_payloads.value.sequence;
    return /^[A-Z]+$/.test(sequence);
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
        url: model_service_urls.alphafold2,
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
let rfdiffusion_payloads = ref({
    input_pdb: "",
    contigs: "",
    hotspot_res: [],
    diffusion_steps: 15
});

function post_rfdiffusion() {
    axios({
        method: 'post',
        url: model_service_urls.rfdiffusion,
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
let proteinmpnn_payloads = ref({
    input_pdb: "",
    ca_only: false,
    input_pdb_chains: [],
    use_soluble_model: false,
    num_seq_per_target: 10,
    sampling_temp: 0.5,
});

function post_proteinmpnn() {
    axios({
        method: 'post',
        url: model_service_urls.proteinmpnn,
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

//Output menu
let tab_index = ref("3d");//3d json
const select_tab_index = (tab: TabsPaneContext, event: Event) => {
    console.log(tab, event)
}

//3d_display

</script>


<style scoped>
.header {
    background-color: #dfdfdf;
    height: 60px;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    border: 2px solid #949494;

    color: #333;
    text-align: left;
    font-size: 30px;
    font-weight: bold;
}

.main {
    background-color: white;
    padding: 0;
    margin: 0;

    display: flex;
}

.input_area {
    flex: 2;

}

.output_area {
    flex: 3;
    padding-left: 10px;
}

.el-row {
    margin-bottom: 5px;
}

.el-col {
    border-radius: 5px;
}

.model_area {
    margin: 0 auto;
    padding: 10px;
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

.output_tabs {
    height: 100%;
    width: 100%;
    background-color: white;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.mol-container {
    width: 100%;
    height: 900px;
}
</style>