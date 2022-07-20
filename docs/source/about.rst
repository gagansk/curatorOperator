**About**
=========

.. _operator-curator:

Operator Curator
----------------

Operator Curator is an air-gapped infrastructure consumption analysis tool for the Red Hat OpenShift Container Platform. Curator retrieves infrastructure utilization for the OpenShift Platform using Operator koku-metrics and provides users the ability to query the infrastructure utilization based on time period, namespace, and infrastructure parameters. 

Users can generate periodic standard and custom reports on infrastructure utilization, which are optionally delivered through automated emails.  Curator also provides APIs to query the information utilization data that is stored in a database in the OpenShift cluster and it can also be used to feed data collected to any infrastructure billing system or business intelligence system. Additionally, Curator also provides administrators of the OpenShift cluster the option to back up their cluster infrastructure consumption data to S3-compatible storage.

You need to have administrator access to an OpenShift v.4.5+ cluster to deploy Operator Curator. For more information on the prerequisites, please view the Requirements section. Once deployed, all the authorized users and systems will be able to view the infrastructure utilization of OpenShift. 

.. _operator-koku-metrics:

Operator Koku-metrics
---------------------

Operator Koku-metrics can be used to obtain Red Hat OpenShift Container Platform (OCP) usage data and upload it to koku. The Koku-metrics operator utilizes Golang to collect usage data from an OCP cluster installation. You must have access to an OpenShift v.4.5+ cluster.

