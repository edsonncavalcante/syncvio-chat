# Etapa de build (compilação de dependências e assets)
FROM sendingtk/chatwoot:v3.13.8 AS builder

# Instala apenas as dependências essenciais para o build
RUN apk update && apk add --no-cache \
    nodejs \
    yarn \
    postgresql-dev \
    imagemagick

WORKDIR /app
ENV RAILS_ENV=production

# Define a chave secreta para pré-compilação de assets
ENV SECRET_KEY_BASE=precompile_placeholder

# Verifica se os arquivos necessários existem antes de copiá-los
# Copia e instala as dependências Ruby
COPY Gemfile Gemfile.lock ./ 
RUN test -f Gemfile && test -f Gemfile.lock \
    && bundle install --without development test --deployment --clean

# Copia e instala as dependências JavaScript
COPY package.json yarn.lock ./ 
RUN test -f package.json && test -f yarn.lock \
    && yarn install --check-files --frozen-lockfile

# Copia o restante do código e arquivos customizados
COPY . ./

# Compila os assets
RUN bundle exec rake assets:precompile

# Remove arquivos desnecessários do ambiente de build
RUN rm -rf \
    tmp/cache \
    log/* \
    spec \
    test \
    .git \
    .gitignore \
    /root/.cache \
    /usr/local/bundle/cache/*

# Etapa final (imagem mínima)
FROM sendingtk/chatwoot:v3.13.8

WORKDIR /app

# Copia apenas os arquivos necessários da etapa de build
COPY --from=builder /app/public /app/public
COPY --from=builder /app/app/views/devise/mailer/ /app/app/views/devise/mailer/
COPY --from=builder /app/config /app/config

# Define o comando padrão de execução
CMD ["bundle", "exec", "puma", "-C", "config/puma.rb"]
