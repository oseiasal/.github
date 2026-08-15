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
│   ├── python-ci.yml
│   ├── go-ci.properties.json
│   ├── go-ci.yml
│   ├── security-scan.properties.json
│   ├── security-scan.yml
│   ├── docker-build.properties.json
│   ├── docker-build.yml
│   ├── ecr-push.properties.json
│   └── ecr-push.yml
└── README.md
```

---

## 🛠️ Workflows Reutilizáveis Disponíveis

### 1. 🐍 Python Quality Workflow (`reusable-python-quality.yml`)
* **Jobs:** `lint` (Ruff/Flake8) e `unit-test` (Pytest com cobertura).
* **Uso:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-python-quality.yml@main
  with:
    python-version: '3.11'
  ```

### 2. 🐹 Go Quality Workflow (`reusable-go-quality.yml`)
* **Jobs:** `lint` (Go Vet / GolangCI-Lint) e `build-and-test` (Go Build / Go Test).
* **Uso:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-go-quality.yml@main
  with:
    go-version: '1.22'
  ```

### 3. 🛡️ DevSecOps Security Workflow (`reusable-security.yml`)
* **Jobs:** `sast-python` (Bandit), `sast-go` (Gosec) e `sca-trivy` (Trivy FS com bloqueio estrito em falhas críticas).
* **Uso:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-security.yml@main
  with:
    language: 'auto' # 'python', 'go' ou 'auto'
    trivy-severity: 'CRITICAL,HIGH'
    fail-on-critical: true
  ```

### 4. 🐳 Docker Build Local (`reusable-build.yml`)
* **Jobs:** `docker-build` (Build local via Buildx).
* **Uso:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-build.yml@main
  with:
    dockerfile-path: 'Dockerfile'
    image-name: 'meu-servico'
    push-image: false
  ```

### 5. 🚀 Docker Build & ECR Push (`reusable-docker-push.yml`)
* **Jobs:** `ecr-push` (Build da imagem e push autenticado no Amazon ECR via OIDC Role ou Access Keys/Session Token).
* **Uso:**
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