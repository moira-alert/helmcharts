# Moira

## Topology configuration parameters

### Moira Filter
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.filter.name` | Moira Filter container name | `moira-filter`
`topology.filter.image` | Moira Filter container image | `moira/filter:latest`
`topology.filter.replicas` | Moira Filter container replicas number | `3`

### Moira Checker
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.checker.name` | Moira Checker container name | `moira-checker`
`topology.checker.image` | Moira Checker container image | `moira/checker:latest`
`topology.checker.replicas` | Moira Checker container replicas number | `3`

### Moira Notifier
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.notifier.name` | Moira Notifier container name | `moira-notifier`
`topology.notifier.image` | Moira Notifier container image | `moira/notifier:latest`
`topology.notifier.replicas` | Moira Notifier container replicas number | `3`

### Moira API
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.api.name` | Moira API container name | `moira-api`
`topology.api.image` | Moira API container image | `moira/api:latest`
`topology.api.replicas` | Moira API container replicas number | `3`

### Moira Web
Parameter | Description | Default value
--------- | ----------- | -------------
`topology.web.name` | Moira Web container name | `moira-web`
`topology.web.image` | Moira Web container image | `moira/web2:latest`
`topology.web.replicas` | Moira Web container replicas number | `3`

## Ingress configuration parameters

Parameter | Description | Default value
--------- | ----------- | -------------
`ingress.enabled` | Ingress enabled | `True`
`ingress.host` | Ingress host | `moira`

## Microservices configuration parameters

### Moira Filter
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.filter.listen` | Metrics listener uri | `:2003`
`microservices.filter.retentionConfig` | Retentions config file path | `/etc/moira/storage-schemas.conf`
`microservices.filter.cacheCapacity` | Number of metrics to cache before checking them | `10`
`microservices.filter.maxParallelMatches` | Number of threads to match incoming graphite-metrics | `0`

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
`microservices.notifier.senders` | List of enabled senders | `[]`
`microservices.notifier.moiraSelfState.enabled` | If true, self state monitor will be enabled | `False`
`microservices.notifier.moiraSelfState.remoteTriggersEnabled` | If true, Moira selfstate will check remote triggers checker works properly | `False`
`microservices.notifier.moiraSelfState.redisDisconnectDelay` | Max Redis disconnect delay | `60s`
`microservices.notifier.moiraSelfState.lastMetricReceivedDelay` | Max Filter metrics receive delay | `120s`
`microservices.notifier.moiraSelfState.lastCheckDelay` | Max Checker checks perform delay for local triggers | `120s`
`microservices.notifier.moiraSelfState.lastRemoteCheckDelay` | Max Checker checks perform delay for remote triggers | `300s`
`microservices.notifier.moiraSelfState.noticeInterval` | Self state monitor alerting interval | `300s`
`microservices.notifier.moiraSelfState.contacts` | List of Moira administrator contacts | `[]`

### Moira API
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.api.listen` | API local network address | `:8081`
`microservices.api.enableCORS` | If true, CORS for cross-domain requests will be enabled | `False`

### Moira Web
Parameter | Description | Default value
--------- | ----------- | -------------
`microservices.web.supportEmail` | Moira administrator email address | ``
`microservices.web.contacts` | List of contact types that are allowed to create using Web UI | `[]`

## Datasources configuration parameters

### Local (Redis)
Parameter | Description | Default value
--------- | ----------- | -------------
`datasources.redis.masterName` | Sentinel master name | ``
`datasources.redis.sentinelAddrs` | Sentinel address list | ``
`datasources.redis.host` | Node ip-address or host name | ``
`datasources.redis.port` | Node port | ``
`datasources.redis.db` | Database id | `0`
`datasources.redis.connectionLimit` | Redis client connection pool size | `512`

### Remote (Graphite)
Parameter | Description | Default value
--------- | ----------- | -------------
`datasources.remote.enabled` | If true, remote datasource will be enabled | `False`
`datasources.remote.URL` | URL of Graphite HTTP API | `https://localhost/carbonapi/render`
`datasources.remote.user` | Auth username (Basic-auth) | ``
`datasources.remote.password` | Auth password (Basic-auth) | ``
`datasources.remote.checkInterval` | Min period to perform triggers re-check | `60s`
`datasources.remote.timeOut` | Max timeout for HTTP-request made to Graphite HTTP API | `60s`

## Monitoring configuration parameters

### Graphite metrics sender
Parameter | Description | Default value
--------- | ----------- | -------------
`monitoring.graphite.enabled` | If true, graphite sender will be enabled | `False`
`monitoring.graphite.runtimeStats` | If true, runtime stats will be captured and sent to graphite | `False`
`monitoring.graphite.URI` | Graphite relay URI | `localhost:2003`
`monitoring.graphite.prefix` | Metrics prefix | `DevOps.Moira.{hostname}`
`monitoring.graphite.interval` | Metrics sending interval | `60s`

### Log sender
Parameter | Description | Default value
--------- | ----------- | -------------
`monitoring.logger.logLevel` | Log level | `info`
`monitoring.logger.logFile` | Log file path | `stdout`

### Profiling server
Parameter | Description | Default value
--------- | ----------- | -------------
`monitoring.pprof.listen` | Profiling server local network address | ``