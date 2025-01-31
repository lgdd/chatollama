# ChatOllama

A Docker Compose to run a local ChatGPT-like application using [Ollama](https://github.com/ollama/ollama), [Open WebUI](https://github.com/open-webui/open-webui) & [Mistral-7B-v0.1](https://huggingface.co/mistralai/Mistral-7B-v0.1).

> *Added [DeepSeek-R1-Distill-Llama-8B](https://ollama.com/library/deepseek-r1) to try out and compare.*

## Usage

Simply run:

```shell
docker compose up
```
The [ollama-models-pull](docker-compose.yml#L30-L36) service will trigger an API call to Ollama to pull the mistral model (~4GB) and shutdown when it's done. You should see the progress in the logs of that service which should end with:

```
{"status":"verifying sha256 digest"}
{"status":"writing manifest"}
{"status":"removing any unused layers"}
{"status":"success"}
```
> It will do the same for the distilled DeepSeek model.

> To verify the list of downloaded models, you can call Ollama on `http://localhost:11434/api/tags`.

The models are stored in a [volume](docker-compose.yml#L7-L8) to avoid downloading them at each restart of Ollama.

Once the model downloaded, you can go to http://localhost. By default, the port number mapped to the host is `80`, but you can change it by editing the [`docker-compose.yml`](docker-compose.yml#L27) file). Next, sign up to create an account (everything is local) and log in. On the top of the page, look for the `Select a model` dropdown menu and select `mistral:latest`. After selecting it, click on the `Set as default` link to avoid having to select it again each time you create a new discussion.

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
If you want to run on CPU, you can comment the lines showed above in the [`docker-compose.yml`](docker-compose.yml#L15-L21) file and then run `docker compose up`.

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
> [!NOTE]
> Make sure that the version of the `nvidia/cuda` image is aligned with the CUDA version installed using the toolkit.