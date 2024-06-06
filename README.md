# LibreSpeed Speedtest Helm Chart

This is a Helm chart for [LibreSpeed Speedtest](https://github.com/librespeed/speedtest). The
chart allows for a standalone deployment of Speedtest (dual & frontend/backend modes are not
currently supported by the chart). Additionally, the chart can be configured to persist data.

When persistence is enabled, this is deployed as a StatefulSet, otherwise as a Deployment.

For more information on LibreSpeed Speedtest, review its GitHub repository.

## Enabling telemetry

To enable telemetry, the underlying container image requires that an email address be specified.
The chart will fail to deploy if one is not provided.

```yaml
speedtest:
  telemetry:
    enabled: true
    email: example@example.com
```

Note that the default deployment configuration is to enable telemetry.

## Disabling data persistence

The container image stores the persistence data in SQLite. To support this, a volume is used to
store the database. This can be disabled by setting `speedtest.persistData` to false:

```yaml
speedtest:
  persistData: false
```

In this configuration if telemetry is enabled that data will be lost every time the pod/container
restarts.

>! Note: Additional replicas are not support when `speedtest.persistData` is set to `true` due to
> the underlying SQLite database.

## Configuring an `ipinfo.io` API key

If you expect a large volume of requests, you may need to configure an API key for `ipinfo.io`
This can be done by creating a secret and specifying `speedtest.ipinfo.apiKeySecretName`. The key
within the secret that holds the API key should be specified as `speedtest.ipinfo.apiKeySecretKey`.

```yaml
speedtest:
  ipinfo:
    apiKeySecretName: ipinfo-api-key
    apiKeySecretKey: api-key
```

## Configuring access to historical statistic

Access to historical statistics is gated behind a password-protected page. The password is configurable
via a secret. Create a plain generic secret and set its name in
`speedtest.telemetry.statsPasswordSecretName` while putting the key of the field that contains the
password in `speedtest.telemtry.statsPasswordSecretKey`.

```yaml
speedtest:
  telemetry:
    statsPasswordSecretName: librespeed-stats
    statsPasswordSecretKey: password
```

## Setting the image tag

It seems that the GitHub packages registry for the image may un-tag older images. If the image fails
to pull, update `image.tag`:

```yaml
image:
  tag: latest
```
