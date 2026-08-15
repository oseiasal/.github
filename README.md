# 🏛️ Repositório Central de CI/CD & DevSecOps (`.github`)

Este repositório centraliza os **Workflows Reutilizáveis (Reusable Workflows)** e os **Starter Templates** para o ecossistema de microsserviços.

---

## 📁 Estrutura do Repositório

```text
.github/
├── .github/
│   └── workflows/
│       ├── reusable-python-quality.yml  # Linter (Ruff/Flake8), Pytest & Coverage
│       ├── reusable-go-quality.yml      # Go Vet, GolangCI-Lint, Go Build & Unit Tests
│       ├── reusable-security.yml        # SAST (Bandit / Gosec) & SCA (Trivy FS com bloqueio em CRITICAL)
│       ├── reusable-build.yml           # Docker Buildx local
│       ├── reusable-docker-push.yml     # Docker Build & Push exclusivo para Amazon ECR
│       ├── quality.yml                  # Pipeline de validação local do repositório
│       └── build.yml                    # Pipeline de build local do repositório
├── workflow-templates/                  # Templates visíveis na aba Actions do GitHub
│   ├── python-ci.properties.json
│   ├── python-ci.yml                    # CI Python: Quality & Security em PRs/develop
│   ├── python-cd.properties.json
│   ├── python-cd.yml                    # CD Python: Quality, Security & Deploy ECR na main
│   ├── go-ci.properties.json
│   ├── go-ci.yml                        # CI Go: Quality & Security em PRs/develop
│   ├── go-cd.properties.json
│   ├── go-cd.yml                        # CD Go: Quality, Security & Deploy ECR na main
│   ├── security-scan.properties.json
│   ├── security-scan.yml                # Template dedicado de segurança
│   ├── ecr-push.properties.json
│   └── ecr-push.yml                     # Template isolado de push ECR
└── README.md
```

---

## 🔀 Estratégia de Branching, CI & CD

| **CI (Integração Contínua)** | `ci.yml` | • PRs para `develop`<br>• PRs para `main` | • **PR para develop:** `quality` (Lint + Testes)<br>• **PR para main:** `quality` + `security` |
| **CD (Entrega Contínua)** | `cd.yml` | • Push na `main` / `master`<br>• `workflow_dispatch` | 1. `quality` (Quality Gate)<br>2. `security` (Security Gate)<br>3. `deploy` (Docker Build & Push ECR) |
| **Security Isolado** | `security.yml` | • Agendamento semanal (Cron) | `security` (SAST & SCA) |

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
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  AWS_SESSION_TOKEN: ${{ secrets.AWS_SESSION_TOKEN }}
```