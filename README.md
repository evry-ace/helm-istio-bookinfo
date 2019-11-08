# Istio Bookinfo Example Application Helm Chart

This Helm Chart deploys the [Istio Bookinfo sample application][istio-bookinfo]
composed of four separate microservices used to demonstrate various Istio
features. The application displays information about a book, similar to a single
catalog entry of an online book store. Displayed on the page is a description of
the book, book details (ISBN, number of pages, and so on), and a few book
reviews.

[istio-bookinfo]: https://istio.io/docs/examples/bookinfo/

The Bookinfo application is broken into four separate microservices:

* `productpage`. The productpage microservice calls the details and `reviews`
  microservices to populate the page.
* `details`. The details microservice contains book information.
* `reviews`. The `reviews` microservice contains book reviews. It also calls the
  `ratings` microservice.
* `ratings`. The `ratings` microservice contains book ranking information that
  accompanies a book review.

There are 3 versions of the `reviews` microservice:

* Version v1 doesn’t call the `ratings` service.
* Version v2 calls the `ratings` service, and displays each rating as 1 to 5
  black stars.
* Version v3 calls the `ratings` service, and displays each rating as 1 to 5 red
  stars.

The end-to-end architecture of the application is shown below.

![Istio Bookinfo Architecture](https://istio.io/docs/examples/bookinfo/noistio.svg)

This application is polyglot, i.e., the microservices are written in different
languages. It’s worth noting that these services have no dependencies on Istio,
but make an interesting service mesh example, particularly because of the
multitude of services, languages and versions for the reviews service.

## Install

### Prerequsite

You need to have [installed Istio][istio-install] before you can successfully
deploy the Bookinfo application.

### Deploy

To run the sample with Istio requires no changes to the application itself.
Instead, you simply need to configure and run the services in an Istio-enabled
environment, with Envoy sidecars injected along side each service. The resulting
deployment will look like this:

![Istio Bookinfo Architecture](https://istio.io/docs/examples/bookinfo/withistio.svg)

All of the microservices will be packaged with an Envoy sidecar that intercepts
incoming and outgoing calls for the services, providing the hooks needed to
externally control, via the Istio control plane, routing, telemetry collection,
and policy enforcement for the application as a whole.

1. The default Istio installation uses automatic sidecar injection. Label the
   namespace that will host the application with istio-injection=enabled:

```
kubectl label namespace default istio-injection=enabled
```

2. Deploy your application using the `helm install` command:

```
helm repo add evry-ace https://evry-ace.github.io/helm-charts/
helm install --name istio-bookinfo evry-ace/istio-bookinfo
```
