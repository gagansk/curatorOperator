**Features**
============

Periodic Reports 
----------------

Using Curator, an administrator of OCP can generate daily, weekly and monthly infrastructure utilization reports generated in the form of a CSV file. Administrators will have the ability to set the report start time so that a daily report can be generated after 24 hours, a weekly report can be generated after 7 days and a monthly report can be generated after 30 days.

Custom reports
--------------
Using Curator, an administrator of OCP can also generate infrastructure utilization report between a start time and end time using the Curator API to generate the following reports: 
 * A report for the entire OCP cluster.
 * A report for a specific namespace. 
 * An aggregated report for a group of namespaces. 
 * A report which contains only specific parameters. 

Data pipelining 
---------------
OCP administrators can run scripts that are packaged in the Curator to stream the infrastructure utilization data collected by Curator to their database for any internal billing machine. 

Mailing services
----------------
Using Curator, OCP administrators will have the ability to automatically send reports that are generated to any email addresses. 

S3 backup (Optional)
--------------------
OCP administrators will have the ability to backup the cluster infrastructure utilization data in a S3-compatible object storage. 


