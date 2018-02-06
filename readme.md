# DevPledge Core API

## Logging into docker
In order to pull from a private Docker Hub repo or push to one, you will need to login to docker. You will only need to do this once per session. There are 3 ways to do this, the first 2 of which are designed for being used in automation (CI):

Note: where env vars are specified before commands you could set these as permanent env vars on your machine.
1. `DOCKER_USER=my_user DOCKER_PASS=my_pass make login`
2. `DOCKER_USER=my_user DOCKER_PASS_FILE=/my_docker_password.txt make login` where `/my_docker_password.txt` contains your password. This method prevents your password being in your terminal history.
3. `docker login -u my_user` This method will prompt for your password on stdin.

## Containers
### devpledge/core-api
This container runs `php-fpm` on port `9000`.

#### Testing core-api code/development container
If you need to make changes to the container, e.g. install a new php module:
1. Make the required changes in `api.dockerfile`
2. Run `make build-core-api`. This command will create a local image reflecting your changes.
3. Start the container in development mode, see "Starting core-api container".

Otherwise if you are just testing code changes start the container in development mode, see "Start core-api container".

#### Starting core-api container
If you have not made changes to a container and you have previsouly ran the image in the mode you want, you can just start the existing container: `docker start dp-core-api`.

To get started you need to create a container from an image. For the core-api there are two ways to run a container:
* Development mode: `make run-core-api-dev`. Local file changes will be reflected on the container.
* Production mode: `make run-core-api-prod`. Only configuration files will be reflected on the container.

Before running the container you should remove the existing `dp-core-api` container (if it exists - check with `docker ps -a`) with the command `docker rm -f dp-core-api`.

#### Pushing production code
Once you have made code changes that you want to go live, you should build your image in production mode and push that container to docker hub.

1. Git commit. You must do this first as the new image will be tagged with the git commit hash.
2. Build the production image: `make build-core-api-prod`. This will create an image with all of your code changes and tag it with the current git commit hash, and `latest`.
3. See "Logging into docker"
4. Push the new image to docker hub: `make push-core-api`.

### devpledge/core-api-web
This container runs `nginx` on port `8080`.
There core-api container should already be running before starting this container as it requires a link to core-api.

#### Updating the core-api-web container
If you need to make changes to the container:
1. Make the required changes in `web.dockerfile` or `config/vhost.conf`
2. Run `make build-web`. This command will create a local image reflecting your changes.
3. Start the container, see "Starting web container".

#### Starting web container
If you have not made changes to a container or you have previsouly ran the image in the mode you want, you can just start the existing container: `docker start dp-core-api-web`.

To get started you need to create a container from an image.
* `make run-web`

Before running the container you should remove the existing `dp-core-api-web` container (if it exists - check with `docker ps -a`) with the command `docker rm -f dp-core-api-web`.

#### Pushing production code
Once you have made changes to this container that you want to go live, you should push your image to docker hub.

1. Git commit. You must do this first as the new image will be tagged with the git commit hash.
2. Build the production image: `make build-web`. This will create an image with all of your changes and tag it with the current git commit hash, and `latest`.
3. See "Logging into docker"
4. Push the new image to docker hub: `make push-web`.

