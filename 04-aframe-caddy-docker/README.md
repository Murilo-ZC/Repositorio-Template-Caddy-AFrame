# 04 — A-Frame servido pelo Caddy em Docker

Junta tudo: a cena A-Frame do diretório 01 empacotada em uma imagem Docker com o Caddy.

Este diretório é **independente**: a pasta `site/` tem a sua própria cópia do `index.html` (partiu do exemplo do 01, mas não depende dele). Daqui em diante, é aqui que a experiência evolui.

```
04-aframe-caddy-docker/
├── Caddyfile           # configuração do servidor
├── Dockerfile          # monta a imagem: caddy + site
├── docker-compose.yml  # build + execução (com a pasta site/ montada)
└── site/
    └── index.html      # cena A-Frame
```

## Rodar com Docker Compose (desenvolvimento)

```bash
cd 04-aframe-caddy-docker
docker compose up --build
```

Acesse https://localhost:8443. Para parar: `Ctrl + C` e depois `docker compose down`.

O `docker-compose.yml` monta a pasta `site/` dentro do contêiner, por cima da cópia que está na imagem. Assim, editou o `index.html`, recarregou o navegador, viu a mudança. Para alterações no `Caddyfile`, rode `docker compose up --build` de novo.

## Rodar só com Docker (como será publicado)

```bash
docker build -t aframe-caddy .
docker run --rm -p 8080:8080 -p 8443:8443 -v caddy_data:/data aframe-caddy
```

Aqui não há pasta montada: o contêiner serve a cópia que o `Dockerfile` colocou na imagem. Editar o HTML **não** muda nada até você refazer o `docker build`. É assim que a imagem se comporta em um servidor.

## Diferença em relação ao 03

| | 03 | 04 |
|---|---|---|
| Imagem | oficial `caddy`, sem alterações | imagem própria (`Dockerfile`) |
| Conteúdo | só montado por volume | copiado para a imagem (e montado por volume no compose, para desenvolver) |
| Alterou o `index.html`? | recarregue o navegador | com compose: recarregue; com `docker run`: refaça o build |

A imagem do 04 é um artefato único e portátil, que é o que você publicaria em um servidor.

## HTTPS

O Caddy só ativa HTTPS quando o site tem um **nome** (`localhost { ... }`). Se o `Caddyfile` usar apenas a porta (`:80 { ... }`), ele serve só HTTP.

Com o `Caddyfile` deste diretório:

- https://localhost:8443 → a página, via HTTPS
- http://localhost:8080 → redireciona para o HTTPS

O certificado é emitido pela CA local do Caddy, que o seu sistema ainda não conhece. Então, na primeira vez, o navegador mostra um aviso de "conexão não segura". Você pode aceitar o aviso ou, melhor, confiar na CA uma vez:

```powershell
# 1. Com o contêiner rodando, copie o certificado raiz para fora
docker compose cp aframe:/data/caddy/pki/authorities/local/root.crt ./caddy-root.crt

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
