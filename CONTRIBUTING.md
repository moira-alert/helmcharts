# Contributing

To add new or update existing helm chart:

 - Place chart files into `charts/chartName` directory
 - Execute `helm package charts/chartName`
 - Move created `chartName-version.tgz` into `docs` directory
 - Execute `helm repo index docs --url https://moira-alert.github.io/helmcharts`
 - Push changes to GitHub