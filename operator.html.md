---
title: Service Metrics Documentation
owner: London Services Enablement
---

- [Upload Required Releases](#upload-required-releases)
- [Writing a BOSH manifest](#writing-bosh-manifest)
  - [Properties](#properties)

<a id="upload-required-releases"></a>
# Upload Required Releases
Upload the following releases to your BOSH director:

* your service release
* the service-metrics release
* your <service-name>-metrics release

<a id="writing-bosh-manifest"></a>
# Writing a BOSH manifest
The service manifest should have a non-errand instance group that colocates the following jobs:

* the `<service-name>` job from the service release
* the `service-metrics` job from the service metrics release
* the `<service-name>-metrics` job from <service-name>-metrics release
* the `metron_agent` job from the loggregator release

```yaml
instance_groups:
- name: service-metrics
  instances: 1
  jobs:
  - name: my-service
    release: <service-release>
  - name: service_metrics
    release: service-metrics
  - name: my-service-metrics
    release: my-service-metrics
  - name: metron_agent
    release: loggregator
  stemcell: trusty
  vm_type: medium
  networks:
  - name: service-metrics
  azs: [eu-west-1c]
```

<a id="properties"></a>
## Properties

The service metrics job expects some configuration to be present:

| field                      |       Type       |                                                                                   Description                                                                                   | Required | default value |
|:---------------------------|:----------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|---------:|--------------:|
| origin                     |      string      |                                           the name of the service, so it can 'mark' metrics originating from that service in the logs                                           |      yes |               |
| metrics_command            |      string      |                                                                       the command to generate the metrics                                                                       |      yes |               |
| metrics_command_args       | array of strings |                                                                   any args to be provided to metrics_command                                                                    |       no |            [] |
| execution_interval_seconds |       int        |                                                              how often the metric generation command should be ran                                                              |       no |            60 |
| debug                      |     boolean      |                                                                            turn verbose mode on/off                                                                             |       no |         false |
| monit_dependencies         | array of strings | an array of jobs that must be up and running before monit attempts to start the service metrics job. This is a way to define job dependencies, which are not supported by BOSH. |       no |            [] |

The metrics_command will be ran on the command line and will usually be the path to a binary provided by the service author. In this case, make sure that the path is valid on the BOSH-deployed VM and that the file has the right permissions.

If the execution of metrics_command fails for any reason (for example if the <my-service>-metrics binary exits with a non-zero exit code), the service-metrics job will not start (or will exit with 0 if it was already running) and the BOSH instance will show as `failing`. `monit` will keep trying to restart the service-metrics job. 

An example snippet is shown below:

```yaml
properties:
  service_metrics: #the service metrics release template expects the property key to be service_metrics, even though the job is called service-metrics
    origin: *name
    execution_interval_seconds: 5
    metrics_command: /bin/echo
    metrics_command_args:
    - "-n"
    - '[{"key":"service-dummy","value":99,"unit":"metric"}]'
    monit_dependencies: []
    nats:
      machines:
      - 10.0.1.109
      port: 4222
      user: nats
      password: <REPLACE_WITH_PASSWORD>
    etcd:
      machines:
      - 10.0.1.110
  metron_agent:
    zone: z1
    deployment: *name
    dropsonde_incoming_port: 3457
  metron_endpoint:
    shared_secret: <REPLACE_WITH_SECRET>
  loggregator:
    etcd:
      machines:
        - 10.0.1.110
  loggregator_endpoint:
    shared_secret: <REPLACE_WITH_SECRET>
```

The service metrics release does not currently support the BOSH v2 manifest format, that allows job level properties.
