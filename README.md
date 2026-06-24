# Jupyter (Kubernetes) — OnDemand app

An OpenOnDemand `batch_connect` app that launches Jupyter in the **Cirrus**
Kubernetes cluster, using a fixed image (`quay.io/jupyter/scipy-notebook`).
Based on OSC's [`bc_k8s_jupyter`](https://github.com/OSC/bc_k8s_jupyter), adapted
to public images.

This is **Phase A** of a three-part effort:

- **Phase A (this repo):** launch a fixed Jupyter image.
- **Phase B:** user supplies any image — [`ondemand-jupyter-k8s-image`](https://github.com/NicholasCote/ondemand-jupyter-k8s-image).
- **Phase C:** Binder-style build from a git URL — [`ondemand-jupyter-binder`](https://github.com/NicholasCote/ondemand-jupyter-binder).

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

## How it works

No shell job script — the k8s adapter creates a pod from `submit.yml.erb`. Three
init containers (from `ood-k8s-utils`) generate a one-time password, inject it into
the notebook config, and set the OnDemand reverse-proxy `base_url` before Jupyter
starts. OnDemand reads back host/port/password to render the Connect button.

## Verify on first launch

- [ ] **Helper image pullable** from Cirrus: `docker.io/ohiosupercomputer/ood-k8s-utils:latest`
      (mirror to Harbor + pin a tag for production).
- [ ] **`$HOME` host-mount** works only if home is on the Cirrus nodes (NFS); else
      switch the mount `type:` or remove it.
- [ ] **RBAC**: adapter can create pods, configmaps, secrets, services in the user namespace.
- [ ] **Jupyter config class**: newer images use `c.ServerApp.*`/`IdentityProvider`
      rather than `c.NotebookApp.*`; switch if password login fails.

Debug: `kubectl describe pod <pod> -n <user-ns>`.
