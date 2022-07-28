API
===


**API Deployment** 

Deploy the API to Openshift


   1  Download raw Koku-Metric-Operator reports for given time frame

      .. code:: init

        oc port-forward <curator-operator-pod> 5000:8082
        curl -XGET "http://localhost:5000/download?start=2022-07-22%2003:00:00.000000&end=2022-07-23%2004:00:00.000000"

        ``start`` and ``end`` parameters will be cast to PostgreSQL timestamp. Therefore multiple formats are supported.

      Downloaded report follows similar structure of a Koku-Metrics-Operator uploaded report.
      ::

          <start>-<end>-koku-metrics.tar.gz
          ├── <start>-<end>-koku-metrics.0.csv
          ├── <start>-<end>-koku-metrics.1.csv
          ├── <start>-<end>-koku-metrics.2.csv
          └── <start>-<end>-koku-metrics.3.csv

   2  Create a sample ``Report`` by defining the parameters

      -  reportingEnd: Required, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime.
      -  reportingStart: Optional, `RFC
         3339 <https://datatracker.ietf.org/doc/html/rfc3339>`_
         Datetime.
      -  reportPeriod: Optional, String. One of Day, Week, Month. Report period N =
         1, 7, 30 days.
      -  namespace: Optional, String. Show report for namespace only. If omitted, show report for all namespace.
     
      -  metricsName:  Optional, array of string. Show report aforementioned metrics only. If not mentioned, show a report for all metrics.
         
         Valid options are:
            -  pod
            -  pod_usage_cpu_core_seconds
            -  pod_request_cpu_core_seconds
            -  pod_limit_cpu_core_seconds
            -  pod_usage_memory_byte_seconds
            -  pod_limit_memory_byte_seconds
            -  pod_request_memory_byte_seconds
            -  node_capacity_cpu_cores
            -  node_capacity_cpu_core_seconds
            -  node_capacity_memory_bytes
            -  node_capacity_memory_byte_seconds


      **Method 1**: Time frame report

      Provide parameter for both ``reportingStart`` and ``reportingEnd``. (``reportPeriod`` will be ignored if provided)

      Result report contains raw CPU and memory metrics for time frame [``reportingStart``, ``reportingEnd``) in project ``namespace`` (if provided).

      .. code:: yaml

        # config/samples/curator_v1alpha1_reportapi.yaml
        apiVersion: curator.operatefirst.io/v1alpha1
        kind: ReportApi
        namespace: report-system
        metadata:
          name: timeframe-report-sample
        spec:
          reportingStart: "2022-07-18T00:00:00Z"
          reportingEnd: "2022-07-20T00:00:00Z"  # prevents Reports targeting future time
          namespace: koku-metrics-operator
          metricsName: ["pod", "pod_usage_cpu_core_seconds"]

      **Method 2**: Standard daily, weekly, monthly report

      Provide parameter for both ``reportPeriod`` and ``reportingEnd``.

      Result report contains raw CPU and memory metrics for the past N days until reportingEnd (including reportingEnd) in project ``namespace`` (if provided).

      .. code:: yaml

        # config/samples/curator_v1alpha1_reportapi.yaml
        apiVersion: curator.operatefirst.io/v1alpha1
        kind: ReportApi
        namespace: report-system
        metadata:
          name: daily-report-sample
        spec:
          reportingEnd: "2022-07-20T00:00:00Z"  # prevents Reports targeting future time
          reportPeriod: Day
          namespace: koku-metrics-operator
          metricsName: ["pod", "pod_usage_cpu_core_seconds"]

      Create one of the two Reports above you just defined:

      .. code:: shell

          oc project curator-operator-system
          # Using project "curator-operator-system" on server ...
          oc apply -f config/samples/curator_v1alpha1_reportapi.yaml


      Access the Report by identifying Report by name and namespace it was created.
      For example, to access ``daily-report-sample`` on namespace ``curator-operator-system``:


      .. code:: shell

        oc port-forward <curator-operator pod> 5000:8082
        curl -XGET "http://localhost:5000/report?reportName=daily-report-sample&reportNamespace=curator-operator-system"
