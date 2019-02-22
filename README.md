IMPORTANT NOTE: The tool is not available for generic usage as of now. This README is only a means to explain what we have built in the tool.

REST API Testing Framework
==========================

This confluence explains the various options to run automated tests to invoke various REST APIs in the target model and other areas.
All of the tests mentioned below can run against any site with configurable levels of concurrency. That makes it convenient to run the functional tests as well as

Key Features
============

1. Run REST API tests using Apache Jmeter - Same test can run against any OMC site defined in site properties or even REST endpoints outside of OMC.

2. Framework can run API invocation tests against VIP, OHS, INTERNAL and direct WLS endpoints.

3. REST API tests can be invoked using various combinations of authentication mechanisms across IC / EC and OCI environments and also all of the current PROD sites.

4. Supports OAuth

5. For simple tests with no input data, there is zero effort to write test cases. Only need to list APIs in a flat file.

6. Easy-to-use integration points for tests that need custom input data, like GET APIs with input args, POST, DELETE etc.

7. Runs the test and generate report with the histogram and other stats about the test execution.

8. Optionally, results can be uploaded to KPI repos. Baseline for the tests are set through KPI repos and SUC/DIF are generated accordingly.
Diagnostics like SQL monitor and JFR are collected in the context of the test.


Setting up the env using the test framework - From EMDIST master
·       All code changes for the test framework is merged into EMDIST master. The framework code can now be setup from your local GIT clone using the steps below.

·        
·       1. git clone git@xxxxxxxx.com:xxxx/xxxxx.git xxxxxx.latest
·        
·       2. cd emdist.latest/./tools/src/main/resources/tools/common/
·        
·       3. sh setuplocal.sh <Any location to be used as T_WORK>
·          -- This step would take about 5 mins.
·          Example: sh setuplocal.sh /scratch/restapi/t_work
 
Steps above will setup all the test artifacts in the designated T_WORK folder mentioned in the setup script.
Now you can run the REST API tests using any of the methods listed below, according to your testing needs.
Any other existing large site

You need to get the site details in the same format as mentioned in the sections below and then pass it as SITE_PROPS argument. 
SITE_PROPS files for all common sites are available in $T_WORK/large_site_props folder after the setuplocal process mentioned above.

Please note that this tool currently has a dependency on the availablity of SQL*Plus in the host where the test is being run. Without SQL*Plus, the SQL monitor and Active details report generation would not happen.
Rest of the test execution will not be impacted. We are working to eliminate this dependency.

Site properties file
Site properties file contains all the site co-ordinates required to run the REST API test. A sample site properties file is attached below for reference.
There are multiple sections in the properties file with different set of properties to exercise different features of the REST API test framework.

Click here for a sample site properties file

# Basic properties required for running API test against OHS.
SERVER_HOST=xxxxxxxxxxxxxxxx
SERVER_PORT=5560
OHS_SERVER_HOST=xxxxxxxxxxxxxxx
OHS_SERVER_PORT=5560
DEFAULT_TENANT_LIST_FILE=$T_WORK/testClasses/large_site_props/psrlarge2_v4_tenant_list.out
SITE_ID=psrlargeexa
SAAS_VERSION=1.32.1-20180625214710
SAAS_BUILD_LABEL=1.32.P4P
EXTOHS=TRUE
CONTENT_TYPE="application/json"

# Following is needed if you want to run API tests against direct WLS host.
AUTH_KEY="Basic xxxx"

# Following props are needed if you want to run the API test against internal OHS endpoints, like the tenant manager API calls.
# Not used too frequently

INTERNALOHS_HOST=xxxxxxxx
INTERNALOHS_PORT=5561
TENANT_MGR_HOST=xxxxxx
TENANT_MGR_PORT=7010
EXTOHS_HOST=h1,h2

 

GET APIs Test
 
Test to invoke a single GET REST API
 

cd remote_tools/psr/targetmodel/get_url_test
 
sh $T_WORK/testClasses/tools/restapifwk/run_get_test_pack.sh GET_URL="/urlpath/urlpath2/urlpath4?limit=5" SITE_PROPS=$PWD/psrlarge2.properties THREADS=1 GET_URL_ID=TM_GET_DATA
Note

GET_URL_ID parameter in the above command is optional if you are not using the LOAD_KPIS_TO_DB option.

 

Test to invoke a list of GET REST APIs for 2 iterations
 

sh $T_WORK/testClasses/tools/restapifwk/run_get_test_pack.sh URL_LIST=$PWD/urls.list THREADS=2 ITERATIONS=2
 

Sample URL List

TM_URL1=/urlpath/urlpath2/urlpath3/urlpath4
TM_URL2=/urlpath/urlpath2/urlpath3
 

