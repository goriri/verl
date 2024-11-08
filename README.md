<div align=center>
  <img src="docs/_static/logo.png" width = "20%" height = "20%" />
</div>

<h1 style="text-align: center;">veRL: Volcano Engine Reinforcement Learning for LLM</h1>

veRL (HybridFlow) is a flexible, efficient and industrial-level RL(HF) training framework designed for large language models (LLMs). veRL is the open-source version of [HybridFlow](https://arxiv.org/abs/2409.19256v2) paper.

veRL is flexible and easy to use with:

- **Easy to support diverse RL(HF) algorithms**: The Hybrid programming model combines the strengths of single-controller and multi-controller paradigms to enable flexible representation and efficient execution of complex Post-Training dataflows. Allowing users to build RL dataflows in a few lines of code.

- **Seamless integration of existing LLM infra with modular API design**: Decouples computation and data dependencies, enabling seamless integration with existing LLM frameworks, such as PyTorch FSDP, Megatron-LM and vLLM. Moreover, users can easily extend to other LLM training and inference frameworks.

- **Flexible device mapping**: Supports various placement of models onto different sets of GPUs for efficient resource utilization and scalability across different cluster sizes.

- Readily integration with popular Hugging Face models


veRL is fast with:

- **State-of-the-art throughput**: By seamlessly integrating existing SOTA LLM training and inference frameworks, veRL achieves high generation and training throughput.

- **Efficient actor model resharding with 3D-HybridEngine**: Eliminates memory redundancy and significantly reduces communication overhead during transitions between training and generation phases.


<p align="center">
| <a href="https://verl.readthedocs.io/en/latest/index.html"><b>Documentation</b></a> | <a href="https://arxiv.org/abs/2409.19256v2"><b>Paper</b></a> | 
<!-- <a href=""><b>Slides</b></a> | -->
</p>



## Installation

For installing the latest version of veRL, the best way is to clone and install it from source. Then you can modify our code to customize your own post-training jobs.

```bash
# install verl together with some lightweight dependencies in setup.py
git clone https://github.com/volcengine/verl.git
cd verl
pip3 install -e .
```

You can also install veRL using `pip3 install`

```bash
# directly install from pypi
pip3 install verl
```

### Dependencies

veRL requires Python >= 3.9 and CUDA >= 12.1.

veRL support various backend, we currently release FSDP and Megatron-LM for actor training and vLLM for rollout generation.

To install the dependencies, we recommend using conda:

```bash
conda create -n verl python==3.9
conda activate verl
```

The following dependencies are required for all backends.

```bash
# install torch [or you can skip this step and let vllm to install the correct version for you]
pip install torch==2.4.0 torchvision==0.19.0 torchaudio==2.4.0 --index-url https://download.pytorch.org/whl/cu121

# install vllm
pip3 install vllm==0.5.4
pip3 install ray==2.10 # other version may have bug

# flash attention 2
pip3 install flash-attn --no-build-isolation
```

**FSDP**

We recommend using FSDP backend to investigate, research and prototype different models, datasets and RL algorithms.

The pros, cons and extension guide for using FSDP backend can be found in [PyTorch FSDP Backend](https://verl.readthedocs.io/en/latest/workers/fsdp_workers.html)

**Megatron-LM**

For users who pursue better scalability, we recommend using Megatron-LM backend. Please install the above dependencies first.

Currently, we support Megatron-LM@core_v0.4.0 and we fix some internal issues of Megatron-LM. Here's the additional installation guide.

The pros, cons and extension guide for using Megatron-LM backend can be found in [Megatron-LM Backend](https://verl.readthedocs.io/en/latest/workers/megatron_workers.html)

```bash
# FOR Megatron-LM Backend
# apex
pip3 install -v --disable-pip-version-check --no-cache-dir --no-build-isolation \
         --config-settings "--build-option=--cpp_ext" --config-settings "--build-option=--cuda_ext" \
         git+https://github.com/NVIDIA/apex

# transformer engine
pip3 install git+https://github.com/NVIDIA/TransformerEngine.git@v1.7

# megatron core v0.4.0
cd ..
git clone -b core_v0.4.0 https://github.com/NVIDIA/Megatron-LM.git
cd Megatron-LM
cp ../verl/patches/megatron_v4.patch .
git apply megatron_v4.patch
pip3 install -e .
export PYTHONPATH=$PYTHONPATH:$(pwd)
```

## Getting Started
Visit our [documentation](https://verl.readthedocs.io/en/latest/index.html) to learn more.

**Running an PPO example should follow:**
- Preparation
  - [Installation](https://verl.readthedocs.io/en/latest/preparation/install.html)
  - [Prepare Data (Parquet) for Post-Training](https://verl.readthedocs.io/en/latest/preparation/prepare_data.html)
  - [Implement Reward Function for Dataset](https://verl.readthedocs.io/en/latest/preparation/reward_function.html)
- PPO Example (Run an example)
  - [PPO Example Architecture](https://verl.readthedocs.io/en/latest/examples/ppo_code_architecture.html)
  - [Config Explanation](https://verl.readthedocs.io/en/latest/examples/config.html)
  - [Run GSM8K Example](https://verl.readthedocs.io/en/latest/examples/gsm8k_example.html)

**For code explanation and advance usage (extension):**
- PPO Trainer and Workers
  - [PPO Ray Trainer](https://verl.readthedocs.io/en/latest/workers/ray_trainer.html)
  - [PyTorch FSDP Backend](https://verl.readthedocs.io/en/latest/workers/fsdp_workers.html)
  - [Megatron-LM Backend](https://verl.readthedocs.io/en/latest/index.html)
- Advance Usage and Extension
  - [Ray API Design Tutorial](https://verl.readthedocs.io/en/latest/advance/placement.html)
  - [Extend to other RL(HF) algorithms](https://verl.readthedocs.io/en/latest/advance/dpo_extension.html)
  - [Add models to FSDP backend](https://verl.readthedocs.io/en/latest/advance/fsdp_extension.html)
  - [Add models to Megatron-LM backend](https://verl.readthedocs.io/en/latest/advance/megatron_extension.html)


## Contribution
### Code formatting
We use yapf (Google style) to enforce strict code formatting when reviewing MRs. To reformat you code locally, make sure you installed `yapf`
```bash
pip3 install yapf
```
Then, make sure you are at top level of verl repo and run
```bash
yapf -ir -vv --style ./.style.yapf verl single_controller examples
```



## Citation

```tex
@article{sheng2024hybridflow,
  title   = {HybridFlow: A Flexible and Efficient RLHF Framework},
  author  = {Guangming Sheng and Chi Zhang and Zilingfeng Ye and Xibin Wu and Wang Zhang and Ru Zhang and Yanghua Peng and Haibin Lin and Chuan Wu},
  year    = {2024},
  journal = {arXiv preprint arXiv: 2409.19256}
}

@inproceedings{zhang2024framework,
  title={A Framework for Training Large Language Models for Code Generation via Proximal Policy Optimization},
  author={Zhang, Chi and Sheng, Guangming and Liu, Siyao and Li, Jiahao and Feng, Ziyuan and Liu, Zherui and Liu, Xin and Jia, Xiaoying and Peng, Yanghua and Lin, Haibin and Wu, Chuan},
  booktitle={In NL2Code Workshop of ACM KDD},
  year={2024}
}
```

