# 02 — Caddy instalado localmente

Servir uma página estática com o [Caddy](https://caddyserver.com) instalado direto na máquina.

```
02-caddy-local/
├── Caddyfile      # configuração do servidor
└── site/
    └── index.html # conteúdo servido
```

## 1. Instalar o Caddy

| Sistema | Comando |
|---|---|
| Windows | `winget install CaddyServer.Caddy` (ou `choco install caddy` / `scoop install caddy`) |
| macOS | `brew install caddy` |
| Linux (Debian/Ubuntu) | siga https://caddyserver.com/docs/install#debian-ubuntu-raspbian |

Alternativa: baixe o executável em https://caddyserver.com/download e coloque-o no `PATH`.

Confira a instalação:

```bash
caddy version
```

## 2. Rodar

Dentro desta pasta:

```bash
cd 02-caddy-local
caddy run
```

O Caddy lê o `Caddyfile` da pasta atual automaticamente. Acesse http://localhost:8080.

Para parar: `Ctrl + C`.

## Atalho sem Caddyfile

O mesmo resultado em uma linha:

```bash
caddy file-server --root site --listen :8080
```
