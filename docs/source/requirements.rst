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

* Run koku-metrics-operator config

    .. code:: yaml
        
        #Documentation/kokumetris-cfg.yaml
        $ oc apply -f Documentation/kokumetris-cfg.yaml



**Install PostgreSQL Image**

* Need to have postgres database up and running in cluster to store all information.

* We are currently using postgres version 13.4 and config file specified `sample. <https://github.com/operate-first/curator-operator/blob/main/Documentation/config/postgres-database-cfg.yaml>`_


**Create database secrets**
  
* First, create project/namespace using below command or web console
  
        ::

        $ oc new-project <project_name>
        
* We need to create base64 encoded database secrets. To encode a string to base64 from the command-line use the below command
      
       ::
       
       $ echo -n "curator" | base64


* For creating secrets run below commmand. Sample file is `here. <https://github.com/operate-first/curator-operator/blob/main/Documentation/config/db-secret-file.yaml>`_

  
  
    .. code:: yaml

        #Documentation/db-secret-file.yaml
        $ oc apply -f Documentation/db-secret-file.yaml
