FROM sendingtk/chatwoot:v3.13.8 AS builder

# Instala Node.js e Yarn para a recompilação dos assets
RUN apk update && apk add --no-cache \
  nodejs \
  yarn

# Define o diretório de trabalho
WORKDIR /app

# Informa o ambiente como produção
ENV RAILS_ENV=production
ENV SECRET_KEY_BASE=precompile_placeholder

# Copia o arquivo customizado do Tailwind CSS
COPY ./tailwind.config.js /app/tailwind.config.js

# Copia os arquivos de emails
COPY emails/*.* /app/app/views/devise/mailer/

# Copia os arquivos de branding para o diretório público
COPY branding/*.* /app/public/

# Limpa os assets antigos
RUN bundle exec rake assets:clean

# Recompila os assets
RUN bundle exec rake assets:precompile

# Etapa final - reduz a imagem para produção
FROM sendingtk/chatwoot:v3.13.8

# Define o diretório de trabalho
WORKDIR /app

# Copia os assets recompilados
COPY --from=builder /app/public /app/public
COPY --from=builder /app/app/views/devise/mailer/ /app/app/views/devise/mailer/
COPY --from=builder /app/tailwind.config.js /app/tailwind.config.js

