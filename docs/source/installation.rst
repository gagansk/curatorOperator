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

    .. code:: yaml
          
      #config/samples/curator_v1alpha1_fetchdata.yaml
      apiVersion: curator.operatefirst.io/v1alpha1
      kind: FetchData
      metadata:
        name: fetchdata-sample
      spec:
        cronjobNamespace: koku-metrics-operator
        schedule: "0 */6 * * *"  # This job will run every 6 hours. You can also use " schedule: '* * * * *' " to run this cron job instantly
        backupSrc: /tmp/koku-metrics-operator-data/upload
        unzipDir: /tmp/koku-metrics-operator-data/s3sync
        has_s3_access: <true if you want to save files in s3 bucket else, false>
        aws_access_key_id: <if has_s3_access variable is true provide the credentials, else nil>
        aws_secret_access_key: <if has_s3_access variable is true provide the credentials, else nil>
        bucket_name: <if has_s3_access variable is true provide the S3 bucket name, else nil>
        s3_host_name: <if has_s3_access variable is true provide the hostname, else nil>
        databaseCleanUp: <true if you want to database cleanup else, false>
        databaseCleanUpDuration: <if databaseCleanUp variable is true provide the databaseCleanUpDuration, else nil>

    
    If you would like to have the S3 backup or remove old records from a database features enable you need to provide values to fields

    .. code:: yaml

       # update the cronjobNamespace field to the correct namespace where you installed koku-metrics-operator
       oc apply -f config/samples/curator_v1alpha1_fetchdata.yaml


  * Report custom resource to generate the automatic report. `Sample CR <https://github.com/operate-first/curator-operator/blob/operator-additional-features/config/samples/curator_v1alpha1_report.yaml>`_


    .. code:: yaml

       #config/samples/curator_v1alpha1_report.yaml
       apiVersion: curator.operatefirst.io/v1alpha1
       kind: Report
       metadata:
         name: day-report
       spec:
         cronjobNamespace: koku-metrics-operator
         scheduleForReport: "*/5 * * * *"
         databaseName: <database-name>
         databasePassword: <password>
         databaseUser: <user-name>
         databaseHostName: <hostname or host ip>
         databasePort: <port-number>
         reportFrequency: <day,week or month>
         emailReports: <boolean value> #if this value is true then input the below values
         emailUser: <sender email>
         emailPassword: <Password>
         emailRecepients: '{"user1@redhat.com":{"cc":["user2@gmail.com"]}}'


    Also, If you would like to automatically send reports that are generated to any email addresses you need to provide values to the above fields

   .. code:: yaml
          
       # update the cronjobNamespace field to the correct namespace where you installed koku-metrics-operator
       oc apply -f config/samples/curator_v1alpha1_report.yaml


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
