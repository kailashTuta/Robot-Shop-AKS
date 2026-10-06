# Three Tier Architecture Deployment on AZURE AKS

Stan's Robot Shop is a sample microservice application you can use as a sandbox to test and learn containerised application orchestration and monitoring techniques. It is not intended to be a comprehensive reference example of how to write a microservices application, although you will better understand some of those concepts by playing with Stan's Robot Shop. To be clear, the error handling is patchy and there is not any security built into the application.

You can get more detailed information from my [blog post](https://www.instana.com/blog/stans-robot-shop-sample-microservice-application/) about this sample microservice application.

This sample microservice application has been built using these technologies:

- NodeJS ([Express](http://expressjs.com/))
- Java ([Spring Boot](https://spring.io/))
- Python ([Flask](http://flask.pocoo.org))
- Golang
- PHP (Apache)
- MongoDB
- Redis
- MySQL ([Maxmind](http://www.maxmind.com) data)
- RabbitMQ
- Nginx
- AngularJS (1.x)

The various services in the sample application already include all required Instana components installed and configured. The Instana components provide automatic instrumentation for complete end to end [tracing](https://docs.instana.io/core_concepts/tracing/), as well as complete visibility into time series metrics for all the technologies.

To see the application performance results in the Instana dashboard, you will first need an Instana account. Don't worry a [trial account](https://instana.com/trial?utm_source=github&utm_medium=robot_shop) is free.

## Build from Source

To optionally build from source (you will need a newish version of Docker to do this) use Docker Compose. Optionally edit the `.env` file to specify an alternative image registry and version tag; see the official [documentation](https://docs.docker.com/compose/env-file/) for more information.

To download the tracing module for Nginx, it needs a valid Instana agent key. Set this in the environment before starting the build.

```shell
export INSTANA_AGENT_KEY="<your agent key>"
```

Now build all the images.

```shell
docker-compose build
```

If you modified the `.env` file and changed the image registry, you need to push the images to that registry

```shell
docker-compose push
```

## Run Locally

You can run it locally for testing.

If you did not build from source, don't worry all the images are on Docker Hub. Just pull down those images first using:

```shell
docker-compose pull
```

Fire up Stan's Robot Shop with:

```shell
docker-compose up
```

If you want to fire up some load as well:

```shell
docker-compose -f docker-compose.yaml -f docker-compose-load.yaml up
```

If you are running it locally on a Linux host you can also run the Instana [agent](https://docs.instana.io/quick_start/agent_setup/container/docker/) locally, unfortunately the agent is currently not supported on Mac.

There is also only limited support on ARM architectures at the moment.

## Marathon / DCOS

The manifests for robotshop are in the *DCOS/* directory. These manifests were built using a fresh install of DCOS 1.11.0. They should work on a vanilla HA or single instance install.

You may install Instana via the [DCOS package manager](https://github.com/dcos/examples/tree/master/instana-agent/1.9).

## Kubernetes

You can run Kubernetes locally using [minikube](https://github.com/kubernetes/minikube) or on one of the many cloud providers.

The Docker container images are all available on [Docker Hub](https://hub.docker.com/u/robotshop/).

Install Stan's Robot Shop to your Kubernetes cluster using the [Helm](K8s/helm/README.md) chart.

To deploy the Instana agent to Kubernetes, just use the [helm](https://github.com/instana/helm-charts) chart.

## Accessing the Store

If you are running the store locally via *docker-compose up* then, the store front is available on localhost port 8080 [http://localhost:8080](http://localhost:8080/)

If you are running the store on Kubernetes via minikube then, find the IP address of Minikube and the Node Port of the web service.

```shell
minikube ip
kubectl get svc web
```

If you are using a cloud Kubernetes / Openshift / Mesosphere then it will be available on the load balancer of that system.

## Load Generation

A separate load generation utility is provided in the `load-gen` directory. This is not automatically run when the application is started. The load generator is built with Python and [Locust](https://locust.io). The `build.sh` script builds the Docker image, optionally taking *push* as the first argument to also push the image to the registry. The registry and tag settings are loaded from the `.env` file in the parent directory. The script `load-gen.sh` runs the image, it takes a number of command line arguments. You could run the container inside an orchestration system (K8s) as well if you want to, an example descriptor is provided in K8s directory. For End-user Monitoring, load is not automatically generated but by navigating through Robot Shop in a browser. For more details, see the [README](load-gen/README.md) in the load-gen directory.

## Website Monitoring / End-User Monitoring

### Docker Compose

To enable Website Monitoring / End-User Monitoring (EUM), see the official [documentation](https://docs.instana.io/website_monitoring/) for how to create a configuration. There is no need to inject the JavaScript fragment into the page; this is handled automatically. Make a note of the unique key and set the environment variables `INSTANA_EUM_KEY` and `INSTANA_EUM_REPORTING_URL` for the web image within `docker-compose.yaml`.

### Kubernetes deployment

The Helm chart for installing Stan's Robot Shop supports setting the key and endpoint URL required for website monitoring; see the [README](K8s/helm/README.md).

## Prometheus

The cart and payment services both have Prometheus metric endpoints. These are accessible on `/metrics`. The cart service provides:

- Counter of the number of items added to the cart

The payment service provides:

- Counter of the number of items purchased
- Histogram of the total number of items in each cart
- Histogram of the total value of each cart

To test the metrics use:

```shell
curl http://<host>:8080/api/cart/metrics
curl http://<host>:8080/api/payment/metrics
```

## AKS Deployment Steps

1. Provision an Azure Kubernetes Service (AKS) cluster.
2. Create a Dockerfile for every microservice and database component.
3. Build the container images and push them to your container registry.
4. Create Kubernetes YAML manifests for every microservice and database component.
5. Create a Helm chart for the application.
6. Configure `kubectl` to connect to the AKS cluster.
7. Verify that `kubectl` is connected to the intended cluster:

   ```shell
   kubectl config current-context
   ```

8. Navigate to the Helm chart directory under `AKS`, create the application namespace, and install the chart:

   ```shell
   kubectl create ns robot-shop
   helm install robot-shop --namespace robot-shop
   ```

9. In the Redis StatefulSet manifest, configure the persistent-volume access mode, storage class, and volume mode.
10. Enable the ingress controller add-on for the AKS cluster.
11. Create an ingress manifest, then apply it and confirm that the ingress resource was created:

    ```shell
    kubectl apply -f <ingress>.yaml
    kubectl get ing -n robot-shop
    ```

12. Open the application using the ingress IP address or hostname.
