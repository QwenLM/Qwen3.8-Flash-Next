# Qwen3.8-Flash-Next

<div style="text-align: center">
  <p align="center">
    <a href="https://chat.qwen.ai/">💜 Qwen Studio</a> |
    <a href="https://huggingface.co/Qwen">🤗 Hugging Face</a> | 
    <a href="https://modelscope.cn/organization/qwen">🤖 ModelScope</a> | 
    <a href="https://github.com/QwenLM/Qwen/blob/main/assets/wechat.png">💬 WeChat (微信)</a> |
    <a href="https://discord.gg/CV4E9rpNSD">🫨 Discord</a>   
  </p>
</div>

Welcome to the GitHub repository of the Qwen3.8-Flash-Next.

## Introduction

### Qwen3.8-Flash-Next

In this release we are opening the weights of **Qwen3.8-Flash-Next**, a multimodal MoE model that also serves as an early preview of the architecture used in **Qwen4**. 
It plays the same role that [Qwen3-Next](https://qwen.ai/blog?id=qwen3-next) played for Qwen3.5: 
the hybrid **Gated DeltaNet + Gated Attention** design introduced at that time has since been used across the Qwen3.5, Qwen3.6, Qwen3.7 and Qwen3.8 series.
We are again releasing the architectural changes early, so that the community can examine them before the full Qwen4 model family is built on top of them.

![Qwen3.8-Flash-Next Architecture](https://qianwen-res.oss-accelerate.aliyuncs.com/Qwen3.8-Flash-Next/architecture.png)

Qwen3.8-Flash-Next upgrades the model systematically along four aspects — **attention, residual, embedding and optimization** — improving model capability while further optimizing computational efficiency, model capacity and training stability:

- **Attention** A **GDN + QSA hybrid architecture**. Gated DeltaNet (GDN) compresses the history efficiently; **Qwen Sparse Attention (QSA)** uses a compressed lightweight indexer to select the important context at micro-block granularity, substantially reducing the cost of attention on long sequences.
- **Residual** **Gated Residual (GR)** widens the residual stream into 4 branches and controls reads and writes with a dynamic gate, strengthening cross-layer information flow and training stability.
- **Embedding** **N-gram Embedding** looks up a table using the local context to scale model capacity with very little extra computation; the embedding table can be offloaded to host memory and overlapped with model computation through asynchronous prefetching.
- **Optimization** The **Muon optimizer** is used, refined around orthogonalization accuracy, the division of labour between Muon and AdamW, and the splitting of fused parameters, with the scaling law refitted for the new architecture.

Qwen3.8-Flash-Next features a **125B**-parameter main model, supplemented by an additional **51B** N-gram embeddings, with **6B** parameters activated per token.
Compared with Qwen3.7-Plus, Qwen3.8-Flash-Next substantially reduces both training and inference cost — training takes only about 1/9 as much, yet it delivers superior capabilities in coding and office tasks.

## News

- 2026-08-26: We release Qwen3.8-Flash-Next. Read more on our [blog](https://qwen.ai/blog?id=qwen3.8-flash-next).

## Models

The official model weights are released on:
- [🤗Hugging Face Hub](https://huggingface.co/Qwen): Most LLM frameworks and applications support downloading model files from Hugging Face Hub automatically by specifying the model ID, e.g., `Qwen/Qwen3.8-Flash-Next`.
  You can also download model files manually using `huggingface download` or `git clone`.
  Please follow the instructions on the model page.
- [🤖ModelScope](https://www.modelscope.cn/organization/Qwen): For users unable to access Hugging Face Hub, we strongly recommend using ModelScope.
  For supported frameworks, you can download from ModelScope by setting environment variables, such as `SGLANG_USE_MODELSCOPE=true` or `VLLM_USE_MODELSCOPE=true`.
  You can also download model files manually using `modelscope download` or `git clone`.
  Please follow the instructions on the model page.

## Benchmarks

Evaluation results are reported in the [Qwen3.8-Flash-Next blog](https://qwen.ai/blog?id=qwen3.8-flash-next).

## Quickstart

### Official

You can try Qwen3.8-Flash-Next on our official sites and enjoy the native experience with extra features.

#### QwenWork

Qwen3.8-Flash-Next now powers the newly-launched "Standard" mode on [QwenWork](https://qwenwork.ai). QwenWork is a one-stop AI working platform launched by Alibaba. Follow [its documentation](https://docs.qwenwork.ai/product-introduction) to get started!

#### Qwen API

[QwenCloud](https://www.qwencloud.com) provides first-class support for Qwen3.8-Flash-Next, which is compatible with various API specifications, including OpenAI and Anthropic, making it simple for you to try Qwen3.8-Flash-Next in your own applications.

#### Qwen Code

[Qwen Code](https://qwen.ai/qwencode) is an open-source AI agent for the terminal, optimized for Qwen models. It helps you understand large codebases, automate tedious work, and ship faster. Follow [its documentation](https://qwenlm.github.io/qwen-code-docs/) to get started!

### Local Use

#### Hugging Face Transformers

[`transformers`](https://huggingface.co/docs/transformers) acts as the model-definition framework in the current open-weight LLM landscape.
It also includes functionalities for LLM inference and training. The addition of serving capabilities in `transformers` makes it much easier to integrate new models in your development.

To launch a server, simply use the `transformers serve` command:
```shell
transformers serve Qwen/Qwen3.8-Flash-Next --port 8000 --continuous-batching
```
An OpenAI-compatible API will be available at `http://localhost:8000/v1`.
See [the Serve CLI guide](https://huggingface.co/docs/transformers/serve-cli/serving) for more information.

#### llama.cpp

[`llama.cpp`](https://github.com/ggml-org/llama.cpp) enables LLM inference with minimal setup and state-of-the-art performance on a wide range of hardware.
llama.cpp supports the Qwen3.8-Flash-Next (text & vision).
Look for models ending with GGUF on Hugging Face Hub.

#### MLX (Apple Silicon)

If you are running on Apple Silicon, [`mlx-vlm`](https://github.com/Blaizzy/mlx-vlm) supports Qwen3.8-Flash-Next (vision + text). 
Original checkpoints are compatible and can be converted. 
You can also search for models ending with MLX on the Hugging Face Hub for ready-to-use quantized versions.

#### Unsloth

[Unsloth](https://unsloth.ai) contains a local UI to run and train LLMs and diffusion models, including Qwen3.8-Flash-Next and more.
See [the Qwen3.8-Flash-Next guide](https://unsloth.ai/docs/models/qwen3.8-next) for running Qwen3.8-Flash-Next quants with Unsloth.

### Deployment

Qwen3.8-Flash-Next is supported by multiple inference frameworks.
Here we demonstrate the usage of SGLang, vLLM, and TokenSpeed.

#### SGLang

[SGLang](https://github.com/sgl-project/sglang) is a fast serving framework for large language models and vision language models.
SGLang can be used to launch a server with an OpenAI-compatible API service.

```shell
sglang serve --model-path Qwen/Qwen3.8-Flash-Next --port 8000 --tp-size 4 --context-length 262144 --reasoning-parser qwen3 --tool-call-parser qwen3_coder
```

An OpenAI-compatible API will be available at `http://localhost:8000/v1`.

Also see SGLang Cookbook on [serving Qwen3.8-Flash-Next](https://docs.sglang.io/cookbook/autoregressive/Qwen/Qwen3.8-Flash-Next).

#### vLLM

[vLLM](https://github.com/vllm-project/vllm) is a high-throughput and memory-efficient inference and serving engine for LLMs.
vLLM can be used to launch a server with an OpenAI-compatible API service.

```shell
vllm serve Qwen/Qwen3.8-Flash-Next --port 8000 --tensor-parallel-size 4 --max-model-len 262144 --reasoning-parser qwen3 --enable-auto-tool-choice --tool-call-parser qwen3_coder
```

An OpenAI-compatible API will be available at `http://localhost:8000/v1`.

Also see vLLM Recipes on [serving Qwen3.8-Flash-Next](https://recipes.vllm.ai/Qwen/Qwen3.8-Flash-Next).

#### TokenSpeed

[TokenSpeed](https://github.com/lightseekorg/tokenspeed) is a speed-of-light LLM inference engine.
TokenSpeed can be used to launch a server with an OpenAI-compatible API service.

```shell
tokenspeed serve Qwen/Qwen3.8-Flash-Next --port 8000 --tensor-parallel-size 4 --max-model-len 262144 --reasoning-parser qwen3 --enable-auto-tool-choice --tool-call-parser qwen3_coder
```

An OpenAI-compatible API will be available at `http://localhost:8000/v1`.

Also see TokenSpeed Recipes on [serving Qwen3.8-Flash-Next](https://lightseek.org/tokenspeed/recipes/models#qwen3-8-flash-next).

### Finetuning

We advise you to use training frameworks, including [Unsloth](https://github.com/unslothai/unsloth), [Swift](https://github.com/modelscope/swift), [Llama-Factory](https://github.com/hiyouga/LLaMA-Factory), to finetune your models with SFT, DPO, GRPO, etc.

## License Agreement

Please find the license file released with the model weights on Hugging Face Hub or ModelScope.

## Citation

If you find our work helpful, feel free to give us a cite.

```bibtex
@techreport{qwen2026design,
    title       = {On the Design of {Qwen3.8-Next} Architecture: Evaluation, Efficiency, and Training Stability},
    author      = {{Qwen Team}},
    institution = {Alibaba Group},
    month       = {August},
    year        = {2026}
}

@misc{qwen3.8flashnext,
    title  = {{Qwen3.8-Flash-Next}: A New Architecture, Towards Ultimate Cost-Efficiency},
    author = {{Qwen Team}},
    month  = {August},
    year   = {2026},
    url    = {https://qwen.ai/blog?id=qwen3.8-flash-next}
}
```

## Contact Us

If you are interested in leaving a message to either our research team or product team, join our [Discord](https://discord.gg/CV4E9rpNSD) or [WeChat groups](https://github.com/QwenLM/Qwen/blob/main/assets/wechat.png)!
