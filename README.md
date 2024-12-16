[![Build Stable](https://github.com/frappe/frappe_docker/actions/workflows/build_stable.yml/badge.svg)](https://github.com/frappe/frappe_docker/actions/workflows/build_stable.yml)
[![Build Develop](https://github.com/frappe/frappe_docker/actions/workflows/build_develop.yml/badge.svg)](https://github.com/frappe/frappe_docker/actions/workflows/build_develop.yml)

Everything about [Frappe](https://github.com/frappe/frappe) and [ERPNext](https://github.com/frappe/erpnext) in containers.

# Getting Started

To get started you need [Docker](https://docs.docker.com/get-docker/), [docker-compose](https://docs.docker.com/compose/), and [git](https://docs.github.com/en/get-started/getting-started-with-git/set-up-git) setup on your machine. For Docker basics and best practices refer to Docker's [documentation](http://docs.docker.com).

Once completed, chose one of the following two sections for next steps.

### Try on your Dev environment

First clone the repo:

```sh
git clone https://github.com/frappe/frappe_docker
cd frappe_docker
```

## FOR WINDOWS

```sh
export APPS_JSON_BASE64=$(base64 -w 0 ./development/apps.json)
```

## FOR MAC

```sh
base64 -i ./development/apps.json -o ./development/apps.json.b64

export APPS_JSON_BASE64=$(base64 -i ./development/apps.json)
```


## Verify generated hash

```sh
echo $APPS_JSON_BASE64
```

## Docker build command

```sh
docker build \
  --build-arg=FRAPPE_PATH=https://github.com/frappe/frappe \
  --build-arg=FRAPPE_BRANCH=version-15 \
  --build-arg=PYTHON_VERSION=3.11.6 \
  --build-arg=NODE_VERSION=18.18.2 \
  --build-arg=APPS_JSON_BASE64=$APPS_JSON_BASE64 \
  --no-cache \
  --platform=linux/amd64 \
  --tag=frappe-custom:latest \
  --file=images/custom/Containerfile .
```


### List all the images, you should see first_image in the list
```sh
docker images
```

### Run the docker image in the background using the -d tag
```sh
docker run -d frappe-custom:latest
```

### List running images to get the container id of our image
```sh
docker ps
```

### Exec into the container to check if all apps are in the image
```sh
docker exec -it {CONTAINER_ID} /bin/bash
```

## Push image
```sh
docker push frappe-custom:latest
```

## Try image

Replace image and erpnext install command in pwd.yml.

```sh
sed -i 's|frappe/erpnext:v15.45.5|frappe-custom:latest' pwd.yml
sed -i 's|--install-app erpnext|--install-app custom|g' pwd.yml
```

## Start services

```sh
docker compose -p frappe-custom -f pwd.yml up -d
```

Check site logs

```sh
docker logs frappe-custom-create-site-1 -f
```

Open site http://localhost:8080


### To run on ARM64 architecture follow this instructions

After cloning the repo run this command to build multi-architecture images specifically for ARM64.

`docker buildx bake --no-cache --set "*.platform=linux/arm64"`

and then

- add `platform: linux/arm64` to all services in the pwd.yaml
- replace the current specified versions of erpnext image on `pwd.yml` with `:latest`

Then run: `docker compose -f pwd.yml up -d`

## Final steps

Wait for 5 minutes for ERPNext site to be created or check `create-site` container logs before opening browser on port 8080. (username: `Administrator`, password: `admin`)

If you ran in a Dev Docker environment, to view container logs: `docker compose -f pwd.yml logs -f create-site`. Don't worry about some of the initial error messages, some services take a while to become ready, and then they go away.

# Documentation

### [Frequently Asked Questions](https://github.com/frappe/frappe_docker/wiki/Frequently-Asked-Questions)

### [Production](#production)

- [List of containers](docs/list-of-containers.md)
- [Single Compose Setup](docs/single-compose-setup.md)
- [Environment Variables](docs/environment-variables.md)
- [Single Server Example](docs/single-server-example.md)
- [Setup Options](docs/setup-options.md)
- [Site Operations](docs/site-operations.md)
- [Backup and Push Cron Job](docs/backup-and-push-cronjob.md)
- [Port Based Multi Tenancy](docs/port-based-multi-tenancy.md)
- [Migrate from multi-image setup](docs/migrate-from-multi-image-setup.md)
- [running on linux/mac](docs/setup_for_linux_mac.md)

### [Custom Images](#custom-images)

- [Custom Apps](docs/custom-apps.md)
- [Build Version 10 Images](docs/build-version-10-images.md)

### [Development](#development)

- [Development using containers](docs/development.md)
- [Bench Console and VSCode Debugger](docs/bench-console-and-vscode-debugger.md)
- [Connect to localhost services](docs/connect-to-localhost-services-from-containers-for-local-app-development.md)

