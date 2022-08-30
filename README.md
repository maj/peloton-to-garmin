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

# Vault support

Peloton and Garmin passwords can be stored in [Vault](https://www.hashicorp.com/products/vault) and injected into the Cronjob pob via the [Vault CSI Provider](https://www.vaultproject.io/docs/platform/k8s/csi)

There is a good tutorial of how to [set this up](https://learn.hashicorp.com/tutorials/vault/kubernetes-secret-store-driver?in=vault/kubernetes) and I'm planning to script it.

But in the meantime the items that are needed are:
1. The CSI secret store driver and Vault provider installed
1. Both Peloton and Garmin passwords in Vault ```vault kv put secret/peloton-to-garmin peloton-password="<enter peloton password>" garmin-password="<enter garmin password>"```
1. A policy, role and service account that allows the application to read the secret
```
vault policy write internal-app - <<EOF
path "secret/data/peloton-to-garmin" {
  capabilities = ["read"]
}
EOF
vault write auth/kubernetes/role/peloton-to-garmin \
    bound_service_account_names=peloton-to-garmin-sa \
    bound_service_account_namespaces=default \
    policies=internal-app \
    ttl=20m

kubectl create serviceaccount peloton-to-garmin-sa
```

## Docker Builds

The image for each flavor can be built by providing its target as an argument to the Docker build command

### Console

```
DOCKER_BUILDKIT=1 docker build -f docker/Dockerfile . --build-arg VERSION=<version number> -t peloton-garmin --target final_console
```

### API

```
DOCKER_BUILDKIT=1 docker build -f docker/Dockerfile . --build-arg VERSION=<version number>  -t peloton-garmin --target final_api
```

### Web

```
DOCKER_BUILDKIT=1 docker build -f docker/Dockerfile . --build-arg VERSION=<version number>  -t peloton-garmin --target final_web
```

## Contributors

Special thanks to all the [contributors](https://github.com/philosowaffle/peloton-to-garmin/graphs/contributors) who have helped improve this project!

## Warnings

⚠️ WARNING!!! Your username and password for Peloton and Garmin Connect are stored in clear text, WHICH IS NOT SECURE. If you have concerns about storing your credentials in an unsecure file, do not use this option.

## Donate
<a href="https://www.buymeacoffee.com/philosowaffle" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/black_img.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important;box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" ></a>
