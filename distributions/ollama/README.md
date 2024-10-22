# Ollama Distribution

The `llamastack/distribution-ollama` distribution consists of the following provider configurations.

| **API**         	| **Inference**  	| **Agents**     	| **Memory**                       	| **Safety**     	| **Telemetry**  	|
|-----------------	|----------------	|----------------	|----------------------------------	|----------------	|----------------	|
| **Provider(s)** 	| remote::ollama 	| meta-reference 	| remote::pgvector, remote::chroma 	| remote::ollama 	| meta-reference 	|


### Start a Distribution (Single Node GPU)

> [!NOTE]
> This assumes you have access to GPU to start a Ollama server with access to your GPU.

```
$ cd llama-stack/distribution/ollama/gpu
$ ls
compose.yaml  run.yaml
$ docker compose up
```

You will see outputs similar to following ---
```
[ollama]               | [GIN] 2024/10/18 - 21:19:41 | 200 |     226.841µs |             ::1 | GET      "/api/ps"
[ollama]               | [GIN] 2024/10/18 - 21:19:42 | 200 |      60.908µs |             ::1 | GET      "/api/ps"
INFO:     Started server process [1]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://[::]:5000 (Press CTRL+C to quit)
[llamastack] | Resolved 12 providers
[llamastack] |  inner-inference => ollama0
[llamastack] |  models => __routing_table__
[llamastack] |  inference => __autorouted__
```

To kill the server
```
docker compose down
```

### Start the Distribution (Single Node CPU)

> [!NOTE]
> This will start an ollama server with CPU only, please see [Ollama Documentations](https://github.com/ollama/ollama) for serving models on CPU only.

```
$ cd llama-stack/distribution/ollama/cpu
$ ls
compose.yaml  run.yaml
$ docker compose up
```

### (Alternative) ollama run + llama stack Run

If you wish to separately spin up a Ollama server, and connect with Llama Stack, you may use the following commands.

#### Start Ollama server.
- Please check the [Ollama Documentations](https://github.com/ollama/ollama) for more details.

**Via Docker**
```
docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

**Via CLI**
```
ollama run <model_id>
```

#### Start Llama Stack server pointing to Ollama server

**Via Docker**
```
docker run --network host -it -p 5000:5000 -v ~/.llama:/root/.llama -v ./gpu/run.yaml:/root/llamastack-run-ollama.yaml --gpus=all distribution-ollama --yaml_config /root/llamastack-run-ollama.yaml
```

Make sure in you `run.yaml` file, you inference provider is pointing to the correct Ollama endpoint. E.g.
```
inference:
  - provider_id: ollama0
    provider_type: remote::ollama
    config:
      url: http://127.0.0.1:14343
```

**Via Conda**

```
llama stack build --config ./build.yaml
llama stack run ./gpu/run.yaml
```
