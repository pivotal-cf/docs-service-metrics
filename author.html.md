---
title: Service Metrics Documentation
owner: London Services Enablement
---

# What is required of the Service Authors?
The service author provides a metrics collection executable, packaged as a BOSH release. The metrics collection executable collects relevant metrics from your service. The metrics are collected by the service-metrics job and forwarded to the metron agent.

The executable should be designed to be called frequently. The metrics job will call the executable every minute by default, but the interval can be changed by the operator.

The executable can accept command line arguments which might help it to produce the metrics. There is not defined interface for the arguments, leaving the service author to decide what makes more sense for their service. The format of the arguments should be documented for the service author to configure in the deployment manifest.

# Create your service metrics release
The executable should be called from the command line and optionally accept arguments.

For example:

```sh
./bin/generate-metrics --all --config /var/vcap/jobs/some-service/conf.yml
```

The return type must be a JSON array. Every element in this array is a metric; each metric sample should provide the keys: key, value, unit.

```json
[
  {
    "key": "average_delay",
    "value": 0.02,
    "unit": "ms"
  },
]

```

We recommend following [this](http://metrics20.org/spec/#units) guide for unit names, and highly encourage SI units and prefixes where appropriate.
