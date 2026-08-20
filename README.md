# 🏛️ Repositório Central de CI/CD & DevSecOps (`.github`)

Este repositório centraliza os **Workflows Reutilizáveis (Reusable Workflows)** e os **Starter Templates** para o ecossistema de microsserviços e infraestrutura em nuvem.

---

## 📁 Estrutura do Repositório

```text
.github/
├── .github/
│   └── workflows/
│       ├── reusable-python-quality.yml    # Linter (Ruff/Flake8), Pytest & Coverage
│       ├── reusable-go-quality.yml        # Go Vet, GolangCI-Lint, Go Build & Unit Tests
│       ├── reusable-security.yml          # SAST (Bandit / Gosec) & SCA (Trivy FS)
│       ├── reusable-build.yml             # Docker Buildx local
│       ├── reusable-docker-push.yml       # Docker Build & Push exclusivo para Amazon ECR via OIDC
│       ├── reusable-terragrunt.yml        # IaC: Lint, Trivy Scan, Plan, Apply e Destroy via OIDC
│       ├── reusable-kustomize-deploy.yml  # K8s: Kubeconform, Deploy Kustomize no EKS & Rollout Check
│       ├── quality.yml                    # Pipeline de validação local do repositório
│       └── build.yml                      # Pipeline de build local do repositório
├── workflow-templates/                    # Templates visíveis na aba Actions do GitHub
│   ├── python-ci.properties.json / .yml
│   ├── python-cd.properties.json / .yml
│   ├── go-ci.properties.json / .yml
│   ├── go-cd.properties.json / .yml
│   ├── security-scan.properties.json / .yml
│   ├── ecr-push.properties.json / .yml
│   ├── terragrunt.properties.json / .yml   # Starter Template para IaC Terragrunt
│   └── k8s-deploy.properties.json / .yml   # Starter Template para Deploy K8s
└── README.md
```

---

## 🔀 Estratégia de Branching, CI & CD

| Camada / Tipo | Workflow | Gatilhos | Etapas Executadas |
| :--- | :--- | :--- | :--- |
| **CI (Microsserviços)** | `ci.yml` | • PRs para `develop` / `main` | Lint + Testes Unitários + DevSecOps Security Scan |
| **CD (Microsserviços)** | `cd.yml` | • Push na `main`<br>• `workflow_dispatch` | Quality Gate ➔ Security Gate ➔ Docker Build & Push ECR (OIDC) |
| **IaC (Terraform / Terragrunt)** | `terraform.yml` | • PRs (Plan)<br>• Manual (Apply/Destroy) | Lint & Trivy IaC Scan ➔ Terragrunt Plan / Apply / Destroy |
| **Deploy K8s (Kustomize)** | `k8s-deploy.yml` | • Push `k8s/**`<br>• `workflow_dispatch` | Kubeconform Lint ➔ AWS EKS Connect (OIDC) ➔ Apply Kustomize Overlay ➔ Rollout Verification |

---

## 🛠️ Workflows Reutilizáveis Disponíveis

### 1. 🐍 Python Quality (`reusable-python-quality.yml`)
```yaml
uses: oseiasal/.github/.github/workflows/reusable-python-quality.yml@main
with:
  python-version: '3.11'
```

### 2. 🐹 Go Quality (`reusable-go-quality.yml`)
```yaml
uses: oseiasal/.github/.github/workflows/reusable-go-quality.yml@main
with:
  go-version: '1.22'
```

### 3. 🛡️ DevSecOps Security (`reusable-security.yml`)
```yaml
uses: oseiasal/.github/.github/workflows/reusable-security.yml@main
with:
  language: 'auto' # 'python', 'go' ou 'auto'
  trivy-severity: 'CRITICAL,HIGH'
  fail-on-critical: true
```

### 4. 🚀 Docker Build & ECR Push (`reusable-docker-push.yml`)
```yaml
permissions:
  id-token: write
  contents: read

uses: oseiasal/.github/.github/workflows/reusable-docker-push.yml@main
with:
  image-name: 'meu-servico'
  dockerfile-path: 'Dockerfile'
  aws-region: 'us-east-1'
  push-image: true
  tag-latest: true
secrets:
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
  AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
```

### 5. 🏗️ IaC Terragrunt & Terraform (`reusable-terragrunt.yml`)
```yaml
permissions:
  id-token: write
  contents: read
  pull-requests: write

uses: oseiasal/.github/.github/workflows/reusable-terragrunt.yml@main
with:
  working_directory: 'terraform/environments/dev'
  terragrunt_action: 'plan' # 'validate', 'plan', 'apply', 'destroy'
  aws_region: 'us-east-1'
  auto_approve: false
secrets:
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
  AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
```

### 6. ☸️ Kubernetes Kustomize Deploy (`reusable-kustomize-deploy.yml`)
```yaml
permissions:
  id-token: write
  contents: read

uses: oseiasal/.github/.github/workflows/reusable-kustomize-deploy.yml@main
with:
  k8s_overlay: 'k8s/overlays/prod'
  cluster_name: 'toogle-cluster'
  aws_region: 'us-east-1'
  namespace: 'toogle-master'
  dry_run: false
  run_smoke_tests: true
secrets:
  AWS_ACCOUNT_ID: ${{ secrets.AWS_ACCOUNT_ID }}
  AWS_ROLE_TO_ASSUME: ${{ secrets.AWS_ROLE_TO_ASSUME }}
```