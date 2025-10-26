# Projeto DevOps - CI/CD com Forgejo e Docker

## 1️⃣ Descrição

Este projeto demonstra a **reprodução de um fluxo DevOps** utilizando ferramentas open source, com foco em:

* **Versionamento de código**: Forgejo (fork do Gitea)
* **Pipeline CI/CD**: workflow local
* **Build e publicação de imagens Docker** de uma aplicação Node.js simples

O objetivo é **demonstrar o ciclo completo de desenvolvimento moderno**, incluindo push para o repositório, build automatizado e execução de container Docker.

---

## 2️⃣ Estrutura do projeto

```
Teste/
├── forgejo/                  # Servidor Git local
│   └── docker-compose.yml
└── app/                      # Aplicação Node.js
    ├── Dockerfile
    ├── package.json
    ├── server.js
    └── .forgejo/             # Pipeline (workflow)
        └── workflows/
            └── build.yml
```

---

## 3️⃣ Tecnologias utilizadas

* **Forgejo**: servidor Git open source auto-hospedado
* **Node.js**: aplicação API REST simples
* **Docker / Docker Compose**: containerização da aplicação e do Forgejo
* **Pipeline local**: workflow `.forgejo/workflows/build.yml` simulando CI/CD

> Obs: Não foi necessário configurar banco de dados separado nem CI completo. A demonstração é prática e auto-suficiente.

---

## 4️⃣ Rodando o projeto manualmente

### 4.1 Subir o Forgejo

Na pasta `forgejo/`:

```bash
docker-compose up -d
```

* Abra no navegador: [http://localhost:3001](http://localhost:3001)
* Complete a configuração inicial (usuário admin, organização, repositório)

---

### 4.2 Subir a aplicação para o Forgejo

Na pasta `app/`:

```bash
git init
git switch -c main
git add .
git commit -m "Primeiro commit da aplicação"
git remote add origin http://localhost:3001/catolica/backend.git
git push -u origin main
```

> Push dispararia o pipeline se houvesse um runner. Como não há runner, vamos rodar manualmente.

---

### 4.3 Executar pipeline manualmente

1. Instalar dependências Node.js:

```bash
npm install
```

2. Testar aplicação localmente:

```bash
node server.js
```

* Em outro terminal:

```bash
curl http://localhost:3000
```

> Deve retornar algo como: `API funcionando! 🚀`

---

### 4.4 Build da imagem Docker

```bash
docker build -t backend:demo .
```

* Confirma que a imagem foi criada:

```bash
docker images
```

---

### 4.5 Rodar a imagem Docker

Mapeando porta do container (3000) para porta livre do host (ex: 3002):

```bash
docker run -p 3002:3000 backend:demo
```

* Abra no navegador: [http://localhost:3002](http://localhost:3002) → `API funcionando! 🚀`

---

### 4.6 Salvar a imagem (opcional)

```bash
docker save backend:demo -o backend.tar
```

* Pode ser copiada ou distribuída para outro host.

---

## 5️⃣ Pipeline de CI/CD (simulado)

Arquivo: `.forgejo/workflows/build.yml`

```yaml
name: Demo CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build:
    runs-on: docker
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Install dependencies
        run: |
          npm install

      - name: Run simple test
        run: |
          node server.js & sleep 2 && curl http://localhost:3000 || echo "Test falhou"

      - name: Build Docker image
        run: |
          docker build -t backend:demo .

      - name: Save Docker image
        run: |
          docker save backend:demo -o backend.tar
```

> Mesmo sem runner, esse workflow mostra **intuitivamente cada passo de um pipeline CI/CD**.

---

## 6️⃣ Demonstração prática

* Mostrar push para Forgejo
* Mostrar logs do pipeline / execução manual
* Mostrar a aplicação rodando via Docker
* Mostrar o arquivo de imagem (`backend.tar`) pronto para deploy

---

## 7️⃣ Observações

* O Forgejo persiste dados em `forgejo/data`
* Pipeline é simples, mas cobre **todos os passos de CI/CD**
* Pode ser expandido para publicar em um registry local ou remoto facilmente

---

## 8️⃣ Autor / Equipe

- GUSTAVO GABRIEL PATERNO
- JOÃO PEDRO SUMI BEBBER
- GUSTAVO FONTANA BERTOLDI
- JEAN CARLO MENEZES CUNHA