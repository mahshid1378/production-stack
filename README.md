# vLLM Production Stack: reference stack for production vLLM deployment

## Introduction

**vLLM Production Stack** project provides a reference implementation on how to build an inference stack on top of vLLM, which allows you to:

- 🚀 Scale from single vLLM instance to distributed vLLM deployment without changing any application code
- 💻 Monitor the  through a web dashboard
- 😄 Enjoy the performance benefits brought by request routing and KV cache offloading

## Step-By-Step Tutorials

0. How To [*Install Kubernetes (kubectl, helm, minikube, etc)*]?
1. How to [*Deploy Production Stack on Major Cloud Platforms (AWS, GCP, Lambda Labs, Azure)*]?
2. How To [*Setup a Minimal vLLM Production Stack*]?
3. How To [*Customize vLLM Configs (optional)*]?
4. How to [*Load Your LLM Weights*]?
5. How to [*Launch Different LLMs in vLLM Production Stack*]?
6. How to [*Enable KV Cache Offloading with LMCache*]?

## Architecture

contains the following key parts:

- **Serving engine**: The vLLM engines that run different LLMs
- **Request router**: Directs requests to appropriate backends based on routing keys or session IDs to maximize KV cache reuse.
- **Observability stack**: monitors the metrics of the backends through [Prometheus] + [Grafana]

<p align="center">
  <img src="https://github.com/user-attachments/assets/8f05e7b9-0513-40a9-9ba9-2d3acca77c0c" alt="Architecture of the stack" width="80%"/>
</p>

## Roadmap

We are actively working on this project and will release the following features soon. Please stay tuned!

- **Autoscaling** based on vLLM-specific metrics
- Support for **disaggregated prefill**
- **Router improvements** (e.g., more performant router using non-python languages, KV-cache-aware routing algorithm, better fault tolerance, etc)

## Deploying the stack via Helm

### Prerequisites

- A running Kubernetes (K8s) environment with GPUs
  - Run `cd utils && bash install-minikube-cluster.sh`
  - Or follow our [tutorial](tutorials/00-install-kubernetes-env.md)

### Deployment

vLLM Production Stack can be deployed via helm charts. Clone the repo to local and execute the following commands for a minimal deployment:

```bash
git clone https://github.com/vllm-project/production-stack.git
cd production-stack/
helm repo add vllm https://vllm-project.github.io/production-stack
helm install vllm vllm/vllm-stack -f tutorials/assets/values-01-minimal-example.yaml
```

### Uninstall

```bash
helm uninstall vllm
```

## Grafana Dashboard

### Features

The Grafana dashboard provides the following insights:

1. **Available vLLM Instances**: Displays the number of healthy instances.
2. **Request Latency Distribution**: Visualizes end-to-end request latency.
3. **Time-to-First-Token (TTFT) Distribution**: Monitors response times for token generation.
4. **Number of Running Requests**: Tracks the number of active requests per instance.
5. **Number of Pending Requests**: Tracks requests waiting to be processed.
6. **GPU KV Usage Percent**: Monitors GPU KV cache usage.
7. **GPU KV Cache Hit Rate**: Displays the hit rate for the GPU KV cache.

<p align="center">
  <img src="https://github.com/user-attachments/assets/05766673-c449-4094-bdc8-dea6ac28cb79" alt="Grafana dashboard to monitor the deployment" width="80%"/>
</p>

### Configuration

See the details in [`observability/README.md`](./observability/README.md)

## Router

The router ensures efficient request distribution among backends. It supports:

- Routing to endpoints that run different models
- Exporting observability metrics for each serving engine instance, including QPS, time-to-first-token (TTFT), number of pending/running/finished requests, and uptime
- Automatic service discovery and fault tolerance by Kubernetes API
- Multiple different routing algorithms
  - Round-robin routing
  - Session-ID based routing
  - (WIP) prefix-aware routing

Please refer to the [router documentation](./src/vllm_router/README.md) for more details.

## Contributing

We welcome and value any contributions and collaborations.  Please check out [CONTRIBUTING.md](CONTRIBUTING.md) for how to get involved.

## License

This project is licensed under Apache License 2.0. See the `LICENSE` file for details.

---

For any issues or questions, feel free to open an issue or contact us ([@ApostaC], [@YuhanLiu11], [@Shaoting-Feng]).
