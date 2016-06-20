---
title: Service Metrics Documentation
owner: London Services Enablement
---

# Overview

- [How do service metrics work?](#architecture)

<a id="architecture"></a>
## How do service metrics work?
![service-catalog-workflow](/img/service_metrics_workflow.mmd.png)

Service-metrics are colocated with the service job. It runs as a daemon process, executing a metrics generation command at regular intervals. Usually the service author will provide a binary for that purpose. In this case the command will invoke that binary.

`service-metrics` will receive the output of the metrics generation command and perform validation on the [format](author.html#output).

If validation succeeds, `service-backups` will send the metrics to the metron agent deployed on the VM. Metron forwards metrics to Doppler. Doppler can be configured to forward the mertics to a third party application such as papertrail or to the cloud contoller's traffic controller.
