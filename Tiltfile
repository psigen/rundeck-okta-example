# See: https://docs.tilt.dev/tiltfile_authoring
load('ext://dotenv', 'dotenv')
dotenv()

# Configure `docker-compose` and watch for changes in the docker config.
docker_compose("./compose.yaml")
# Tell docker-compose to use docker-bake for better build performance.
os.putenv("COMPOSE_BAKE", "true")
