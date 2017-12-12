# DockerHub Trigger Demo

## Fortune Docker image

Fortune is a tiny Docker image with *fortune* generated during `docker build`. Every time you are doing new docker build (without build cache), the new *fortune* is generated.

```sh
docker build -t codefresh/fortune --no-cache .
```

Running the *fortune* Docker image, will print out the *fortune* text.

```sh
docker run -it --rm codefresh/fortune
```

## Build and Push Pipeline

`build_pipeline.yaml` is a Codefresh pipeline that builds a new *fortune* image (with `--no-cache` flag) and pushes it to `codefresh/fortune` DockerHub repository.

## Run Pipeline

`run_pipeline.yaml` is a Codefresh pipeline that runs a `codefresh/fortune` image as a pipeline step and prints out the fortune stored within this image.

## Setting up a DockerHub Trigger

### Pre-request

Deploy Codefresh *Hermes* (Trigger Manager) and *Nomios* (DockerHub Event Provider) on some Kubernetes cluster.

### Codefresh Setup: pipeline and trigger

1. Create new Codefresh Repository from [trigger-examples](https://github.com/codefresh-io/trigger-examples)
1. Create a new `build` Codefresh pipeline from `dockerhub/build_pipeline.yaml` file
1. Create a new `run` Codefresh pipeline from `dockerhub/run_pipeline.yaml` file
1. Configure a new *Hermes* DockerHub trigger, named `index.docker.io:codefresh:fortune:push`
1. Get *Nomios* service public IP (for example `135.14.15.24`)

## DockerHub webhook setup

1. Navigate to `codefresh/fortune` DockerHub [webhook settings](https://hub.docker.com/r/codefresh/fortune/~/settings/webhooks/)
1. Add a new webhook URL `<nomios-public-ip>/dockerhub?secret<secret-key>`

### Try it now

```ascii
          DockerHub             Nomios                 Hermes                  Codefresh API
              +                   +                      +                         +
              |                   |                      |                         |
docker push   |                   |                      |                         |
              |                   |    trigger           |                         |
+-----------> |   webhook(push)   |     * norm.push      |                         |
              |                   |     * original       |                         |
              | +---------------> |     * secret         |                         |
              |                   |                      |                         |
              |                   | +------------------> |   run.pipeline(vars)    |
              |                   |                      |                         |
              |                   |                      | +-------------------->  |
              |                   |                      |                         |
              |                   |                      |                         |
              +                   +                      +                         +
```