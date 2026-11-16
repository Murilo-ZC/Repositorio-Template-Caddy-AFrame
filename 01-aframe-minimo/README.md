# 01 — A-Frame mínimo (só o navegador)

O projeto mais simples possível: um único `index.html` que carrega o A-Frame por CDN.

## Como rodar

1. Dê um duplo clique em `index.html` (ou arraste o arquivo para o navegador).
2. Pronto. Use o mouse para olhar ao redor e `W A S D` para andar.

Não precisa instalar nada, mas precisa de internet para baixar o A-Frame do CDN.

## Limitações de abrir via `file://`

- O botão de VR/AR (WebXR) exige contexto seguro (`https://` ou `localhost`). Pelo `file://` a cena aparece, mas o modo imersivo pode não funcionar.
- Texturas, modelos 3D (`.glb`) e outros arquivos externos costumam ser bloqueados pelo navegador quando a página é aberta via `file://` (CORS).

Para resolver isso, a página precisa ser servida por um servidor web — é o que os diretórios 02, 03 e 04 mostram.
