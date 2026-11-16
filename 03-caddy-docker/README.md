# 03 — Caddy em um contêiner Docker

Mesmo servidor do diretório 02, mas sem instalar o Caddy: usamos a imagem oficial `caddy` do Docker Hub.

```
03-caddy-docker/
├── Caddyfile           # configuração do servidor (montada no contêiner)
├── docker-compose.yml  # define o contêiner
└── site/
    └── index.html      # conteúdo servido (montado em /srv)
```

## Pré-requisito

[Docker Desktop](https://www.docker.com/products/docker-desktop/) (Windows/macOS) ou Docker Engine + Compose (Linux).

```bash
docker --version
docker compose version
```

## Rodar com Docker Compose

```bash
cd 03-caddy-docker
docker compose up
```

Acesse https://localhost:8443. Para parar: `Ctrl + C` e depois `docker compose down`.

## Rodar só com `docker run`

PowerShell (Windows):

```powershell
docker run --rm -p 8080:8080 -p 8443:8443 `
  -v "${PWD}/Caddyfile:/etc/caddy/Caddyfile:ro" `
  -v "${PWD}/site:/srv:ro" `
  -v caddy_data:/data `
  caddy:2-alpine
```

Bash (macOS/Linux):

```bash
docker run --rm -p 8080:8080 -p 8443:8443 \
  -v "$PWD/Caddyfile:/etc/caddy/Caddyfile:ro" \
  -v "$PWD/site:/srv:ro" \
  -v caddy_data:/data \
  caddy:2-alpine
```

## Editando os arquivos

- **HTML (`site/`)**: a pasta é montada no contêiner, então a mudança vale na hora. Basta recarregar o navegador. O `Caddyfile` envia `Cache-Control: no-cache` para impedir que o navegador mostre uma versão antiga guardada em cache.
- **`Caddyfile`**: o Caddy só lê a configuração ao iniciar. Depois de editar, rode `docker compose restart`.

## HTTPS

O Caddy só ativa HTTPS quando o site tem um **nome** (`localhost { ... }`). Se o `Caddyfile` usar apenas a porta (`:80 { ... }`), ele serve só HTTP.

Com o `Caddyfile` deste diretório:

- https://localhost:8443 → a página, via HTTPS
- http://localhost:8080 → redireciona para o HTTPS

O certificado é emitido pela CA local do Caddy, que o seu sistema ainda não conhece. Então, na primeira vez, o navegador mostra um aviso de "conexão não segura". Você pode aceitar o aviso ou, melhor, confiar na CA uma vez:

```powershell
# 1. Com o contêiner rodando, copie o certificado raiz para fora
docker compose cp caddy:/data/caddy/pki/authorities/local/root.crt ./caddy-root.crt

# 2. Windows: instale para o seu usuário (não precisa de admin)
certutil -addstore -user Root caddy-root.crt
```

No macOS: `sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain caddy-root.crt`. O Firefox usa um repositório próprio: importe em *Configurações → Privacidade e Segurança → Certificados → Ver certificados → Autoridades*.

A CA fica guardada no volume `caddy_data`, então só é preciso fazer isso uma vez. Se você rodar `docker compose down -v`, o volume é apagado e uma CA nova é criada.

### Acessando de outro dispositivo (headset)

Para abrir pelo IP da máquina na rede (ex.: `192.168.0.10`), adicione o IP ao `Caddyfile`:

```
{
	http_port 8080
	https_port 8443
	skip_install_trust
	default_sni 192.168.0.10
}

localhost, 192.168.0.10 {
	...
}
```

O `default_sni` é necessário porque o navegador não informa o nome do site quando a conexão é feita por IP. No headset, acesse `https://192.168.0.10:8443` e aceite o aviso de certificado (ou instale o `caddy-root.crt` no dispositivo).
