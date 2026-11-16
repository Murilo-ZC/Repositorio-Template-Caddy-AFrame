# Imagem oficial do Caddy como base
FROM caddy:2-alpine

# Configuração do servidor
COPY Caddyfile /etc/caddy/Caddyfile

# Conteúdo da experiência A-Frame (fica "congelado" dentro da imagem)
COPY site/ /srv/

EXPOSE 8080 8443
