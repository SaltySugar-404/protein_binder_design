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
                            <el-input v-model="alphafold2_inputs.sequence" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Algorithm</h3>
                            <el-select v-model="alphafold2_inputs.algorithm" placeholder="please select algorithm">
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
                                <el-button @click="post_alphafold2_service()" plain style="width: 100%">Run</el-button>
                            </el-col>
                        </el-row>
                    </div>
                    <div class="model_area"><!--RFDiffusion-->
                        <el-row :gutter="10">
                            <h2 class="model_id">RFDiffusion</h2>
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Sequence</h3>
                            <el-input v-model="rfdiffusion_inputs.input_pdb" type="textarea" :rows="1"
                                placeholder="Awating AlphaFold2 response" disabled />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Contigs</h3>
                            <el-input v-model="rfdiffusion_inputs.contigs" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Hotspots</h3>
                            <el-input v-model="rfdiffusion_inputs.hotspot_res" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Diffusion Steps</h3>
                            <el-slider v-model="rfdiffusion_inputs.diffusion_steps" show-input :min="10" :max="50" />
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-button @click="check_model_status('rfdiffusion')" plain style="width: 100%">Alert
                                    Status</el-button>
                            </el-col>
                            <el-col :span="12">
                                <el-button @click="post_rfdiffusion()" plain style="width: 100%">Run
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
                            <el-input v-model="proteinmpnn_inputs.input_pdb" type="textarea" :rows="1"
                                placeholder="Awating RFDiffusion response" disabled />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Chains</h3>
                            <el-input v-model="proteinmpnn_inputs.input_pdb_chains" type="textarea" :rows="1"
                                placeholder="Please input" clearable />
                        </el-row>
                        <el-row :gutter="10">
                            <h3 class="model_config">Number of Sequences per Target</h3>
                            <el-slider v-model="proteinmpnn_inputs.num_seq_per_target" show-input :min="1" :max="30" />
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-checkbox class="model_config" v-model="proteinmpnn_inputs.ca_only">Alpha-Carbon
                                    Only</el-checkbox>
                            </el-col>
                            <el-col :span="12">
                                <el-checkbox class="model_config" v-model="proteinmpnn_inputs.use_soluble_model">Use
                                    Soluble Model</el-checkbox>
                            </el-col>
                        </el-row>
                        <el-row :gutter="10">
                            <el-col :span="12">
                                <el-button @click="check_model_status('proteinmpnn')" plain style="width: 100%">Alert
                                    Status</el-button>
                            </el-col>
                            <el-col :span="12">
                                <el-button @click="post_proteinmpnn()" plain style="width: 100%">Run
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
const timeout=3000

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

// async function check_model_status(model_id: string, is_alert: boolean = true) {
//     let message = {}
//     let is_ready = true
//     try {
//         const response = await axios.get(model_status_urls[model_id]);

//         if (response.data['status'] == 'ready') {
//             message = { type: 'success', message: 'Service is ready', showClose: true }
//             is_ready = true
//         } else {
//             message = { type: 'error', message: 'Service is not ready', showClose: true }
//             is_ready = false
//         }
//     } catch (error) {
//         message = { type: 'error', message: 'Error:' + error, showClose: true }
//         is_ready = false
//     }
//     if (is_alert) ElMessage(message)
//     return is_ready
// }

// import axios from 'axios';

async function check_model_status(
    model_id: string,
    is_alert: boolean = true,
    maxWaitTime: number = timeout
): Promise<boolean> {
    let message = {};
    let is_ready = true;
    const controller = new AbortController();

    const timeoutId = setTimeout(() => {
        controller.abort();
    }, maxWaitTime);

    try {
        const response = await axios.get(model_status_urls[model_id], {
            signal: controller.signal,
        });

        clearTimeout(timeoutId);

        if (response.data.status === 'ready') {
            message = { type: 'success', message: 'Service is ready', showClose: true };
            is_ready = true;
        } else {
            message = { type: 'error', message: 'Service is not ready', showClose: true };
            is_ready = false;
        }
    } catch (error) {
        clearTimeout(timeoutId);

        if (axios.isCancel(error)) {
            message = { type: 'error', message: 'Error: Request timed out', showClose: true };
        } else {
            message = { type: 'error', message: `Error: ${error.message}`, showClose: true };
        }
        is_ready = false;
    }

    if (is_alert) ElMessage(message);
    return is_ready;
}

