![Peloton To Garmin Banner](/images/logo/readme_banner.png?raw=true "Peloton to Garmin Banner")
[![GitHub license](https://img.shields.io/github/license/philosowaffle/peloton-to-garmin.svg)](https://github.com/philosowaffle/peloton-to-garmin/blob/master/LICENSE)
[![GitHub Release](https://img.shields.io/github/release/philosowaffle/peloton-to-garmin.svg?style=flat)]()
[![Github all releases](https://img.shields.io/github/downloads/philosowaffle/peloton-to-garmin/total.svg)](https://GitHub.com/philosowaffle/peloton-to-garmin/releases/)
<span class="badge-buymeacoffee"><a href="https://www.buymeacoffee.com/philosowaffle" title="Donate to this project using Buy Me A Coffee"><img src="https://img.shields.io/badge/buy%20me%20a%20coffee-donate-yellow.svg" alt="Buy Me A Coffee donate button" /></a></span>
# peloton-to-garmin

#### _#PelotonToGarmin_

This is a fork of https://github.com/philosowaffle/peloton-to-garmin that adds advanced configuration options that are difficult to generally support

# Kubernetes

It is also possible to run P2G in Kubernets via [Helm](https://helm.sh/). By default P2G is conmfigured with the default values from the [configuration.local.json file](helm/peloton-to-garmin/values.yaml).

In addition it is installed as a [Kubernetes Cronjob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) so it will run automatically on the scheduled interval (i.e. by default every 6 hours)

To provide your own values create an overrides_value.yaml file and provide it when installing the chart.

For example an overrides-value.yaml file might look like:
```
app:
  enablePolling: false
peloton:
  email: "fill_me_in@domain.com"
  password: "fill_me_in"
  numWorkoutsToDownload: 5
  excludeWorkoutTypes: []
garmin:
  email: "fill_me_in@domain.com"
  password: "fill_me_in"
  upload: true
config:
  # Every day at 3pm
  schedule: "0 15 * * *"
```

And installed with:
```
cd ./helm
helm install peloton-to-garmin ./peloton-to-garmin --values override.values.yaml
```