Test to invoke GET REST APIs - direct URL to service's WLS
By default, all the tests mentioned above are designed to run against the OHS end point. But there can be cases where we need to run the tests against direct endpoints, ie. the service's WLS server:port.

sh $T_WORK/testClasses/tools/restapifwk/run_get_test_pack.sh URL_LIST=$PWD/urls.list THREADS=2 ITERATIONS=2 ACCESS_TYPE=DIRECT_WLS DIRECT_WLS_HOST=<server host> DIRECT_WLS_PORT=<server port>
 

Where do I find the results of the test run ?
After every test mentioned below, you will get the output of the test printed at the stdout in the console and also available in the following files.

Response time stats will be in a file named <URL_ID>_report.out. The file name prefix can change according to the test you are running.
Detailed report from Jmeter would be in csv files in the $T_WORK location.
SQL monitor and active detail reports if available, will be present in /scratch/em.heapdumps folder.
Click here for a sample <TEST>_report.out

Test Details
============

URL / Endpoint: /urlpath/urlpath2/urlpath4?limit=5
URL ID : TM_GET_5_DATA
Concurrent Threads: 1
Test Log - CSV : /scratch/name/restapi.twork.3/TM_GET_5_DATA_1_ITER2_.csv

 Test Log - XML : /scratch/name/restapi.twork.3/TM_GET_5_DATA_1_ITER2.csv.xml

Test Statistics
===============

1. Summary
----------

URL_ID=TM_GET_5_DATA
TIMESTAMP=1531527143225
URL=/urlpath/urlpath2/urlpath4?limit=5
TOTAL_REQUESTS=2
PASS=2
FAIL=0
MIN RESP TIME=87
MAX RESP TIME=312
AVG_TIME_IN_MILLISECONDS=199.5
PERCENTILE90=87
TOTAL_BYTES=6204
SUCCESS PERC=100
TRANSFERRED FILE SIZE=6.05859KB
TOTAL_CONCURRENT_REQUESTS=1
ITERATIONS=2

2. Histogram
--------------

0 - 500 ms , 2 (100%)
500 ms - 1 sec , 0 (0%)
1 sec - 2 secs , 0 (0%)
2 secs - 5 secs , 0 (0%)
5 secs - 10 secs , 0 (0%)
10 secs - 60 secs , 0 (0%)
1 min - 5 mins , 0 (0%)
> 5 mins , 0 (0%)
RESP_CNT_GT_2SEC=0
RESP_CNT_GT_5SEC=0
RESP_PERC_GT_2SEC=0
RESP_PERC_GT_5SEC=0
PERCENTILE_90=87
ERR_CNT_4XX=0
ERR_CNT_5XX=0
ERR_CNT_3XX=0
ERR_CNT_2XX=2
ERR_CNT_CONN_REFUSED=0
ERR_PERC_4XX=0
ERR_PERC_5XX=0
ERR_PERC_3XX=0
ERR_PERC_2XX=100
ERR_PERC_CONN_REFUSED=0
ACTUAL_THROUGHPUT_REQ_PER_MIN=0

Diagnostics
===========

1. Top 3 ECIDs by response time
-------------------------------

005SEdnsdyd7u1mLsqP5iX0004^000009w,200,87
005SEdnsJSr7u1mLsqP5iX0004^000009u,200,312

2. SQL Monitor and Active details reports.
------------------------------------------

Response time 312 is within threshold of 1000 ms. No SQL reports are collected ...

JFR Reports
===========

JFR option is not enabled in run_get_test_pack call.

run_get_test_pack.sh - Command Line Options
Option

Required/Optional

Default

Description

URL_LIST / GET_URL

required

One of these options is mandatory. URL list or the GET API end point should be provided.

THREADS

optional

"10 50 100 200"

Test will run for 4 different levels of concurrency if no option is passed ih the command line.

SITE_PROPS

required

Properties of the site where the test needs to be executed

GET_URL_ID

required when GET_URL is specified

This is the ID which is used to insert the test results into KPI repository

ACCESS_TYPE

optional

OHS

This flag needs to be set to "DIRECT_WLS" if we need to run the test against any particular WLS server.

Please note that this option is valid only when we are running tests in our internal sites and not in PROD

DIRECT_WLS_HOST

required when ACCESS_TYPE=DIRECT_WLS

DIRECT_WLS_PORT

required when ACCESS_TYPE=DIRECT_WLS

JFR

optional

Flag to enable JFR collection for the apps listed in JFR_APP_LIST

JFR_APP_LIST

required when JFR flag is set

Example:

