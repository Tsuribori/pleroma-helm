# Helm Chart for Pleroma

Deploy a Pleroma instance to Kubernetes using Helm.

## Installing

`cp values.yaml.template values.yaml`

This Chart has been constructed with [docker-pleroma](https://github.com/angristan/docker-pleroma) by Angristan in mind,
and should work out of the box with it. Follow the instructions they have made for building the image (SECURITY WARNING: the 
postgresql password will be baked into the image so DO NOT PUBLISH IT TO A PUBLIC DOCKER REGISTRY!).
Most importantly the value of the postgres hostname MUST be `{{ .Release.Name }}-postgresql }}`, due
to the fact that all configs are baked in and can't be set by Kubernetes. After building change the
contents in `values.yaml` to those matching `config/secret.exs`, most importantly the database and ingress parameters.
By default only `1Gi` is reserved for user uploads.

Install with:

```
helm dep update
helm install pleroma .
```

(Note in this example the postgres hostname is `pleroma-postgresql`)

Also to note is that the Bitnami Postgresql chart makes a `8Gi` PVC by default, see [here](https://github.com/bitnami/charts/tree/master/bitnami/postgresql#parameters) to view all configurable parameters related to the chart.


## Using with other Docker Pleroma builds

The most important values to change are under `pleromaImageOptions` in `values.yaml`.


## Upgrading

Build a new version of the Docker image and change corresponding values in `values.yaml` and
run:

`helm upgrade pleroma`

Migrations *should* be handled automagically by `job-run-migration.yaml`.
