# promswarm

Modernized version of [swarmprom](https://github.com/stefanprodan/swarmprom) packaged as a [nothelm](https://github.com/neuroforgede/nothelm.py/) chart.

# How to use this repository

## Requirements

- Traefik set up and running with an attachable network `traefik-public` (configurable name)
- A recent version of docker-stack-deploy https://github.com/neuroforgede/docker-stack-deploy for automatic secret rotation
- A recent version of nothelm.py https://github.com/neuroforgede/nothelm.py/ for the deployment templating

## Install in your Swarm

Setup your workdirectory with the following commands (please verify all the variables in the yaml files this generates):

```bash
export BASE_DIR="."
export WORKDIR="$(pwd)/test"
export CHART_DIR="../chart"
export START_PWD=$(pwd)

mkdir -p $WORKDIR/secrets

echo "exec nothelm run deploy --project-dir $CHART_DIR -f values.yaml -f secrets/secret_values.yaml" > $WORKDIR/setup.sh

cat > $WORKDIR/secrets/secret_values.yaml << EOF
promswarm_prometheus_teams_webhook_url: "http://some.teams.webhook.url"
promswarm_alertmanager_admin_password: "s3cr3t"
promswarm_prometheus_admin_password: "moars3cr3t"
promswarm_grafana_admin_password: "holys3cr3tyay"
EOF

cat > $WORKDIR/values.yaml << EOF
stack_name: monitoring

promswarm_monitoring_base_domain: some.base.domain

promswarm_traefik_public_network: 'traefik-public'
promswarm_monitoring_net_network_mtu: '1350'
promswarm_external_monitors_network_mtu: '1350'

promswarm_prometheus_volume_config: {}
promswarm_grafana_volume_config: {}
promswarm_alertmanager_volume_config: {}
EOF
```

After you have verified everything looks good, you can deploy by running

```bash
cd $WORKDIR
bash setup.sh
```

# Sample Screenshots

Grafana:
![grafik](https://user-images.githubusercontent.com/719760/173693628-28da52b0-0f04-4483-b52b-3c2e4aa7ab2b.png)

Prometheus:
![grafik](https://user-images.githubusercontent.com/719760/173693770-b198a832-d76d-4808-8eb5-1768097fdf35.png)

Alertmanager:
![grafik](https://user-images.githubusercontent.com/719760/173693833-e9312ebd-0a0a-43ee-a8be-3552b6f3746c.png)