// async function post_model_service(model_id: string, inputs: any) {
//     if (await check_model_status(model_id)) {
//         axios.post(model_service_urls[model_id], inputs, { headers: headers })
//             .then(response => {
//                 console.log(response.data);
//             })
//     }
// }

//AlphaFold2
let alphafold2_inputs = ref({
    sequence: "",
    algorithm: "mmseqs2"
});

function check_alphafold2_inputs() {
    let sequence = alphafold2_inputs.value.sequence;
    return /^[A-Z]+$/.test(sequence);
}

async function post_alphafold2_service() {
    if (!check_alphafold2_inputs()) {
        ElMessage({
            type: 'error',
            message: 'Invalid sequence input. Please enter a valid sequence.',
            showClose: true
        })
    } else {
        if (!await check_model_status('alphafold2', false)) {
            ElMessage({
                type: 'error',
                message: 'Service is not ready',
                showClose: true
            });

        } else {
            axios({
                method: 'post',
                url: model_service_urls.alphafold2,
                headers: headers,
                data: {
                    sequence: alphafold2_inputs.value.sequence,
                    algorithm: alphafold2_inputs.value.algorithm
                }
            }).then((res) => {
                console.log(res)//输入到RFDiffusion
            })
        }
    }
}

//RFDiffusion
let rfdiffusion_inputs = ref({
    input_pdb: "",
    contigs: "",
    hotspot_res: [],
    diffusion_steps: 15
});

function check_rfdiffusion_inputs() {
    if (rfdiffusion_inputs.value.input_pdb.length &&
        rfdiffusion_inputs.value.contigs.length &&
        rfdiffusion_inputs.value.hotspot_res.length
    ) return true
    return false
}

async function post_rfdiffusion() {
    if (!check_rfdiffusion_inputs()) {
        ElMessage({
            type: 'error',
            message: 'Awiting AlphaFold2 response',
            showClose: true
        })
    }
    else {
        if (!await check_model_status('rfdiffusion', false)) {
            ElMessage({
                type: 'error',
                message: 'Service is not ready',
                showClose: true
            });
        } else {
            axios({
                method: 'post',
                url: model_service_urls.alphafold2,
                headers: headers,
                data: {
                    input_pdb: rfdiffusion_inputs.value.input_pdb,
                    contigs: rfdiffusion_inputs.value.contigs,
                    hotspot_res: rfdiffusion_inputs.value.hotspot_res,
                    diffusion_steps: rfdiffusion_inputs.value.diffusion_steps
                }
            }).then((res) => {
                console.log(res)//输入到ProteinMPNN
            })
        }
    }
}

//ProteinMPNN
let proteinmpnn_inputs = ref({
    input_pdb: "",
    ca_only: false,
    input_pdb_chains: [],
    use_soluble_model: false,
    num_seq_per_target: 10,
    sampling_temp: 0.5,
});

function check_proteinmpnn_inputs() {
    if (proteinmpnn_inputs.value.input_pdb.length &&
        proteinmpnn_inputs.value.input_pdb_chains.length
    ) return true
    return false
}

async function post_proteinmpnn() {
    if (!check_proteinmpnn_inputs()) {
        ElMessage({
            type: 'error',
            message: 'Awiting RFDiffusion response',
            showClose: true
        })
    }
    else {
        if (!await check_model_status('proteinmpnn', false)) {
            ElMessage({
                type: 'error',
                message: 'Service is not ready',
                showClose: true
            });
        } else {
            axios({
                method: 'post',
                url: model_service_urls.alphafold2,
                headers: headers,
                data: {
                    input_pdb: proteinmpnn_inputs.value.input_pdb,
                    ca_only: proteinmpnn_inputs.value.ca_only,
                    input_pdb_chains: proteinmpnn_inputs.value.input_pdb_chains,
                    use_soluble_model: proteinmpnn_inputs.value.use_soluble_model,
                    num_seq_per_target: proteinmpnn_inputs.value.num_seq_per_target
                }
            }).then((res) => {
                console.log(res)//输出，或输入至Alphafold2_Multimer
            })
        }
    }
}

//Alphafold2_Multimer
let alphafold2_multimer_inputs = ref({
    sequences: [],
    algorithm: 'mmseqs2'
});

//Output menu
let tab_index = ref("3d");//3d json
const select_tab_index = (tab: TabsPaneContext, event: Event) => {
    console.log(tab, event)
}

//3d_display

</script>


<style scoped>
.header {
    background-color: white;
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
    padding: 20px;
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
}
</style>