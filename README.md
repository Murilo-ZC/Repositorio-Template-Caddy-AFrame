# Repositorio-Template-Caddy-AFrame

Repositório de template para criação de um projeto utilizando o [A-Frame](https://aframe.io) e o [Caddy](https://caddyserver.com) para servir essa aplicação.

O repositório é dividido em quatro etapas independentes, da mais simples à mais completa:

| Diretório | O que mostra | Precisa de |
|---|---|---|
| [`01-aframe-minimo`](01-aframe-minimo) | Cena A-Frame aberta direto no navegador | Só o navegador |
| [`02-caddy-local`](02-caddy-local) | Servir uma página estática com o Caddy instalado na máquina | Caddy |
| [`03-caddy-docker`](03-caddy-docker) | O mesmo servidor, usando a imagem oficial do Caddy no Docker | Docker |
| [`04-aframe-caddy-docker`](04-aframe-caddy-docker) | A cena A-Frame empacotada em uma imagem Docker com o Caddy | Docker |

Cada diretório tem um `README.md` com o passo a passo. O exemplo 02 responde em http://localhost:8080; os exemplos 03 e 04 (Docker) respondem em https://localhost:8443.
