# 🏠 HomeLab Environment

Bem-vindo ao repositório central do meu **HomeLab**. Este projeto tem como objetivo centralizar a infraestrutura de automação residencial, gerenciamento de rede, comunicação e privacidade da minha casa.

Tudo é orquestrado via Docker Compose, facilitando a manutenção e portabilidade.

## 🚀 Serviços e Funcionalidades

A stack atual, acessível via Cloudflare Tunnel (`duvidoso.tech`), é composta pelos seguintes serviços:

### 🛡️ Privacidade & Rede

* **AdGuard Home**: Servidor DNS que atua como "buraco negro" para anúncios e rastreadores em toda a rede local.
* **Caddy V2**: Servidor Web e Proxy Reverso. Gerencia os certificados SSL automaticamente e roteia o tráfego do túnel para os containers internos.

### 💬 Comunicação & Automação

* **Evolution API (WhatsApp)**: API robusta para integração com WhatsApp. Conectada ao Postgres e Redis para alta performance.
* **n8n**: Ferramenta de automação de fluxo de trabalho (workflow automation). É o "cérebro" que conecta o WhatsApp aos outros dispositivos da casa.

### 🏗️ Infraestrutura Backend

* **PostgreSQL**: Banco de dados relacional para a Evolution API.
* **Redis**: Cache e gerenciamento de sessões para a Evolution API.
* **Portainer**: Interface Web para gerenciamento visual dos containers Docker.

---

## ⚙️ Pré-instalação (Configuração de Rede)

> **⚠️ Atenção:** Para que o AdGuard e outros serviços funcionem corretamente sem conflitos de porta (ex: porta 53 ou 80), utilizamos uma técnica de `macvlan shim`. Siga estes passos antes de subir os containers.

### Configurando o Shim Macvlan

1. **Limpeza de configurações antigas (se houver):**

   ```bash
   sudo ip link delete macvlan-shim
   ```

2. **Criar o novo link shim:**
   Substitua `eno1` pela sua interface física real (verifique com `ip a` ou `ifconfig`).

   ```bash
   sudo ip link add macvlan-shim link eno1 type macvlan mode bridge
   ```

3. **Atribuir IP para a ponte:**
   Vamos usar o IP `.11` para a ponte (host), reservando o `.10` para o container do PiHole/AdGuard.

   ```bash
   sudo ip addr add 192.168.1.11/32 dev macvlan-shim
   ```

4. **Levantar a interface:**

   ```bash
   sudo ip link set macvlan-shim up
   ```

5. **Criar rota estática:**
   Isso permite que o host do Docker (Linux) converse com o container na rede macvlan.

   ```bash
   sudo ip route add 192.168.1.10/32 dev macvlan-shim
   ```

---

## 🛠️ Instalação e Deploy

1. **Configurar Variáveis de Ambiente:**
   Crie um arquivo `.env` na raiz baseado no exemplo.

   > **🚨 Importante sobre Senhas:** Evite usar o caractere `$` (cifrão) nas senhas do `.env` (como `AUTHENTICATION_API_KEY`), pois o Linux/Docker tenta interpretar isso como variável. Use apenas letras e números ou escape como `$$`.

2. **Subir a Stack:**

   ```bash
   npm run up
   # ou
   docker compose up -d
   ```

3. **Verificar Logs:**

   ```bash
   docker logs -f evolution-api
   ```

## 🔗 Acessos (via Caddy/Tunnel)

Configurado no `docker/caddy/Caddyfile`:

| Serviço | URL | Descrição |
| :--- | :--- | :--- |
| **n8n** | `https://n8n.duvidoso.tech` | Editor de fluxos |
| **Evolution API** | `https://evapi.duvidoso.tech` | Documentação Swagger / API |
| **Portainer** | `https://portainer.duvidoso.tech` | Gestão Docker |
| **AdGuard** | `https://adguard.duvidoso.tech` | Admin do DNS Block |

## 📚 Futuras Implementações

* [ ] Dashboard unificado (Homepage ou Dashy).
* [ ] Home Assistant para dispositivos Zigbee/Tuya.
* [ ] Monitoramento com Prometheus + Grafana.
