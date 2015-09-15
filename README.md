This repo is for building or starting Discourse image. Image is based on Ruby 2.2.x and Nginx with Phusion Passenger 5.x.
All actions are performed within the repo folder.

```bash
git@github.com:pachkovsky/discourse-docker.git
cd discourse-docker
```

# Building image

You need to have running Postgres and Redis servers to build Discourse image

```bash
docker-compose -f build-dependencies.yml up -d
```

Get IP address of Redis and Postgres

```bash
docker inspect --format '{{ .NetworkSettings.IPAddress }}' build_postgres
docker inspect --format '{{ .NetworkSettings.IPAddress }}' build_redis
```

And replace `postgres` and `redis` with exact values in `build/discourse/Dockerfile`

```bash
env DISCOURSE_DB_HOST    postgres
env DISCOURSE_REDIS_HOST redis
```

Now build the image

```bash
docker build -t discourse build/discourse
```

Push it to your Docker registry, e.g:

```bash
docker tag discourse pachkovskij/discourse
docker push pachkovskij/discourse
```

Clean up

```bash
docker-compose -f build-dependencies.yml stop
docker-compose -f build-dependencies.yml rm
```

# All-in-one

Copy and edit `.env.sample`

```bash
cp .env.sample .env
```

**IMPORTANT:** leave `DISCOURSE_DB_HOST=postgres` `DISCOURSE_REDIS_HOST=redis` as is.

Run Discourse:

```bash
docker-compose up -d
```

Create and migrate database
```
docker exec discourse-web bundle exec rake db:create db:migrate
```

Done.

# Forum-only

Copy and edit `.env.sample`

```bash
cp .env.sample .env
```

Edit everything you need.

Run Discourse:

```bash
docker-compose -f standalone.yml up -d
```

Create and migrate database
```
docker exec discourse-web bundle exec rake db:create db:migrate
```

Done.