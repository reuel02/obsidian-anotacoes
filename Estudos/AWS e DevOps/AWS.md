# 🚀 Guia de Deploy: Aplicação Python na AWS

Este documento detalha o fluxo de trabalho para levar uma aplicação Flask do ambiente local para a nuvem, garantindo qualidade, segurança e escalabilidade.

---

## 📦 Fase 1: Docker (Ambiente Local)

O objetivo é garantir a portabilidade da aplicação, eliminando o problema de "na minha máquina funciona".

- **Dockerfile:** Utiliza a imagem `python:3.11-slim` para otimizar o tamanho e o tempo de build.
- **.dockerignore:** Essencial para evitar o envio de arquivos desnecessários (`.venv`, `__pycache__`, etc.) para o contêiner.
- **Docker Compose:** Orquestra o `PostgreSQL` e a `API`, gerenciando redes virtuais e persistência de dados via volumes.

|Ação|Comando|
|:--|:--|
|Iniciar ambiente|`docker compose up --build`|
|Parar ambiente|`docker compose down`|

---

## 🤖 Fase 2: CI (Integração Contínua)

Automatização via **GitHub Actions** disparada a cada _push_ na branch `main`.

1. **Linting:** Validação de estilo com `black --check .`.
2. **Segurança (SAST):** Varredura de vulnerabilidades com `bandit -r .`.
3. **Testes:** Execução da suíte de testes com `pytest`.
4. **Build:** Verificação da integridade do `Dockerfile` em ambiente limpo.

---

## ☁️ Fase 3: Provisionamento na AWS

Configuração da infraestrutura básica na nuvem.

- **FinOps:** Configuração de _Budget_ de "Zero Spend" ($0.01) para evitar surpresas na fatura.
- **EC2 (Instância):** Ubuntu 24.04 LTS, tipo `t2.micro` (Free Tier).
- **Segurança (Firewall):**
    - **Porta 22:** SSH (Acesso administrativo).
    - **Porta 80:** HTTP (Tráfego público).
    - **Porta 443:** HTTPS (Tráfego seguro).

> **Acesso Remoto:** `ssh -i nome-da-chave.pem ubuntu@IP_PUBLICO_AWS`

---

## 🛠️ Fase 4: Configuração e Deploy

Preparação do servidor e execução da aplicação.

### 1. Preparação do Ambiente

```bash
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install git curl -y
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker
```

### 2. Deploy

1. Clone o repositório: `git clone URL_DO_REPOSITORIO`
2. Configure as variáveis de ambiente: `nano .env`
3. Suba a aplicação: `docker compose up -d --build`

_Dica: Use `docker ps` para listar contêineres e `docker logs <nome>` para depuração._

---

## 🚪 Fase 5: Nginx (Proxy Reverso)

Configuração para expor a aplicação na porta 80 de forma profissional.

**Configuração do Site (`/etc/nginx/sites-available/barbearia`):**

```nginx
server {
    listen 80;
    server_name _; 

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

**Ativação:**

```bash
sudo ln -s /etc/nginx/sites-available/barbearia /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```