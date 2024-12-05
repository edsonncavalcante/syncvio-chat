# Etapa de build (compilação de dependências e assets)
FROM sendingtk/chatwoot:v3.13.8 as builder

# Instala apenas as dependências essenciais
RUN apk update && apk add --no-cache \
    nodejs-current \
    yarn \
    postgresql-dev \
    imagemagick

# Define o diretório de trabalho
WORKDIR /app

# Define o ambiente como produção
ENV RAILS_ENV=production
ENV SECRET_KEY_BASE=precompile_placeholder

# Instala dependências Ruby e Yarn
RUN bundle install --without development test
RUN yarn install --check-files

# Copia os arquivos customizados para branding e emails
COPY branding/*.* /app/public/
COPY emails/*.* /app/app/views/devise/mailer/

# Compila os assets
RUN bundle exec rake assets:clean
RUN bundle exec rake assets:precompile --trace

# Etapa final (imagem mínima)
FROM sendingtk/chatwoot:v3.13.8

WORKDIR /app

# Copia apenas os arquivos necessários da etapa de build
COPY --from=builder /app/public /app/public
COPY --from=builder /app/app/views/devise/mailer/ /app/app/views/devise/mailer/
COPY --from=builder /app/tmp /app/tmp
COPY --from=builder /app/log /app/log
