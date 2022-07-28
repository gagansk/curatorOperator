=================================
**Development and testing guide**
=================================

Summary
-------

This document covers the details related to contributing to this project. Details such as modifying the code,
testing this modification, and debugging the issues will be listed in this document. If you are facing issues that are beyond the scope of this document and would like to discuss them with the Curator Team, you are more than welcome to raise an issue on our `GitHub issue tracker <https://github.com/operate-first/curator-operator/issues>`_.


Introduction
------------

In this section, let's dive into the internals of the operator which explains the crux behind its working and its components.
The curator operator runs on a distroless image which has only GO-related packages available in its core. Please refer to the architecture section to get a better understanding of the operator internals.
The operator consists of 3 different controllers, API controller, Report Controller, and FetchData controller.

API Controller - It servers the API requests made to the controller to get the reports.

Report Controller - This runs the cron job based on the configuration(Daily, Weekly, and Monthly).

FetchData Controller - Fetches the data from Koku-metrics-operator, suggested running every 6 hours.

How to contribute to this project
---------------------------------

This will serve as a kick-start guide for anyone who is willing to contribute to this project. Any changes, bug fixes or enhancements are always welcome.

Adding new Custom resources(CRs):
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Custom resources help in modifying the way our system behaves up to a certain extent. Variables such as DB connections, email accounts and
cron job timing can be set via the CRs.

New CRs can be added to the existing ``api/version/<name>_types.go`` files or by creating a new controller which creates a new ``api/version/<name>_types.go``

This document might be helpful when dealing with CR validation: `Custom Resource Validation <https://book.kubebuilder.io/reference/markers/crd-validation.html>`_

Once the new CRs are added or the existing CRs are modified make sure to run the below command to register these
CRs and create the manifests.
    
    ``make manifests``
    
    ``make generate``

These parameters can be accessed in your controller code and can be used for any logical implementation.
Check the existing code to find examples in the ``controller/*`` directory.

Modifying DB:
^^^^^^^^^^^^^

Most of the core logic for report generation is built around the DB function and tables. You may want to have a tweak at it.
All the DB-related manipulations exist in ``internal/db/psqldb.go`` there are queries that create a table and a query to create a function.

``generate_report(frequency_ text)`` function carries the logic for fetching from the DB tables and generating the reports. Making modifications to this will change the 
the logic for generating new reports.

This logic is run when the controller is set up for the first time, hence creating DB tables and functions on the attached Database. If you run into issues while deploying this code, check the debugging
section for any help.

Creating new controllers:
^^^^^^^^^^^^^^^^^^^^^^^^^

For some new additional features it might be handy to create a new controller, for this there 2 important things that have to be in place:

- Custom resource definitions (API)
- Controller logic (Controller)

As this is a generic topic, hence attaching a link on how to create a new API group and a controller: `Create a new API and Controller <https://sdk.operatorframework.io/docs/building-operators/golang/tutorial/#create-a-new-api-and-controller>`_


The containers:
^^^^^^^^^^^^^^^

Some of the logic exists inside a container that runs when we either call FetchData or the Report controllers.
This project relies on 2 containers which are below:

- FetchData: ``docker.io/surbhi0129/crd_unzip:latest``
- Email Reporter: ``docker.io/rav28/email_controller:v0.0.12``

You can always pull these containers, modify their logic and update the latest container path in the controller code.



Testing
-------

Currently, as there are no unit tests or testing frameworks it's important to test the sanity of the updated code for every PR.
Below are a few steps or tests that can be run on the project to verify that nothing breaks. 


Testing Day reports:
^^^^^^^^^^^^^^^^^^^^

Day reports are currently run at a particular time(currently at 8:05 PM EST) but for testing the changes its not practical to wait
so long, hence change the time in the ``controllers/report_controller.go``. Setting values like "\*/2 * * \*" would run the reports every 2 minutes. 

Tip: use https://crontab.guru/ to get cron job configuration.

Note: Running Day/Month/Weekly reports keeps updating the reports_human table with duplicate data, hence test it only in a controlled DB environment.

