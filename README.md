This repo is for building or starting Discourse images. Image is based on Ruby 2.2.x and Nginx with Phusion Passenger 5.x.
All actions are performed within the repo folder.

`git@github.com:pachkovsky/discourse-docker.git`

`cd discourse-docker`

# Building image

You need to have running Postgres and Redis servers to build Discourse image

`docker-compose -f build-dependencies.yml up -d`

Get IP address of Redis and Postgres

```bash
docker inspect --format '{{ .NetworkSettings.IPAddress }}' build_postgres
docker inspect --format '{{ .NetworkSettings.IPAddress }}' build_redis
```

And replace `postgres` and `redis` with exact values in build/discourse/Dockerfile

```
env DISCOURSE_DB_HOST    postgres
env DISCOURSE_REDIS_HOST redist
```

Now build the image

`docker build -t discourse build/discourse`

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

Copy .env.sample
`cp .env.sample .env`

Edit it but leave `DISCOURSE_DB_HOST=postgres` `DISCOURSE_REDIS_HOST=redis` as is

And run

`docker-compose up -d`

Done.

# Forum-only

Copy .env.sample

`cp .env.sample .env`

Edit everything you need.

Run Discourse:

`docker-compose -f standalone.yml up -d`

Done.