# Jupyter (Kubernetes) — OnDemand app

An OpenOnDemand `batch_connect` app that runs Jupyter on the **Cirrus**
Kubernetes cluster. The user picks CPUs, memory, and wall time on the launch
form; everything else about the pod is fixed by this app.

Based on OSC's [`bc_k8s_jupyter`](https://github.com/OSC/bc_k8s_jupyter).

## Files

| File | Purpose |
|------|---------|
| `manifest.yml` | App name/description in the dashboard. |
| `form.yml` | Launch form; targets `cluster: cirrus`; CPU/memory/wall time. |
| `submit.yml.erb` | The k8s pod spec (container, configmap, mounts, init containers). |
| `view.html.erb` | The "Connect to Jupyter" button. |
| `template/` | Required by batch_connect; unused for the k8s flow. |

## Deploy as a dev app

In the OnDemand dashboard: **Develop → My Sandbox Apps → New App**, then give it a
directory name and this repo's **HTTPS** git URL. OnDemand clones the repo root —
which is why the app files live at the top level here (not in a subdirectory).
