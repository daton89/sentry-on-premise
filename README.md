# sentry-on-premise

If you build Web Application, you probably use Sentry. We are going to setup a self-hosted instance of Sentry running Docker.

The official documentation: [Install Sentry with docker](https://docs.sentry.io/server/installation/docker/)

## Installation of Sentry On Premise 

Clone (or fork) Sentry On Premise: 

```bash
git clone https://github.com/getsentry/onpremise.git ~/Projects/SentryOnPremise
```

There may need to be modifications to the included `docker-compose.yml` file to accommodate your needs or your environment. These instructions are a guideline for what you should generally do.

1. `mkdir -p data/{sentry,postgres}` - Make our local database and sentry config directories. This directory is bind-mounted with postgres so you don't lose state!
2. `docker-compose run --rm web config generate-secret-key` - Generate a secret key. Add it to `docker-compose.yml` in `base` as `SENTRY_SECRET_KEY`.
3. `docker-compose run --rm web upgrade` - Build the database. Use the interactive prompts to create a user account.
4. `docker-compose up -d` - Lift all services \(detached/background mode\).
5. Access your instance at `localhost:9000`!

Note that as long as you have your database bind-mounted, you should be fine stopping and removing the containers without worry.

### Install Sentry On Premise in production with Docker and Traefik

We would like to install Sentry with the help of Traefik.

The default Sentry On Premise configuration of docker containers work with links, but we need to customise the configuration of networks to make it works with Traefik.

 We can check out this [Github repo](https://github.com/daton89/sentry-on-premise) that i've prepared.

The problem that i had with On Premise was related to communication between containers. Since i didn't figure out why it doesn't work, i found a trick that consist in run before the default configuration to make it install and work and then run the daton89/sentry-on-premise docker-compose file to make it works with Traefik proxy.

### Updating Sentry

Updating Sentry using Docker Compose is really simple. 

1. Set the latest version in Dockerfile
2. Run `docker-compose build --pull` 
3. Load the Sentry Secret stored in the ./env file `source ./env`
4. Run `docker-compose run --rm web upgrade` (Run new migrations)
5. Run `docker-compose up -d` to recreate the services
