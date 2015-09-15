FROM phusion/passenger-ruby22

RUN \
  ifconfig

env RAILS_ENV production
env RUBY_GC_MALLOC_LIMIT 90000000

# Set env variables required to compile assets
env DISCOURSE_DB_HOST     postgres
env DISCOURSE_REDIS_HOST  redis
env DISCOURSE_DB_USERNAME postgres

RUN \
  apt-get update && \
  apt-get -y install nodejs build-essential git-core

# Download discourse
RUN \
  cd /tmp && \
  git clone https://github.com/discourse/discourse.git /srv/discourse

WORKDIR /srv/discourse

# Install bundler
RUN \
  gem install bundler

# Install Discourse dependencies
RUN \
  apt-get install -y libpq++-dev git-core imagemagick

# Install gems
RUN \
  bundle install --deployment -j4 --without development test

# Compile assets
RUN \
 bundle exec rake db:create db:migrate

# Compile assets
RUN \
 bundle exec rake assets:precompile

RUN \
  chown -R www-data:www-data /srv/discourse

RUN mkdir -p /var/nginx/cache
RUN chown -R www-data:www-data /var/nginx/cache

RUN \
  rm /etc/nginx/sites-enabled/default

ADD discourse.conf /etc/nginx/sites-enabled/discourse.conf
ADD discourse-env.conf /etc/nginx/main.d/discourse-env.conf

CMD ["/sbin/my_init"]

EXPOSE 80

VOLUME "/srv/discourse/public/uploads"

RUN \
  apt-get install -y advancecomp jhead jpegoptim libjpeg-progs optipng pngcrush pngquant ghostscript

RUN \
  npm install -g svgo gifsicle

RUN \
  apt-get clean

RUN \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN rm -f /etc/service/nginx/down
