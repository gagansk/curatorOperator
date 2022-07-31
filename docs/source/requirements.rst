Requirements
============

To run Curator Operator, the users are expected to meet the prerequisites listed below.

Prerequisite
-------------

**Admin access to OpenShift 4.5+**

**Operator-SDK 1.17+ and Go version 1.17+**


**Install Koku Metrics Operator**

* You need to install koku-metrics-operator either via operator-hub or command line.

* We are currently using koku-metrics-operator version 1.1.7 with koku-metric config specified `here. <https://github.com/operate-first/curator-operator/blob/main/Documentation/config/kokumetris-cfg.yaml>`_


**Install PostgreSQL Image**

* Need to have postgres database up and running in cluster to store all information.

* We are currently using postgres version 10.X and config file specified `sample. <https://github.com/operate-first/curator-operator/blob/main/Documentation/config/postgres-database-cfg.yaml>`_


**Create database secrets**

* We need to create database secrets. For that you can run below commmand. Sample file is `here. <https://github.com/operate-first/curator-operator/blob/main/Documentation/config/db-secret-file.yaml>`_

    .. code:: yaml

        #Documentation/db-secret-file.yaml
        oc apply -f Documentation/db-secret-file.yaml

