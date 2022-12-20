# takahē on Fly.io

Here's a short tutorial to get [Takahē](https://jointakahe.org) running on [Fly.io](https://fly.io).
It's intentionally a very basic setup both in terms of the Fly.io configuration as well as the Takahē installation, but should get things going.

As far as I understand Fly.io pricing, the minimal version of this should even run within the free tier.

The following assumes a working Fly.io account, the installed Fly commandline tool, [`flyctl`](https://fly.io/docs/flyctl/), and a previous authentication via `flyctl auth`.

## Fly.io app name

Let's set up a name for the Fly.io app:

    APP_NAME=<YOUR APP NAME>

## Create a Postgres database

First, let's create a [Fly Postgres](https://fly.io/docs/postgres/) instance. (Example for the smallest instance in Frankfurt, adjust according to your needs.)

    flyctl postgres create --name $APP_NAME-postgres --region fra --initial-cluster-size 1 --vm-size shared-cpu-1x --volume-size 1

## Create an application

Next, we create the (empty) Fly.io app.

    flyctl apps create --name $APP_NAME

## Attach database to application

Now, we can attach the Postgres instance to the app and set the correct environment variable for the connection URL for Takahē. This also creates the database and the database user.

    flyctl postgres attach $APP_NAME-postgres -a $APP_NAME --database-name takahe --database-user takahe --variable-name TAKAHE_DATABASE_SERVER

## Setup secrets

Next, we need to generate a `SECRET_KEY` for Takahē and want to set up a few encrypted environment variables for our Takahē installation. (See [Media Configuration](https://docs.jointakahe.org/en/latest/installation/#media-configuration) and [Email Configuration](https://docs.jointakahe.org/en/latest/installation/#email-configuration).)
There's no need to set `TAKAHE_DATABASE_SERVER` as that is already done by Fly in the database attachment step above.

    flyctl secrets set -a $APP_NAME TAKAHE_SECRET_KEY=<SECRET KEY>
    flyctl secrets set -a $APP_NAME TAKAHE_MEDIA_BACKEND=s3://access-key:secret-key@endpoint-url/bucket-name
    flyctl secrets set -a $APP_NAME TAKAHE_EMAIL_SERVER=sendgrid://apikey

## Setup fly.toml

The main configuration for our Fly.io app lives in [`fly.toml`](fly.toml). Feel free to adjust the example base config file in this repository, especially considering the [Environment Variables](https://docs.jointakahe.org/en/latest/installation/#environment-variables) for configuring Takahē further.

## Deploy

Now, from the directory of the `fly.toml` file, we can deploy our app.

    flyctl deploy

Takahē should now be running at \<YOUR APP DOMAIN>.

## Create admin user

To log into Takahē, first create a admin user by logging into the Fly.io instance:

    flyctl ssh console
    /takahe/manage.py createsuperuser

## Login, Takahē setup

Now, go to \<YOUR APP DOMAIN> to login and complete the setup of the Takahē instance.

    flyctl open
