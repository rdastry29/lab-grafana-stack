# 🧪 Lab Grafana Stack — APM Local com Kubernetes

Laboratório local de observabilidade completa usando a **Grafana Stack (LGTM)** como alternativa ao Dynatrace.

## Stack

| Componente | Função | Equivalente Dynatrace |
|---|---|---|
| **Grafana Alloy** | Agente/Collector de telemetria | OneAgent |
| **Mimir** | Armazenamento de métricas (Prometheus-compatível) | Métricas do Dynatrace |
| **Tempo** | Armazenamento e consulta de traces distribuídos | PurePath / Distributed Tracing |
| **Loki** | Armazenamento e consulta de logs | Log Viewer do Dynatrace |
| **Grafana** | Dashboards, exploração e alertas | Dynatrace UI |
| **Podinfo** | App de testes leve para geração de telemetria | Aplicações monitoradas |

## Pré-requisitos

- macOS com Apple Silicon (M1/M2/M3)
- [Colima](https://github.com/abiosoft/colima) `>= 0.6`
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
- [helm](https://helm.sh/docs/intro/install/) `>= 3.12`
- [helmfile](https://helmfile.readthedocs.io/) `>= 0.150`

## Início Rápido

### 1. Subir o cluster

```bash
colima start --profile grafana-lab \
  --cpu 6 \
  --memory 10 \
  --disk 60 \
  --kubernetes \
  --kubernetes-version v1.33.2+k3s1 \
  --k3s-arg="--disable=traefik" \
  --k3s-arg="--disable=servicelb" \
  --network-address
```

### 2. Adicionar os repos Helm

```bash
helmfile repos
```

### 3. Criar os namespaces

```bash
kubectl apply -f namespaces.yaml
```

### 4. Deploy da Grafana Stack

```bash
helmfile apply -l component=grafana-stack
```

### 5. Deploy do Podinfo (App de testes)

```bash
helmfile apply -l component=podinfo
```

### 6. Acessar o Grafana

```bash
kubectl port-forward -n grafana-stack svc/grafana 3000:80
# Abrir: http://localhost:3000 (admin/admin)
```

## Estrutura do Repositório

```
lab-grafana-stack/
├── helmfile.yaml           # Orquestrador principal (todos os releases)
├── namespaces.yaml         # Definição dos namespaces K8s
├── values/
│   ├── grafana.yaml        # Config do Grafana (datasources, dashboards)
│   ├── mimir.yaml          # Config do Mimir (métricas)
│   ├── tempo.yaml          # Config do Tempo (traces)
│   ├── loki.yaml           # Config do Loki (logs)
│   ├── alloy.yaml          # Config do Alloy (collector)
│   └── podinfo.yaml        # Config do Podinfo (app de telemetria)
├── dashboards/             # Dashboards Grafana em JSON
└── docs/                   # Notas, aprendizados e arquitetura
```

## Parar / Retomar o Lab

```bash
# Parar (preserva o estado)
colima stop --profile grafana-lab

# Retomar
colima start --profile grafana-lab
```

## Recursos de Referência

- [Grafana Helm Charts](https://github.com/grafana/helm-charts)
- [Podinfo Github](https://github.com/stefanprodan/podinfo)
- [Grafana Alloy Docs](https://grafana.com/docs/alloy/)
- [Mimir Docs](https://grafana.com/docs/mimir/)
- [Tempo Docs](https://grafana.com/docs/tempo/)
- [Loki Docs](https://grafana.com/docs/loki/)
