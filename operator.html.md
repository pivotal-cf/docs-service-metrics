---
title: Service Metrics Documentation
owner: London Services Enablement
---

# Upload Required Releases
Upload the following releases to your BOSH director:

* your service release
* the service-metrics release

# Writing a BOSH manifest
The service manifest should have a non-errand instance group that colocates the following jobs:

* the `<service-name>` job from the service release
* the `service-metrics` job from the service metrics release
* the `metron_agent` job from the loggregator release

```yaml
instance_groups:
- name: service-metrics
  instances: 1
  jobs:
  - name: service
    release: <service-release>
  - name: service_metrics
    release: *name
  - name: metron_agent
    release: loggregator
  stemcell: trusty
  vm_type: medium
  networks:
  - name: service-metrics
  azs: [eu-west-1c]
```

The service metrics job expects some configuration to be present:
* origin: the name of the service, so it can 'mark' metrics originating from that service in the logs (mandatory)
* metrics_command: the command to generate the metrics (mandatory)
* metrics_command_args: any args to be provided to metrics_command (optional, default: [])
* execution_interval_seconds: how often the metric generation command should be ran (optional, default: 60)
* debug: turn verbose mode on/off (optional, default: false)
* monit_dependencies: an array of jobs that must be up and running before monit attempts to start the service metrics job. This is a way to define job dependencies, which are not supported by BOSH. (optional, default: [])


An example snippet is shown below:

```yaml
properties:
  service_metrics: #the service metrics release template expects the property key to be service_metrics, even though the job is called service-metrics
    origin: *name
    execution_interval_seconds: 5
    metrics_command: /bin/echo
    metrics_command_args:
    - -n
    - '[{"key":"service-dummy","value":99,"unit":"metric"}]'
    monit_dependencies: []
    nats:
      machines:
      - 10.0.1.109
      port: 4222
      user: nats
      password: uan7Aque7Dee1jiu3Ru3
    etcd:
      machines:
      - 10.0.1.110
  metron_agent:
    zone: z1
    deployment: *name
    dropsonde_incoming_port: 3457
  metron_endpoint:
    shared_secret: uan7Aque7Dee1jiu3Ru3
  loggregator:
    etcd:
      machines:
        - 10.0.1.110
  loggregator_endpoint:
    shared_secret: uan7Aque7Dee1jiu3Ru3
```

The service metrics release does not currently support the BOSH v2 manifest format, that allows job level properties.
