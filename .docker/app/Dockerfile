FROM harbor.k8s.temple.edu/library/ruby:2.7-alpine

WORKDIR /app

COPY . .

USER root

ARG SECRET_KEY_BASE

RUN apk add -U --no-cache \
      bash=5.1.0-r0 \
      libxslt=1.1.34-r0 \
      tzdata=2021a-r0 \
      shared-mime-info=2.0-r0 \
      sqlite-libs=3.34.1-r0 && \
    apk add -U --no-cache --virtual build-dependencies \
      git=2.30.2-r0 \
      build-base=0.5-r2 \
      libxslt-dev=1.1.34-r0 \
      sqlite-dev=3.34.1-r0 \
      nodejs=14.16.1-r1 \
      yarn=1.22.10-r0 && \
    gem install bundler:2.2.17 && \
    bundle config build.nokogiri --use-system-libraries && \
    bundle config set --local without "development test" && \
    bundle install --jobs=8 && \
    find "$GEM_HOME" -name Gemfile.lock -exec rm "{}" \; && \
    find "$GEM_HOME" -name yarn.lock -exec rm "{}" \; && \
    RAILS_ENV=production SECRET_KEY_BASE=$SECRET_KEY_BASE bundle exec rails assets:precompile && \
    rm -rf node_modules && \
    rm -rf /var/cache/apk/ && \
    chown nobody -R /app/db && \
    chown nobody -R /app/public && \
    chown nobody -R /app/tmp && \
    chown nobody -R /app/log

USER nobody

ENTRYPOINT [".docker/app/entrypoint.sh"]
EXPOSE 3000

CMD ["rails", "server", "-b", "0.0.0.0"]
