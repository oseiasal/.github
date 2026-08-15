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
│       ├── reusable-build.yml           # Docker Buildx & Trivy Container Scan
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
│   └── docker-build.yml
└── README.md
```

---

## 🛠️ Workflows Reutilizáveis Disponíveis

### 1. 🐍 Python Quality Workflow (`reusable-python-quality.yml`)
* **Jobs:**
  * `lint`: Executa `ruff` e `flake8` para análise estática de código.
  * `unit-test`: Executa `pytest` com relatório de cobertura XML (`pytest-cov`).
* **Chamada:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-python-quality.yml@main
  with:
    python-version: '3.11'
  ```

### 2. 🐹 Go Quality Workflow (`reusable-go-quality.yml`)
* **Jobs:**
  * `lint`: Executa `go vet` e `golangci-lint`.
  * `build-and-test`: Compila o código (`go build`) e executa os testes unitários (`go test -v -race`).
* **Chamada:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-go-quality.yml@main
  with:
    go-version: '1.22'
  ```

### 3. 🛡️ DevSecOps Security Workflow (`reusable-security.yml`)
* **Jobs:**
  * `sast-python`: Análise SAST com `bandit` para código Python.
  * `sast-go`: Análise SAST com `gosec` para código Go.
  * `sca-trivy`: Análise SCA de vulnerabilidades em dependências via `trivy` (modo `fs`) com bloqueio em falhas **CRITICAL/HIGH**.
* **Chamada:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-security.yml@main
  with:
    language: 'python' # ou 'go' ou 'auto'
    trivy-severity: 'CRITICAL,HIGH'
    fail-on-critical: true
  ```

### 4. 🐳 Docker Build Workflow (`reusable-build.yml`)
* **Jobs:**
  * `docker-build`: Build otimizado com Docker Buildx e varredura de imagem com Trivy Container Scanner.
* **Chamada:**
  ```yaml
  uses: oseiasal/.github/.github/workflows/reusable-build.yml@main
  with:
    dockerfile-path: 'Dockerfile.service'
    image-name: 'meu-servico'
    push-image: false
  ```