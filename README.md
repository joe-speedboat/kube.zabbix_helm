# Zabbix on K3S
* Setup Zabbix on K3S with helm
  * Values: https://github.com/zabbix-community/helm-zabbix/blob/master/charts/zabbix/README.md
* Add Backup with retention


## Setup Zabbix
```bash
HOST=mon.domain.tld

helm repo add zabbix-community https://zabbix-community.github.io/helm-zabbix
helm search repo zabbix-community/zabbix -l
export ZABBIX_CHART_VERSION='5.0.1'

helm upgrade --install zabbix zabbix-community/zabbix  \
  --dependency-update \
  --create-namespace \
  --version $ZABBIX_CHART_VERSION \
  -n monitoring \
  --set postgresql.persistence.enabled=true \
  --set postgresql.persistence.storageSize=10Gi \
  --set ingressRoute.enabled=true \
  --set ingressRoute.hostName=$HOST \
  --debug

kubectl get pv
```

## Uninstal Zabbix
```bash
helm uninstall zabbix -n monitoring
kubectl delete namespace monitoring
```

## Install Backup
```bash
for y in zabbix-backup-pvc.yml zabbix-backup-cron.yml; do echo "install $y"; kubectl apply -f $y; done
```

