**Installation**
================================

Deploying the Operator
------------------------
Clone the curator operator `repository. <https://github.com/operate-first/curator-operator>`_


Currently, we have two versions of the curator operator.

version 1 (`production <https://github.com/operate-first/curator-operator/tree/production>`_ branch) - Curator operator with basic features (Periodic Reports, Custom reports)

version 2 (`operator-additional-features <https://github.com/operate-first/curator-operator/tree/operator-additional-features>`_ branch) - Curator with additional features (Mailing services, S3 backup)



**To build and deploy Operator you can use one of the two branches.**

First, update a project/namespace in *config/default/kustomization.yaml*. This is where we are going to deploy Curator Operator.

Before running the operator, the CRD must be registered with the Kubernetes apiserver:

      .. code:: yaml
       
       #cd curator-operator/
       #switch to production or operator-additional-features branch
       #you can use quay.io or docker.io to build and push operator image
       make install
       make docker-build docker-push IMG=quay.io/<user-name>/<image-name>
       make deploy IMG=quay.io/<user-name>/<image-name>



Undeploy the Operator
------------------------

      .. code:: yaml
          
          #cd curator-operator/
          make undeploy

The above command will delete everything including the project.
