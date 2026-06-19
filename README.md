# PLG Logging Stack

A minimal, fully open-source **PLG** logging stack — **P**romtail's successor (Grafana Alloy) -> **L**oki -> **G**rafana - deployed two ways from one repo:

- **Kubernetes** (Minikube + Helm)
- **Podman Compose**

Both paths run the same Loki + Grafana, auto-provision the datasource and a logs dashboard, and use [`flog`](https://github.com/mingrammer/flog) as a fake log generator so the dashboards are never empty. Everything runs locally on a Mac.

> **Why Alloy and not Promtail?** Promtail reached end-of-life on **2 March 2026**; Grafana Alloy is its supported replacement. The acronym "PLG" is kept for familiarity, but the **L** and **G** are the real stars - Alloy is just the shipper.

## Architecture

```
            ┌─────────┐     ┌────────┐     ┌──────┐     ┌─────────┐
  Podman:   │  flog   │────▶│ shared │────▶│ Alloy│────▶│  Loki   │◀──┐
            │(stdout) │     │ logfile│     │(tail)│     │(storage)│   │
            └─────────┘     └────────┘     └──────┘     └─────────┘   │
                                                                      │ query
            ┌─────────┐                    ┌──────┐     ┌─────────┐   │
  K8s:      │  flog   │───pod stdout──────▶│ Alloy│────▶│  Loki   │   │
            │  pod    │                    │(DS)  │     │         │   │
            └─────────┘                    └──────┘     └─────────┘   │
                                                          ┌─────────┐ │
                                                          │ Grafana │─┘
                                                          └─────────┘
```