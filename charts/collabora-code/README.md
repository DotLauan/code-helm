# Collabora CODE

[Collabora](https://www.collaboraoffice.com/code/) is a online office suite.

## Introduction

This chart creates a single Collabora CODE Pod to run Collabora CODE suite, for example as integration together with nextcloud. Installation is based on the docker documentation [CollaboraDocker](https://www.collaboraoffice.com/code/docker/).

For most easy integration it´s recommended to use cert-manager together with your favorite ingress controller to get a fully working, ssl-terminated Collabora CODE server.

## Prerequisites

- Kubernetes 1.9+ with Beta APIs enabled

## Installing the Chart

To install the chart with the release name `my-release`, run:

```bash
$ helm install --name my-release stable/collabora
```

This command deploys a Collabora Online Development Edition server.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

Refer to [values.yaml](values.yaml) for the full run-down on defaults. These are a mixture of Kubernetes and Collabora-related directives that map to environment variables in the [CollaboraCODEDocker](https://github.com/CollaboraOnline/Docker-CODE) Docker image.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    --set varname=true stable/collabora
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml stable/collabora
```

> **Tip**: You can use the default [values.yaml](values.yaml)

The following tables lists the configurable parameters of this chart and their default values.

| Parameter                                         | Description                                                   | Default                                                     |
| ------------------------------------------------- | ------------------------------------------------------------- | ----------------------------------------------------------- |
| `replicaCount`                                    | Number of provisioner instances to deployed                   | `1`                                                         |
| `strategy`                                        | Specifies the strategy used to replace old Pods by new ones   | `Recreate`                                                  |
| `image.repository`                                | Provisioner image                                             | `collabora/code`                                            |
| `image.tag`                                       | Version of provisioner image                                  | ``                                                   |
| `image.pullPolicy`                                | Image pull policy                                             | `IfNotPresent`                                              |
| `collabora.DONT_GEN_SSL_CERT`                     |                                                               | `true`                                                      |
| `collabora.domain`                                | Double escaped WOPI host (DEPRECATED)                         | `wopihost\\.domain`                                         |
| `collabora.aliasgroups`                           | List of objects containing domain and list of aliases (see values.yaml for exmaple)    | `[]`                                         |
| `collabora.extra_params`                          | List of params to use as env var                              | `--o:ssl.termination=true --o:ssl.enable=false`             |
| `collabora.server_name`                           | Collabora server name (single escaped)                        | `collabora\.domain`                                         |
| `collabora.password`                              | Collabora admin panel pass                                    | `examplepass`                                               |
| `collabora.username`                              | Collabora admin panel user                                    | `admin`                                                     |
| `collabora.dictionaries`                          | Collabora enabled dictionaries                                | `de_DE en_GB en_US es_ES fr_FR it nl pt_BR pt_PT ru`        |
| `ingress.enabled`                                 |                                                               | `false`                                                     |
| `ingress.annotations`                             |                                                               | `{}`                                                        |
| `ingress.paths`                                   |                                                               | `[]`                                                        |
| `ingress.hosts`                                   |                                                               | `[]`                                                        |
| `ingress.tls`                                     |                                                               | `[]`                                                        |
| `livenessProbe.enabled`                           | Turn on and off liveness probe                                | `true`                                                      |
| `livenessProbe.initialDelaySeconds`               | Delay before liveness probe is initiated                      | `30`                                                        |
| `livenessProbe.periodSeconds`                     | How often to perform the probe                                | `10`                                                        |
| `livenessProbe.timeoutSeconds`                    | When the probe times out                                      | `2`                                                         |
| `livenessProbe.successThreshold`                  | Minimum consecutive successes for the probe                   | `1`                                                         |
| `livenessProbe.failureThreshold`                  | Minimum consecutive failures for the probe                    | `3`                                                         |
| `livenessProbe.scheme`                            | Scheme for the probe                                          | `HTTP`                                                      |
| `livenessProbe.path`                              | Path for the probe                                            | `/`                                                         |
| `readinessProbe.enabled`                          | Turn on and off readiness probe                               | `true`                                                      |
| `readinessProbe.initialDelaySeconds`              | Delay before readiness probe is initiated                     | `30`                                                        |
| `readinessProbe.periodSeconds`                    | How often to perform the probe                                | `10`                                                        |
| `readinessProbe.timeoutSeconds`                   | When the probe times out                                      | `2`                                                         |
| `readinessProbe.successThreshold`                 | Minimum consecutive successes for the probe                   | `1`                                                         |
| `readinessProbe.failureThreshold`                 | Minimum consecutive failures for the probe                    | `3`                                                         |
| `readinessProbe.scheme`                           | Scheme for the probe                                          | `HTTP`                                                      |
| `readinessProbe.path`                             | Path for the probe                                            | `/`                                                         |
| `securityContext.allowPrivilegeEscalation`        | Create & use Pod Security Policy resources                    | `true`                                                      |
| `securitycontext.capabilities.add`                | Collabora needs to run with MKNOD as capabibility             | `[MKNOD]`                                                   |
| `resources`                                       | Resources required (e.g. CPU, memory)                         | `{}`                                                        |
| `nodeSelector`                                    | Node labels for pod assignment                                | `{}`                                                        |
| `affinity`                                        | Affinity settings                                             | `{}`                                                        |
| `tolerations`                                     | List of node taints to tolerate                               | `[]`                                                        |


## Persistence

There is no need for a persistent storage to run collabora code edition. All parameters in `/etc/loolwsd/loolwsd.xml` can be adjusted with using extra_params environment variable.

## Changelog

### Update to 2.3.0

Beginning with Collabora CODE docker image version `21.11.3.6` the definition of domains has changed. The values property `collabora.domain` is now deprecated, but it´s supported for backwards compatibility.

Instead of the old definition

```yaml
collabora:
  domain: nextcloud\\.domain
```

the replacement for this is

```yaml
collabora:
  aliasgroups:
    - domain: https://nextcloud.domain:443
      aliases:
        - alias1\\.domain
        - alias2\\.domain
```

If you already defined a property `collabora.domain` with a value `foo\\.bar` thie will be automatically appended to aliasgroups resulting in the following exampe:

```yaml
collabora:
  aliasgroups:
    - domain: https://foo.bar:443
```

Notice that there is noe escaped dot here anymore and we have a protocol and a port. My tests were only successful with such a defintion.