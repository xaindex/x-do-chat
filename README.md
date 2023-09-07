# X-Do Chat 🦙

X-Do Chat is a new open-source language model from [Xaindex.ai](https://xaindex.ai) that performs as well as closed-source models. 

This model can be used to run the `7B` version of LLaMA and it also works with fine-tuned models.

**Note: X-Do Chat is for research purposes only. It is not intended for commercial use.**

## Prerequisites

- **X-Do Chat weights**. The weights for LLaMA have not yet been released publicly.
- **GPU machine**. You'll need a Linux machine with an NVIDIA GPU attached and the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker) installed. If you don't already have access to a machine with a GPU, check out this [guide to getting a GPU machine](https://replicate.com/docs/guides/get-a-gpu-machine).
- **Docker**. You'll be using the Cog command-line tool to build and push a model. Cog uses Docker to create containers for models.

## Step 1: Set up weights

Replicate currently supports the `7B` model size.

Put your downloaded weights in a folder called `unconverted-weights`. The folder hierarchy should look something like this: 

```
unconverted-weights
├── 7B
│   ├── checklist.chk
│   ├── consolidated.00.pth
│   └── params.json
├── tokenizer.model
└── tokenizer_checklist.chk
```

Convert the weights from a PyTorch checkpoint to a transformers-compatible format using this command:

```
cog run python -m transformers.models.llama.convert_llama_weights_to_hf --input_dir unconverted-weights --model_size 7B --output_dir weights
```

You final directory structure should look like this:

```
weights
├── config.json
├── generation_config.json
├── pytorch_model-00001-of-00002.bin
├── pytorch_model-00002-of-00002.bin
├── pytorch_model.bin.index.json
├── special_tokens_map.json
├── tokenizer.model
└── tokenizer_config.json
```

Once you've done this, you should uncomment `unconverted-weights` in your `.dockerignore` file. This ensures that `unconverted-weights` aren't built into the resulting cog image.

## Step 2: Run the model

You can run the model locally to test it:

```
cog predict -i prompt="Simply put, the theory of relativity states that"
```

X-Do Chat is not fine-tuned to answer questions. You should construct your prompt so that the expected answer is the natural continuation of your prompt. 

## Step 3: Configure the model to run on A100 GPUs

The default GPU type is a T4, but for best performance you'll want to configure your model to run on an A100.

