# GGUF Parser

Review/Check [GGUF](https://github.com/ggerganov/ggml/blob/master/docs/gguf.md) files,
estimate the memory usage
for [llama.cpp](https://github.com/ggerganov/llama.cpp), [stable-diffusion.cpp](https://github.com/leejet/stable-diffusion.cpp)
and [llama-box](https://github.com/gpustack/llama-box).

See [GGUF Parser detail introduction](https://github.com/gpustack/gguf-parser-go) for more information.

## Usage

```shell
$ gguf-parser --help
NAME:
   gguf-parser - Review/Check GGUF files and estimate the memory usage.

USAGE:
   gguf-parser [GLOBAL OPTIONS]

VERSION:
   ...

GLOBAL OPTIONS:
   --debug        Enable debugging, verbosity. (default: false)
   --help, -h     Print the usage.
   --version, -v  Print the version.

   Estimate

   --device-metric value [ --device-metric value ]                            Specify the device metrics, which is used to estimate the throughput, in form of "FLOPS;Up Bandwidth[;Down Bandwidth]". The FLOPS unit, select from [PFLOPS, TFLOPS, GFLOPS, MFLOPS, KFLOPS]. The Up/Down Bandwidth unit, select from [PiBps, TiBps, GiBps, MiBps, KiBps, PBps, TBps, GBps, MBps, KBps, Pbps, Tbps, Gbps, Mbps, Kbps]. Up Bandwidth usually indicates the bandwidth to transmit the data to calculate, and Down Bandwidth indicates the bandwidth to transmit the calculated result to next layer. For example, "--device-metric 10TFLOPS;400GBps" means the device has 10 TFLOPS and 400 GBps Up/Down bandwidth, "--device-metric 10TFLOPS;400GBps;5000MBps" means the device has 5000MBps Down bandwidth. If the quantity specified by "--device-metric" is less than the number of estimation devices(determined by "--tensor-split" and "--rpc" to infer the device count), then replicate the last "--device-metric" to meet the required number of evaluation devices.
   --flash-attention, --flash-attn, --fa, --diffusion-fa                      Specify enabling Flash Attention, which is used to estimate the usage. Flash Attention can reduce the usage of RAM/VRAM. (default: false)
   --gpu-layers value, --ngl value, --n-gpu-layers value                      Specify how many layers of the main model to offload, which is used to estimate the usage, default is full offloaded. (default: -1)
   --main-gpu value, --mg value                                               Specify the GPU to use for the model (with "--split-mode=none") or for intermediate results and KV (with "--split-mode=row"), which is used to estimate the usage. Since gguf-parser cannot recognize the host GPU devices or RPC servers, "--main-gpu" only works when "--tensor-split" is set. (default: 0)
   --no-flash-attention, --no-flash-attn                                      Specify disabling Flash Attention. (default: false)
   --parallel-size value, --parallel value, --np value, --threads-http value  Specify the number of parallel sequences to decode, which is used to estimate the usage. (default: 1)
   --platform-footprint value                                                 Specify the platform footprint(RAM,VRAM) of running host in MiB, which is used to estimate the NonUMA usage, default is "150,250". Different platform always gets different RAM and VRAM footprints, for example, within CUDA, "cudaMemGetInfo" or "cudaSetDevice" would occupy some RAM and VRAM, see https://stackoverflow.com/questions/64854862/free-memory-occupied-by-cudamemgetinfo. (default: "150,250")
   --rpc value                                                                Specify the RPC servers, which is used to estimate the usage, it is a comma-separated list of host:port. Woks with "--tensor-split".
   --tensor-split value, --ts value                                           Specify the fraction of the model to offload to each device, which is used to estimate the usage, it is a comma-separated list of integer. Since gguf-parser cannot recognize the host GPU devices or RPC servers, must explicitly set "--tensor-split" to indicate how many devices are used. To declare the devices belong to RPC servers, set "--rpc" please.

   Estimate/LLaMACpp

   --batch-size value, -b value                                        Specify the logical batch size, which is used to estimate the usage. (default: 2048)
   --cache-type-k value, --ctk value                                   Specify the type of Key cache, which is used to estimate the usage, select from [f32, f16, bf16, q8_0, q4_0, q4_1, iq4_nl, q5_0, q5_1]. (default: "f16")
   --cache-type-v value, --ctv value                                   Specify the type of Value cache, which is used to estimate the usage, select from [f32, f16, bf16, q8_0, q4_0, q4_1, iq4_nl, q5_0, q5_1]. (default: "f16")
   --ctx-size value, -c value                                          Specify the size of prompt context, which is used to estimate the usage, default is equal to the model's maximum context size. (default: 0)
   --gpu-layers-draft value, --ngld value, --n-gpu-layers-draft value  Specify how many layers of the draft model to offload, which is used to estimate the usage, default is full offloaded. (default: -1)
   --gpu-layers-step value                                             Specify the step of layers to offload, works with "--gpu-layers". (default: 0)
   --in-max-ctx-size                                                   Limit the context size to the maximum context size of the model, if the context size is larger than the maximum context size. (default: false)
   --max-projected-cache value, --visual-max-image-cache value         Specify how many projected embedding to be cached. (default: 0)
   --mmap                                                              Specify enabling Memory-Mapped using, which is used to estimate the usage. Memory-Mapped can avoid loading the entire model weights into RAM. (default: false)
   --no-kv-offload, --nkvo                                             Specify disabling Key-Value offloading, which is used to estimate the usage. Disable Key-Value offloading can reduce the usage of VRAM. (default: false)
   --no-mmap                                                           Specify disabling Memory-Mapped using, which is used to estimate the usage. Memory-Mapped can avoid loading the entire model weights into RAM. (default: false)
   --split-mode value, --sm value                                      Specify how to split the model across multiple devices, which is used to estimate the usage, select from [layer, row, none]. Since gguf-parser always estimates the usage of VRAM, "none" is meaningless here, keep for compatibility. (default: "layer")
   --swa-full                                                          Specify using full-size SWA cache. (default: false)
   --ubatch-size value, --ub value                                     Specify the physical maximum batch size, which is used to estimate the usage. (default: 512)
   --visual-max-image-size value                                       Specify maximum image size when completion with vision model. (default: 0)

   Estimate/StableDiffusionCpp

   --image-autoencoder-tiling, --vae-tiling, --image-vae-tiling                             Specify to enable tiling for the vae model. (default: false)
   --image-batch-count value, --batch-count value, --image-max-batch value                  Specify the batch(generation) count of the image. (default: 1)
   --image-free-compute-memory-immediately                                                  Specify to free the compute memory immediately after the generation, which burst using VRAM. (default: false)
   --image-height value, --height value, --image-max-height value                           Specify the (maximum) height of the image. (default: 1024)
   --image-no-autoencoder-offload, --vae-on-cpu, --image-no-vae-model-offload               Specify to offload the vae model to CPU. (default: false)
   --image-no-autoencoder-tiling, --image-no-vae-tiling                                     Specify to disable tiling for the vae model, it takes precedence over --image-autoencoder-tiling. (default: false)
   --image-no-conditioner-offload, --clip-on-cpu, --image-no-text-encoder-model-offload     Specify to offload the text encoder model to CPU. (default: false)
   --image-no-control-net-offload, --control-net-cpu, --image-no-control-net-model-offload  Specify to offload the control net model to CPU. (default: false)
   --image-width value, --width value, --image-max-width value                              Specify the (maximum) width of the image. (default: 1024)

   Load

   --cache-expiration value      Specify the expiration of cache, works with "--url/--hf-*/--ms-*/--ol-*". (default: 24h0m0s)
   --cache-path value            Cache the read result to the path, works with "--url/--hf-*/--ms-*/--ol-*". (default: "/Users/thxcode/.cache/gguf-parser")
   --skip-cache                  Skip cache, works with "--url/--hf-*/--ms-*/--ol-*", default is caching the read result. (default: false) [$SKIP_CACHE]
   --skip-dns-cache              Skip DNS cache, works with "--url/--hf-*/--ms-*/--ol-*", default is caching the DNS lookup result. (default: false) [$SKIP_DNS_CACHE]
   --skip-proxy                  Skip proxy settings, works with "--url/--hf-*/--ms-*/--ol-*", default is respecting the environment variables "HTTP_PROXY/HTTPS_PROXY/NO_PROXY". (default: false) [$SKIP_PROXY]
   --skip-range-download-detect  Skip range download detect, works with "--url/--hf-*/--ms-*/--ol-*", default is detecting the range download support. (default: false) [$SKIP_RANGE_DOWNLOAD_DETECT]
   --skip-tls-verify             Skip TLS verification, works with "--url/--hf-*/--ms-*/--ol-*", default is verifying the TLS certificate on HTTPs request. (default: false) [$SKIP_TLS_VERIFY]

   Model/Local

   --control-net-path value, --control-net value, --image-control-net-model value                               Path where the GGUF file to load for the Control Net model, optional.
   --control-vector-path value, --control-vector value [ --control-vector-path value, --control-vector value ]  Path where the GGUF file to load for the Control Vector adapter, optional.
   --draft-path value, --model-draft value, --md value                                                          Path where the GGUF file to load for the draft model, optional, e.g. "~/.cache/lm-studio/models/QuantFactory/Qwen2-1.5B-Instruct-GGUF/Qwen2-1.5B-Instruct.Q5_K_M.gguf".
   --lora-path value, --lora value [ --lora-path value, --lora value ]                                          Path where the GGUF file to load for the LoRA adapter, optional.
   --mmproj-path value, --mmproj value                                                                          Path where the GGUF file to load for the multimodal projector, optional.
   --path value, --model value, -m value                                                                        Path where the GGUF file to load for the main model, e.g. "~/.cache/lm-studio/models/QuantFactory/Qwen2-7B-Instruct-GGUF/Qwen2-7B-Instruct.Q5_K_M.gguf".
   --upscale-path value, --upscale-model value, --image-upscale-model value                                     Path where the GGUF file to load for the Upscale model, optional.

   Model/Remote

   --control-net-url value                                    Url where the GGUF file to load for the Control Net model, optional.
   --control-vector-url value [ --control-vector-url value ]  Url where the GGUF file to load for the Control Vector adapter, optional.
   --draft-url value                                          Url where the GGUF file to load for the draft model, optional, e.g. "https://huggingface.co/QuantFactory/Qwen2-1.5B-Instruct-GGUF/resolve/main/Qwen2-1.5B-Instruct.Q5_K_M.gguf". Note that gguf-parser does not need to download the entire GGUF file.
   --lora-url value [ --lora-url value ]                      Url where the GGUF file to load for the LoRA adapter, optional.
   --mmproj-url value                                         Url where the GGUF file to load for the multimodal projector, optional.
   --token value                                              Bearer auth token to load GGUF file, optional, works with "--url/--draft-url". [$TOKEN]
   --upscale-url value                                        Url where the GGUF file to load for the Upscale model, optional.
   --url value, --model-url value, --mu value                 Url where the GGUF file to load for the main model, e.g. "https://huggingface.co/QuantFactory/Qwen2-7B-Instruct-GGUF/resolve/main/Qwen2-7B-Instruct.Q5_K_M.gguf". Note that gguf-parser does not need to download the entire GGUF file.

   Model/Remote/HuggingFace

   --hf-control-net-file value                                        Model file below the "--hf-control-net-repo", optional.
   --hf-control-net-repo value                                        Repository of HuggingFace which the GGUF file store for the Control Net model, optional, works with "--hf-control-net-file".
   --hf-control-vector-file value [ --hf-control-vector-file value ]  Control Vector adapter file below the "--hf-repo".
   --hf-draft-file value                                              Model file below the "--hf-draft-repo", optional, e.g. "Qwen2-1.5B-Instruct.Q5_K_M.gguf".
   --hf-draft-repo value                                              Repository of HuggingFace which the GGUF file store for the draft model, optional, e.g. "QuantFactory/Qwen2-1.5B-Instruct-GGUF", works with "--hf-draft-file".
   --hf-file value, --hff value                                       Model file below the "--hf-repo", e.g. "Qwen2-7B-Instruct.Q5_K_M.gguf".
   --hf-lora-file value [ --hf-lora-file value ]                      LoRA adapter file below the "--hf-repo".
   --hf-mmproj-file value                                             Multimodal projector file below the "--hf-repo".
   --hf-repo value, --hfr value                                       Repository of HuggingFace which the GGUF file store for the main model, e.g. "QuantFactory/Qwen2-7B-Instruct-GGUF", works with "--hf-file".
   --hf-token value, --hft value                                      User access token of HuggingFace, optional, works with "--hf-repo/--hf-file pair" or "--hf-draft-repo/--hf-draft-file" pair. See https://huggingface.co/settings/tokens. [$HF_TOKEN]
   --hf-upscale-file value                                            Model file below the "--hf-upscale-repo", optional.
   --hf-upscale-repo value                                            Repository of HuggingFace which the GGUF file store for the Upscale model, optional, works with "--hf-upscale-file".

   Model/Remote/ModelScope

   --ms-control-net-file value                                        Model file below the "--ms-control-net-repo", optional.
   --ms-control-net-repo value                                        Repository of ModelScope which the GGUF file store for the Control Net model, optional, works with "--ms-control-net-file".
   --ms-control-vector-file value [ --ms-control-vector-file value ]  Control Vector adapter file below the "--ms-repo".
   --ms-draft-file value                                              Model file below the "--ms-draft-repo", optional, e.g. "qwen1_5-1_8b-chat-q5_k_m.gguf".
   --ms-draft-repo value                                              Repository of ModelScope which the GGUF file store for the draft model, optional, e.g. "qwen/Qwen1.5-1.8B-Chat-GGUF", works with "--ms-draft-file".
   --ms-file value                                                    Model file below the "--ms-repo", e.g. "qwen1_5-7b-chat-q5_k_m.gguf".
   --ms-lora-file value [ --ms-lora-file value ]                      LoRA adapter file below the "--ms-repo".
   --ms-mmproj-file value                                             Multimodal projector file below the "--ms-repo".
   --ms-repo value                                                    Repository of ModelScope which the GGUF file store for the main model, e.g. "qwen/Qwen1.5-7B-Chat-GGUF", works with "--ms-file".
   --ms-token value                                                   Git access token of ModelScope, optional, works with "--ms-repo/--ms-file" pair or "--ms-draft-repo/--ms-draft-file" pair. See https://modelscope.cn/my/myaccesstoken. [$HF_TOKEN, $MS_TOKEN]
   --ms-upscale-file value                                            Model file below the "--ms-upscale-repo", optional.
   --ms-upscale-repo value                                            Repository of ModelScope which the GGUF file store for the Upscale model, optional, works with "--ms-upscale-file".

   Model/Remote/Ollama

   --ol-base-url value  Model base URL of Ollama, e.g. https://registry.ollama.ai. (default: "https://registry.ollama.ai")
   --ol-model value     Model name of Ollama, e.g. "gemma2".
   --ol-usage           Specify respecting the extending layers introduced by Ollama, works with "--ol-model", which affects the usage estimation. (default: false)

   Output

   --estimate           Skip all the information except the estimate result. (default: false)
   --in-mib             Display the estimated result in table with MiB. (default: false)
   --in-short           Display the estimated result in table in short form. (default: false)
   --json               Output as JSON. (default: false)
   --json-pretty        Works with "--json", to output pretty format JSON. (default: true)
   --raw                Output the GGUF file information as JSON only, skip anything. (default: false)
   --raw-output value   Works with "--raw", to save the result to the file
   --skip-architecture  Skip to display architecture. (default: false)
   --skip-estimate      Skip to estimate. By default, gguf-parser always estimates the file which types with "model". (default: false)
   --skip-metadata      Skip to display metadata. (default: false)
   --skip-tokenizer     Skip to display tokenizer. By default, gguf-parser always displays the tokenizer of the file which types with "model". (default: false)

```

### Environment Variables Support

- `TOKEN`: The bearer auth token to load GGUF file, works with `--url/--draft-url`.
- `HF_ENDPOINT`: The HuggingFace endpoint, default is `https://huggingface.co`.
- `HF_TOKEN`: The HuggingFace token, see [HuggingFace token](https://huggingface.co/settings/tokens).
- `MS_ENDPOINT`: The ModelScope endpoint, default is `https://modelscope.cn`.
- `MS_TOKEN`: The ModelScope token, see [ModelScope token](https://modelscope.cn/my/myaccesstoken).

## License

MIT
