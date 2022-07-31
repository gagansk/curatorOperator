**Installation**
================================

Deploying the Operator
------------------------
Clone the curator operator `repository. <https://github.com/operate-first/curator-operator>`_


Currently, we have two versions of the curator operator.

v0.0.1 - Curator operator with basic features (Periodic Reports, Custom reports)

v0.0.2 - Curator with additional features (Mailing services, S3 backup)

============
TODO NEED TO BUILD IMAGES
============

**To deploy the operator you can use one of the two above images.**

      .. code:: yaml
          
          #cd curator-operator/
          make deploy IMG=quay.io/operate-first/curator-operator



**You can build and deploy Operator on your own**

First, create the *curator-operator-system* project. This is where we are going to deploy Curator Operator.

Before running the operator, the CRD must be registered with the Kubernetes apiserver:

      .. code:: yaml
       
       #cd curator-operator/
       make manifests
       make generate
       make install
       make docker-build docker-push IMG=quay.io/<user-name>/<image-name>
       make deploy IMG=quay.io/<user-name>/<image-name>



Undeploy the Operator
------------------------

      .. code:: yaml
          
          #cd curator-operator/
          make undeploy

The above command will delete everything including the project.