Steps to test day reports:
^^^^^^^^^^^^^^^^^^^^^^^^^^

    ``make manifests``

    ``make generate``

    ``make install``

    ``make docker-build IMG=docker.io/user/name:version``

    ``make docker-push IMG=docker.io/user/name:version``

    ``make deploy IMG=docker.io/user/name:version``

    ``oc apply -f path_to_report_yaml/curator_v1alpha1_report.yaml``

Verify the logs in the Day report container in the ``koku-metrics-operator`` namespace. Also, verify the reports_human table in the DB.

**Expected Result:** ``generate_report`` function should be called with the right frequency and the ``reports_human`` table should be populated with new data.


Database creation:
^^^^^^^^^^^^^^^^^^

The below steps can be used to verify the DB creation in the psql. Deploying the operator connects the DB and deploys the Tables and Functions
automatically. 

**Test cases:**

    Test: Create a DB with no tables and deploy the operator.
    Expected Result: All tables and routines should be created in the DB.
    
    
    Test: Create a DB with a few tables and deploy the operator. 
    
    Expected Result: Pre-existing tables will not be modified but new tables and functions will be created.
    
    
    Test: Create a DB with all the tables and no function and deploy the operator.
    
    Expected Result: No new tables will be created but only the function will be created.
    
    
    
    Test: Create a DB with all tables and a function with wrong return parameters.
    
    Expected Result: Operator will throw an error and fail the deployment as the function has the wrong return parameters.
    
    
    Test: Create a DB with all tables and correct functions and deploy the operator.
    
    Expected Result: No changes in the DB.
    
Fetch Data controller:
^^^^^^^^^^^^^^^^^^^^^^

FetchData controller has a minimal job of fetching the data from the koku-metrics operator and populating the curator DB logs tables.

**Steps:**

Run all the commands until ``make deploy`` post that runs the below command to launch the cron job.

    ``oc apply -f path_to_report_yaml/curator_v1alpha1_fetchdata.yaml``

Note: It is recommended to run this cronJob once in 6 hours as the koku-metrics-operator updates it only once in 6 hours.
But for testing, you could run it instantly.

**Expected Results:** logs_0, logs_1, and logs_2 tables will be populated with the latest data from koku-metrics-operator.

Sanity of the data generated:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Any changes to the report_human table will need some sanity testing to be done in order to verify the results.

Sanity test can be done by querying Prometheus using PromQL queries. 

Below are few sample queries which are used currently:

    ``Login to openshift console -> Administrator -> Observe -> Metrics -> Run the query``

.. list-table:: PromQL queries for Report Parameters
   :widths: 25 25
   :header-rows: 1

   * - Report Parameter
     - PromQL Query
   * - Pods CPU request - Prometheus Report (Millicore)
     - sum(sum(kube_pod_container_resource_requests{resource='cpu',namespace='<namespace>',node!='',pod!=''} * on(pod, namespace) group_left kube_pod_status_phase{phase='Running'}) without (container, instance, uid))	
   * - Pods memory request total - Prometheus Report (MB)
     - sum(sum(kube_pod_container_resource_requests{resource='memory',namespace='<namespace>',node!='',pod!=''} * on(pod, namespace) group_left kube_pod_status_phase{phase='Running'})) without (container, instance, uid) /1024 /1024	
   * - Volume storage request - Prometheus Report (GB)
     - sum(kube_persistentvolumeclaim_resource_requests_storage_bytes * on(persistentvolumeclaim, namespace) group_left(volumename) kube_persistentvolumeclaim_info{volumename != ''}) BY (namespace)
   * - Volume storage usage - Prometheus Report (GB)
     - sum(kubelet_volume_stats_used_bytes * on(persistentvolumeclaim, namespace) group_left(volumename) kube_persistentvolumeclaim_info{volumename != '', namespace = <namespace>})
   * - Volume storage request - Prometheus Report (GB)
     - sum(kubelet_volume_stats_capacity_bytes * on(persistentvolumeclaim, namespace) group_left(volumename) kube_persistentvolumeclaim_info{volumename != '', namespace = "koku-metrics-operator"})