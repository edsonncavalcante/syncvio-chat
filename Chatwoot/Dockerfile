# Etapa de build (compilação de dependências e assets)
FROM sendingtk/chatwoot:v3.13.8 AS builder

WORKDIR /app
ENV RAILS_ENV=production
ENV SECRET_KEY_BASE=precompile_placeholder

# Copia os arquivos customizados para branding e emails
COPY branding/*.* /app/public/
COPY emails/*.* /app/app/views/devise/mailer/

# Recompila os assets
RUN bundle exec rake assets:clean
RUN bundle exec rake assets:precompile

# Etapa final (imagem mínima)
FROM sendingtk/chatwoot:v3.13.8

WORKDIR /app

# Copia apenas os arquivos alterados da etapa de build
COPY --from=builder /app/public /app/public
COPY --from=builder /app/app/views/devise/mailer/ /app/app/views/devise/mailer/
