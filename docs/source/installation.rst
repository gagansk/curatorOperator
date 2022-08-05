**Installation**
================================

Deploying the Operator
------------------------
1. **Clone the operator curator** `repository. <https://github.com/operate-first/curator-operator>`_


   Currently, we have two versions of the curator operator.

   version 1 (`production <https://github.com/operate-first/curator-operator/tree/production>`_ branch) - Curator operator with basic features (Periodic Reports, Custom reports)

   version 2 (`operator-additional-features <https://github.com/operate-first/curator-operator/tree/operator-additional-features>`_ branch) - Curator with additional features (Mailing services, S3 backup Optional)



2. **To build and deploy Operator you can use one of the two branches.**

   First, update a project/namespace in *config/default/kustomization.yaml*. This is where we are going to deploy Curator Operator.

   Before running the operator, the CRD must be registered with the Kubernetes apiserver:

      .. code:: yaml
       
       #cd curator-operator/
       #switch to production or operator-additional-features branch
       #you can use quay.io or docker.io to build and push operator image
       make install
       make docker-build docker-push IMG=quay.io/<user-name>/<image-name>
       make deploy IMG=quay.io/<user-name>/<image-name>

   The operator curator is running but is not doing any work. We need to create a CR.

3. **Create below two custom resources**
 
* FetchData custom resource to fetch data from koku-metrics-operator. `Sample CR <https://github.com/operate-first/curator-operator/blob/operator-additional-features/config/samples/curator_v1alpha1_fetchdata.yaml>`_

* Report custom resource to generate the automatic report. `Sample CR <https://github.com/operate-first/curator-operator/blob/operator-additional-features/config/samples/curator_v1alpha1_report.yaml>`_
  
   .. code:: yaml
          
       # update the cronjobNamespace field to the correct namespace where you installed koku-metrics-operator
       oc apply -f <cr-file-path>


Uninstall a CustomResourceDefinition
----------------

      .. code:: yaml
          
          #cd curator-operator/
          make uninstall

   When you uninstall a CRD, the server will uninstall the RESTful API endpoint and delete all custom objects stored in it.


Undeploy the Operator
------------------------

      .. code:: yaml
          
          #cd curator-operator/
          make undeploy

   The above command will delete everything including the project.
