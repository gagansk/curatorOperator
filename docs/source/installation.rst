**Installation and maintenance**
================================

Install Operator Curator
------------------------

As a cluster administrator, you can install the Operator Curator from OperatorHub using the OpenShift Container Platform web console or CLI. Subscribing an Operator to one or more namespaces makes the Operator available to developers on your cluster.

Update Operator Curator
-----------------------

The subscription of an installed Operator Curator specifies an update channel, which is used to track and receive updates for the Operator. To upgrade the Operator to start tracking and receiving updates from a newer channel, you can change the update channel in the subscription.

If the approval strategy in the subscription is set to “Automatic”, the upgrade process initiates as soon as a new Curator version is available in the selected channel. If the approval strategy is set to Manual, you must manually approve pending upgrades.

Delete Operator Curator 
-----------------------
Cluster administrators can delete installed Operator Curator from a selected namespace by using the web console in the Operator Lifecycle Manager (OLM) on your OpenShift Container Platform cluster or through CLI. 


