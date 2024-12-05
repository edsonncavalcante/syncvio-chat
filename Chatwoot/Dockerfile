# Etapa de build (compilação de dependências e assets)
FROM sendingtk/chatwoot:v3.13.8 AS builder

RUN apk update && apk add --no-cache \
    nodejs-current \
    yarn \
    postgresql-dev \
    imagemagick

WORKDIR /app
ENV RAILS_ENV=production

# Adicione a configuração da chave diretamente
ENV SECRET_KEY_BASE=e6a1d765b64e8e123abc4a78907c8f675dfa2cd6dce56f3d12345ef12345abcd

# Instala dependências Ruby e Yarn
RUN bundle install --without development test --deployment
RUN yarn install --check-files

# Copia os arquivos customizados para branding e emails
COPY branding/*.* /app/public/
COPY emails/*.* /app/app/views/devise/mailer/

# Compila os assets
RUN bundle exec rake assets:clean
RUN bundle exec rake assets:precompile --trace

# Remove arquivos desnecessários do ambiente de build
RUN rm -rf \
    tmp/cache \
    log/* \
    spec \
    test \
    node_modules \
    /root/.cache \
    /usr/local/bundle/cache/*

# Etapa final (imagem mínima)
FROM sendingtk/chatwoot:v3.13.8

WORKDIR /app

# Copia apenas os arquivos necessários da etapa de build
COPY --from=builder /app/public /app/public
COPY --from=builder /app/app/views/devise/mailer/ /app/app/views/devise/mailer/

# Remove arquivos desnecessários que possam ter sido copiados
RUN rm -rf \
    tmp/cache \
    log/* \
    spec \
    test \
    node_modules

# Define o comando padrão
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]