JFR_APP_LIST="emcpdm-ods-query-app,emcpdm-tm-mgmt-app"

This list can also be added in the site properties file.

TENANT_LIST_FILE

optional

DEFAULT_TENANT_LIST in SITE_PROPS file

GET URL with dynamic variables
URL can contain variables like ${entityId}, ${BLACKOUT_ID} and so on. Currently the list of variables are limited to what is needed in the data model tests. if there is a requirement for you to pass any custom input variables other than the following, in the GET requests, please file an ER and assign to Siraj Sherriff

${entityId}
${alertId}
${tagValue}
${tagKey}
${hostMeId}
${dbMeId}

POST APIs Tests
For invoking any POST request, you need a payload to send with the POST request. There are two ways of dealing with the payload generation.

a. POST using static payload

A single static file or a list of payload files POST-ed with every request. Each record in the list will be picked by one of the concurrent threads during the execution.

 

POST a single entity in a single thread
 
Following example does a POST of metric payload files. POST_TYPE should be a uque string identifying the API being tested and should start with one of the following strings,
"TM_" , "PFSVC_", "MS_", "ITA_" , "ORCH_", "APM_", "SMA_"  or "COMP_".

Payload files are generated dynamically ahead of the test so that each request POST a unique payload. For example, POST for batch or single entity. (See the section below for dynamic content generation for few of the well-known APIs)
To generate a dynamic payload, you can use any approach that you want to follow. After generating all the unique payload files, list all those file names in a single file and pass it in the  PAYLOAD_JSON_LIST command line argument.

 

Single command to prepare payload and POST the payload
sh $T_WORK/testClasses/tools/restapifwk/run_post_test_pack.sh SITE_PROPS=$SITE_PROPS THREADS=10 LOAD_KPIS_TO_DB=[TRUE | FALSE] URL_LIST=$PWD/post_DATA.list
Sample URL list file for POST

TM_POST_BATCH_ENTITY_LOAD=/urlpath/urlpath2/urlpath4/batch#xyz.json#batchentity_payload.lst
TM_POST_SINGLE_ENTITY_LOAD=/urlpath/urlpath2/urlpath4#xyz.json#singleentity_payload.lst
THREADS is a mandatory argument and should be set to the number of concurrent threads to be run. Without this input, the test would run in pre-defined set of concurrent threads like "10 50 100 200" by default.
 

Each record in the URL list file above has 3 tokens delimited by '#'.
First token is the URL itself. URL can contain variables like ${entityId}, ${BLACKOUT_ID} and so on. Currently the list of variables are limited to what is needed in the data model tests.
if there is a requirement for you to pass any custom input variables in the POST requests, other than the ones listed below, please file an ER and assign to Siraj Sherriff

${entityId}
${alertId}
${tagValue}
${tagKey}
${hostMeId}
${dbMeId}
Second token is the payload json template. For all non-data model tests, you need to mention "notemplate" 
Third token can be either 
A static json payload file name (with .json extension). This will be the file that Jmeter will use to pick the payload jsons.
A list which contains absolute file locations which will be picked by each thread of Jmeter test. If number of threads are more than the list of files, the list wll be recycled. You can do your preferred way of automation and populate the list of files and mention the list file name in the third token of the API record in the URL list.
There are certain POST calls that does not take any payload. For such cases, you can provide the string "no_payload" in the third token.
The example above is for uploading DATA using the single and batch entity creation APIs.
How to run the full GET test suite.

Usage: sh $T_WORK/testClasses/tools/restapifwk/run_get_test_pack.sh SITE_PROPS=<site props file>, default is pod2.properties.

You can set the site properties file to any existing site.

sh $T_WORK/testClasses/tools/restapifwk/run_get_test_pack.sh SITE_PROPS=tmpsr6node.properties
 

Upload test results to HTMDB application

You can execute run_get_test_pack.sh or run_post_test_generic.sh utilities mentioned above, with an additional argument LOAD_KPIS_TO_DB=TRUE and you can see your KPIs in the following page

There is a menu option "REST API PSR RESULTS" which has a link for each area. Depending on the prefix you used for the POST_TYPE or GET_URL_ID or the prefix of the URL ids in the URL list file, the data would be displayed in the corresponding page.
ie. If your URL had "MS_" prefix, your KPIs would be in "Monitoring" page and so on.

REST API Test Diagnostics
In the GET and POST tests above, we can add the following options to collect a JFR for the duration of the test execution.

JFR=1

Optionally, you can also provide a list of apps for which the JFR needs to be collected.

JFR_APP_LIST=app1,app2

If the JFR_APP_LIST if not provided in the input, it is picked up from the URL list file or from the site properties file.
