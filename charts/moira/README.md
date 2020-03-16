# Moira

Moira is a real-time alerting tool, based on [Graphite](https://graphite.readthedocs.io) data.

# Prerequisites

 - Kubernetes 1.35 with Beta APIs enabled
 - Redis 3.2+

# Installation

Add charts repo:

```console
helm repo add moiracharts https://moira-alert.github.io/helmcharts
```

## Single chart installation

Simply pass Redis host, port parameters to connect to a single redis node:

```console
$ helm install moiracharts/moira --name moira \
    - set datasources.redis.host="redis" \
    - set datasources.redis.port="6379"
```

or use masterName and sentinelAddrs to interact with Redis sentinel setup:

```console
$ helm install moiracharts/moira --name moira \
    - set datasources.redis.masterName="redisMaster" \
    - set datasources.redis.sentinelAddrs="redisSentinel1:26379,redisSentinel2:2369,redisSentinel3:2369"
```

## As a part of Graphite installation

 - Add chart as a dependency to [requirements.yaml](https://github.com/helm/helm/blob/master/docs/helm/helm_dependency.md)

```yaml
dependencies:
  - name: moira
    condition: moira.enabled
    version: 2.6.0
    repository: "https://moira-alert.github.io/helmcharts"
```

 - Pass contents of your `storage-schemas.conf` to `moira.microservices.filter.retentionConfig`
 - Specify `moira.datasources.remote` section parameters if you want to use [Remote Triggers](https://moira.readthedocs.io/en/latest/user_guide/advanced.html#data-source) feature

# Configuration

## Topology configuration parameters

### Moira Filter
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.filter.name` | Moira Filter container name | `moira-filter`
`topology.filter.image` | Moira Filter container image | `moira/filter:2.6.0.0`
`topology.filter.replicas` | Moira Filter container replicas number | `1`

### Moira Checker
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.checker.name` | Moira Checker container name | `moira-checker`
`topology.checker.image` | Moira Checker container image | `moira/checker:2.6.0.0`
`topology.checker.replicas` | Moira Checker container replicas number | `1`

### Moira Notifier
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.notifier.name` | Moira Notifier container name | `moira-notifier`
`topology.notifier.image` | Moira Notifier container image | `moira/notifier:2.6.0.0`
`topology.notifier.replicas` | Moira Notifier container replicas number | `1`

### Moira API
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.api.name` | Moira API container name | `moira-api`
`topology.api.image` | Moira API container image | `moira/api:2.6.0.0`
`topology.api.replicas` | Moira API container replicas number | `1`

### Moira Web
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.web.name` | Moira Web container name | `moira-web`
`topology.web.image` | Moira Web container image | `moira/web2:2.6.0.0`
`topology.web.replicas` | Moira Web container replicas number | `1`

## Ingress configuration parameters

Parameter | Description | Default value
--------- | ----------- | -------------
`ingress.enabled` | Ingress enabled | `true`
`ingress.host` | Ingress host | `moira`

## Microservices configuration parameters

### Moira Filter
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.filter.listen` | Metrics listener uri | `:2003`
`microservices.filter.retentionConfig` | Retentions config file content | `Default schema`
`microservices.filter.cacheCapacity` | Number of metrics to cache before checking them | `10`
`microservices.filter.maxParallelMatches` | Number of threads to match incoming graphite-metrics | `0`
`microservices.filter.patternsUpdatePeriod` | Period in which patterns will be reloaded from Redis | `1s`

### Moira Checker
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.checker.nodataCheckInterval` | Period for every trigger to perform forced check on | `60s`
`microservices.checker.checkInterval` | Min period to perform active triggers re-check | `10s`
`microservices.checker.lazyTriggersCheckInterval` | Min period to perform inactive triggers re-check | `10m`
`microservices.checker.metricsTTL` | Time interval to store metrics | `3h`
`microservices.checker.stopCheckingInterval` | Period for every trigger to cancel forced check if no metrics were received | `30s`
`microservices.checker.maxParallelChecks` | Max parallel checks number for local triggers | `0`
`microservices.checker.maxParallelRemoteChecks` | Max parallel checks number for remote triggers  | `0`

### Moira Notifier
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.notifier.senderTimeout` | Soft timeout to start retrying to send notification after single failed attempt | `10s`
`microservices.notifier.resendingTimeout` | Hard timeout to stop retrying to send notification after multiple failed attempts | `1:00`
`microservices.notifier.frontURI` | Web-UI uri prefix for trigger links in notifications | `http://localhost`
`microservices.notifier.timezone` | Timezone to use to convert ticks | `UTC`
`microservices.notifier.datetimeFormat` | Format for email sender | `15:04 02.01.2006`
`microservices.notifier.senders` | Enabled senders | `{}`
`microservices.notifier.moiraSelfState.enabled` | If true, self state monitor will be enabled | `false`
`microservices.notifier.moiraSelfState.remoteTriggersEnabled` | If true, Moira selfstate will check remote triggers checker works properly | `false`
`microservices.notifier.moiraSelfState.redisDisconnectDelay` | Max Redis disconnect delay | `60s`
`microservices.notifier.moiraSelfState.lastMetricReceivedDelay` | Max Filter metrics receive delay | `120s`
`microservices.notifier.moiraSelfState.lastCheckDelay` | Max Checker checks perform delay for local triggers | `120s`
`microservices.notifier.moiraSelfState.lastRemoteCheckDelay` | Max Checker checks perform delay for remote triggers | `300s`
`microservices.notifier.moiraSelfState.noticeInterval` | Self state monitor alerting interval | `300s`
`microservices.notifier.moiraSelfState.contacts` | Moira administrator contacts | `{}`

### Moira API
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.api.listen` | API local network address | `:8081`
`microservices.api.enableCORS` | If true, CORS for cross-domain requests will be enabled | `false`

### Moira Web
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.web.supportEmail` | Moira administrator email address | `""`
`microservices.web.contacts` | Contact types that are allowed to create by using Web UI | `{}`

## Datasources configuration parameters

### Local (Redis)
Parameter | Description | Default value
--------- | ----------- | -------------
`datasources.redis.masterName` | Sentinel master name | `""`
`datasources.redis.sentinelAddrs` | Sentinel address list | `""`
`datasources.redis.host` | Node ip-address or host name | `""`
`datasources.redis.port` | Node port | `""`
`datasources.redis.db` | Database id | `0`
`datasources.redis.connectionLimit` | Redis client connection pool size | `512`
`datasources.redis.allowSlaveReads` | Allow reading data from slave replicas | `true`

### Remote (Graphite)
Parameter | Description | Default value
--------- | ----------- | -------------
`datasources.remote.enabled` | If true, remote datasource will be enabled | `false`
`datasources.remote.url` | URL of Graphite HTTP API | `https://localhost/carbonapi/render`
`datasources.remote.user` | Auth username (Basic-auth) | `""`
`datasources.remote.password` | Auth password (Basic-auth) | `""`
`datasources.remote.checkInterval` | Min period to perform triggers re-check | `60s`
`datasources.remote.timeout` | Max timeout for HTTP-request made to Graphite HTTP API | `60s`

## Telemetry configuration parameters
Parameter | Description | Default value
--------- | ----------- | -------------
`telemetry.listen` | Telemetry listener local network address | `""`

### Graphite metrics sender
Parameter | Description | Default value
--------- | ----------- | -------------
`telemetry.graphite.enabled` | If true, graphite sender will be enabled | `false`
`telemetry.graphite.runtimeStats` | If true, runtime stats will be captured and sent to graphite | `false`
`telemetry.graphite.uri` | Graphite relay URI | `localhost:2003`
`telemetry.graphite.prefix` | Metrics prefix | `DevOps.Moira.{hostname}`
`telemetry.graphite.interval` | Metrics sending interval | `60s`

### Log sender
Parameter | Description | Default value
--------- | ----------- | -------------
`telemetry.logger.logLevel` | Log level | `info`
`telemetry.logger.logFile` | Log file path | `stdout`

### Profiling server
Parameter | Description | Default value
--------- | ----------- | -------------
`telemetry.pprof.enabled` | If true, profiling server will be enabled | `false`