# 🚀 Homelab & Infrastructure as Code (IaC)

Bem-vindo ao meu repositório central de Infraestrutura. Este projeto é um laboratório vivo onde planejo, testo e implemento soluções de nível empresarial com foco em **Cultura DevOps, Segurança Zero Trust e Alta Disponibilidade**.

Todo o ecossistema é orquestrado via Docker Compose, utilizando variáveis de ambiente (`.env`) e redes isoladas para garantir que a infraestrutura seja 100% reprodutível (IaC) em qualquer servidor Linux.

## 🛡️ Arquitetura e Filosofia Zero Trust

A base deste laboratório foi desenhada assumindo que a rede física é hostil. Nenhuma porta de aplicação é exposta diretamente ao mundo externo (`0.0.0.0`). A arquitetura é dividida em planos isolados:

* **Management Plane (Acesso Administrativo):** Todo o tráfego de gestão e acesso a painéis internos ocorre exclusivamente via **Tailscale (VPN Mesh)**.
* **Micro-segmentação (Padrão Sidecar):** Serviços críticos não compartilham o mesmo proxy de borda. Utilizo contêineres Sidecar do Tailscale acoplados aos serviços para garantir que cada aplicação possua seu próprio IP e túnel criptografado na Tailnet (ex: `https://zabbix-monitor`).
* **Data Plane (Tráfego Público):** Serviços que precisam de exposição à internet externa são roteados através de **Cloudflare Tunnels**, garantindo proxy reverso, SSL automático e mitigação de DDoS sem a necessidade de abrir portas no roteador ou firewall da rede local.

## 🛠️ Stack Tecnológica

* **Containerização:** Docker & Docker Compose
* **Redes & Proxy:** Tailscale, Cloudflare Zero Trust
* **Observabilidade:** Zabbix (Monitoramento Ativo/Passivo) e PostgreSQL
* **Automação & Integração:** n8n (Orquestração de fluxos de negócios e webhooks)
* **Sistema Operacional Host:** Ubuntu Server

## 📂 Estrutura do Monorepo

Para demonstrar flexibilidade arquitetural, os serviços neste repositório frequentemente possuem múltiplas formas de implantação (ex: com portas expostas para testes locais ou blindados via VPN Sidecar). 

* 📁 `/n8n` - Plataforma de automação baseada em nós.
* 📁 `/monitoramento` - Stack de observabilidade (Zabbix Server, Web, Banco de Dados).
* 📁 `/cloudflare` - Roteamento seguro de borda.

### ⚙️ Como utilizar

Cada diretório possui seu próprio `README.md` detalhado com os comandos específicos de orquestração. O padrão geral de implantação exige a criação prévia de uma rede externa global e a configuração do arquivo `.env` para injeção segura de credenciais:

```bash
# 1. Criação da rede virtual isolada
docker network create minha-rede-personalizada

# 2. Implantação do serviço (Exemplo usando arquitetura Tailscale Sidecar)
docker compose -f docker-compose-tailscale.yml up -d
