# Camada 2 — Stack de Monitoramento (NOC)

Stack de monitoramento em Docker Compose: **Zabbix + PostgreSQL + Prometheus + node-exporter + Grafana**.

## Estrutura

```
noc-monitoring/
├── docker-compose.yml
├── .env                      # senhas e fuso (NÃO commitar)
├── .gitignore
├── prometheus/
│   └── prometheus.yml        # alvos de scrape
└── grafana/
    └── provisioning/
        └── datasources/
            └── prometheus.yml  # conecta o Grafana ao Prometheus automaticamente
```

## Passo a passo

### 1. Ajuste as senhas
Abra o arquivo `.env` e troque `TroqueEstaSenha123` por senhas suas (banco do Zabbix e admin do Grafana).

### 2. Suba a stack
No terminal, dentro da pasta `noc-monitoring/`:

```bash
docker compose up -d
```

A primeira vez baixa as imagens — pode levar alguns minutos. O Zabbix demora ~1-2 min pra inicializar o banco na primeira subida.

### 3. Verifique se subiu tudo
```bash
docker compose ps
```
Todos os containers devem estar com status `Up`.

### 4. Acesse as interfaces

| Serviço | URL | Login padrão |
|---------|-----|--------------|
| Zabbix Web | http://localhost:8080 | `Admin` / `zabbix` |
| Grafana | http://localhost:3000 | o que você pôs no `.env` |
| Prometheus | http://localhost:9090 | — |

> ⚠️ O login padrão do Zabbix é **`Admin`** (com A maiúsculo) e senha **`zabbix`**. Troque após o primeiro acesso em *Users → Admin → Change password*.

### 5. Valide cada peça (para os screenshots do portfólio)

**Prometheus** — em http://localhost:9090, vá em *Status → Targets*. Os alvos `prometheus` e `node-exporter` devem estar **UP** (verde). 📸 *Screenshot 1*

**Grafana** — em http://localhost:3000, vá em *Connections → Data sources*. O Prometheus já deve aparecer conectado (provisionado automaticamente). Importe um dashboard pronto: *Dashboards → New → Import → ID `1860`* (Node Exporter Full). 📸 *Screenshot 2 — o dashboard com gráficos de CPU/memória/rede*

**Zabbix** — em http://localhost:8080, faça login. 📸 *Screenshot 3 — tela inicial / dashboard do Zabbix*

### 6. Pare a stack (quando terminar — modelo MVP)
```bash
docker compose down          # para e remove containers, mantém os volumes
docker compose down -v       # remove TUDO incluindo dados (tear down completo)
```

## Comandos úteis

```bash
docker compose logs -f zabbix-server   # ver logs de um serviço
docker compose restart grafana         # reiniciar um serviço
docker compose ps                      # status dos containers
```

## Próximo passo (Camada 1 — GNS3)
Quando a topologia GNS3 estiver de pé, os servidores Linux dela ganham um `node-exporter` e são adicionados ao `prometheus/prometheus.yml` (já tem um bloco comentado pronto). O Zabbix passa a monitorar o roteador/switch via SNMP.
