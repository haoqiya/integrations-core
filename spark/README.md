# Spark Check

![Spark Graph][1]

## Overview

The Spark check collects metrics for:

- Drivers and executors: RDD blocks, memory used, disk used, duration, etc.
- RDDs: partition count, memory used, disk used
- Tasks: number of tasks active, skipped, failed, total
- Job state: number of jobs active, completed, skipped, failed

## Setup
### Installation

The Spark check is included in the [Datadog Agent][2] package, so you don't need to install anything else on your:

- Mesos master (if you're running Spark on Mesos),
- YARN ResourceManager (if you're running Spark on YARN), or
- Spark master (if you're running Standalone Spark)

### Configuration

1. Edit the `spark.d/conf.yaml` file, in the `conf.d/` folder at the root of your [Agent's configuration directory][3].
    See the [sample spark.d/conf.yaml][4] for all available configuration options:

    ```yaml
        init_config:

        instances:
          - spark_url: http://localhost:8088 # Spark master web UI
        #   spark_url: http://<Mesos_master>:5050 # Mesos master web UI
        #   spark_url: http://<YARN_ResourceManager_address>:8088 # YARN ResourceManager address

            spark_cluster_mode: spark_standalone_mode # default is spark_yarn_mode
        #   spark_cluster_mode: spark_mesos_mode
        #   spark_cluster_mode: spark_yarn_mode

            cluster_name: <CLUSTER_NAME> # required; adds a tag 'cluster_name:<CLUSTER_NAME>' to all metrics

        #   spark_pre_20_mode: true   # if you use Standalone Spark < v2.0
        #   spark_proxy_enabled: true # if you have enabled the spark UI proxy
    ```

    Set `spark_url` and `spark_cluster_mode` according to how you're running Spark.

2. [Restart the Agent][5] to start sending Spark metrics to Datadog.

### Validation

[Run the Agent's `status` subcommand][6] and look for `spark` under the Checks section.

## Data Collected
### Metrics
See [metadata.csv][7] for a list of metrics provided by this check.

### Events
The Spark check does not include any events.

### Service Checks
The Agent submits one of the following service checks, depending on how you're running Spark:

- **spark.standalone_master.can_connect**
- **spark.mesos_master.can_connect**
- **spark.application_master.can_connect**
- **spark.resource_manager.can_connect**

The checks return CRITICAL if the Agent cannot collect Spark metrics, otherwise OK.

## Troubleshooting
### Spark on AWS EMR.

To get Spark metrics if Spark is set up on AWS EMR, [use bootstrap actions][8] to install the [Datadog Agent][9] and then create the `/etc/dd-agent/conf.d/spark.yaml` configuration file with [the proper values on each EMR node][10].

## Further Reading

* [Hadoop & Spark monitoring with Datadog][11]


[1]: https://raw.githubusercontent.com/DataDog/integrations-core/master/spark/images/sparkgraph.png
[2]: https://app.datadoghq.com/account/settings#agent
[3]: https://docs.datadoghq.com/agent/faq/agent-configuration-files/#agent-configuration-directory
[4]: https://github.com/DataDog/integrations-core/blob/master/spark/datadog_checks/spark/data/conf.yaml.example
[5]: https://docs.datadoghq.com/agent/faq/agent-commands/#start-stop-restart-the-agent
[6]: https://docs.datadoghq.com/agent/faq/agent-commands/#agent-status-and-information
[7]: https://github.com/DataDog/integrations-core/blob/master/spark/metadata.csv
[8]: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-bootstrap.html
[9]: https://docs.datadoghq.com/agent
[10]: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-connect-master-node-ssh.html
[11]: https://www.datadoghq.com/blog/monitoring-spark
