# ChatOllama

A Docker Compose to run a local ChatGPT-like application using [Ollama](https://github.com/ollama/ollama), [Ollama Web UI](https://github.com/ollama-webui/ollama-webui) & [Mistral-7B-v0.1](https://huggingface.co/mistralai/Mistral-7B-v0.1).

## Usage

Simply run:

```shell
docker compose up
```
The [ollama-models-pull](docker-compose.yml#L30) service will trigger an API call to Ollama to pull the mistral model (~4GB) and shutdown when it's done. You should see the progress in the logs of that service which should end with:

```
{"status":"verifying sha256 digest"}
{"status":"writing manifest"}
{"status":"removing any unused layers"}
{"status":"success"}
```

The models are stored in a [volume](docker-compose.yml#L8) to avoid downloading them at each restart of Ollama.

## Requirements

By default, Ollama is set to use 1 NVIDIA GPU:

```yaml
deploy:
  resources:
    reservations:
      devices:
        - driver: nvidia
          count: 1
          capabilities: [ gpu ]
```
If you want to run on CPU, you can comment the lines showed above in the [`docker-compose.yml`](docker-compose.yml#L15) file and then run `docker compose up`.

If you want to run on an NVIDIA GPU, make sure that your Docker daemon configuration file contains the following:

```json
"runtimes": {
  "nvidia": {
    "path": "nvidia-container-runtime",
    "runtimeArgs": []
  }
}
```
> You can also add `"default-runtime": "nvidia"`.

Also, you should have installed the [NVIDIA CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit). To verify that Docker can access your GPU, you can run:

```shell
docker run --runtime nvidia --rm nvidia/cuda:12.3.1-base-ubuntu20.04 nvidia-smi
```
> Make sure that the version of the `nvidia/cuda` image is aligned with the CUDA version installed using the toolkit.