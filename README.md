# Helm Chart for Pleroma

Deploy a Pleroma instance to Kubernetes using Helm.

## Installing

`cp values.template.yaml values.yaml`

This Chart has been constructed with [docker-pleroma](https://github.com/angristan/docker-pleroma) by Angristan in mind,
and should work out of the box with it. Images are provided from container registry at [GitLab](https://gitlab.com/Tsuribori/docker-pleroma).
You can configure some Pleroma config values in `values.yaml` (you must atleast set the secret key).
All configuration parameters can be changed/added in `templates/pleroma-configmap.yaml` which will be injected into the container
(so the values used in building the Docker image can be overwritten, however this will trigger a recompilation inside the
container so it's not the most efficient, but it is convienient). The hostname will be taken from the first host
defined in the ingress so it's important to change it to your own.
By default only `1Gi` is reserved for user uploads.

Install with:

```
helm dep update
helm install pleroma .
```

Also to note is that the Bitnami Postgresql chart makes a `8Gi` PVC by default, see [here](https://github.com/bitnami/charts/tree/master/bitnami/postgresql#parameters) to view all configurable parameters related to the chart.


## External Postgresql

Set `postgresql.enabled: false` and fill `externalPostgresql` values. The
password should be stored in an existing secret referenced by
`externalPostgresql.existingSecret` and
`externalPostgresql.existingSecretPasswordKey`.

```
postgresql:
  enabled: false

externalPostgresql:
  host: example.com
  port: 5432
  user: plemora
  database: plemora
  existingSecret: plemora-auth
  existingSecretPasswordKey: porgresql-password
```

## Using with other Docker Pleroma builds

The most important values to change are under `pleromaImageOptions` in `values.yaml`. Also recompilation will fail
if `podSecurityContext` does not match.


## Upgrading

Build a new version of the Docker image and change corresponding values in `values.yaml` and
run:

`helm upgrade pleroma .`

Migrations *should* be handled automagically by Pleroma.
