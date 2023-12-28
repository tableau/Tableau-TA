TA-Tableau
==========
[![Community Supported](https://img.shields.io/badge/Support%20Level-Community%20Supported-457387.svg)](https://www.tableau.com/support-levels-it-and-developer-tools)

**Release:** v1.0

This is a Splunk add-on for Tableau Server logs.  
This TA allows you to ingest and search Tableau Logs directly in Splunk.
Ingest has been tested (and included) for Desktop, Prep, and Bridge but little work has been done to utilize those inputs.

This has been tested with Splunk 8.0 and above, but should also support older versions.

Requirements
------------

There are several dependencies.  Please reference requirements.txt.


Installation
------------

Install the Splunk Add-on for Tableau:

Where to install this add-on

This add-on can be safely installed on all componets of a Splunk Distributed Deployment.

 Splunk Instance Type | Supported | Required | Comments
----------------------|----------------------|----------------------|----------------------
Search Heads | Yes | Conditional | Required if you want to use the field extractions in search for props and transforms
Indexers | Yes | Yes | Required to properly format the data and timestamps 
Heavy Forwarders | Yes | No | The props.conf can be used on Heavy Forwarders
Universal Forwarders | Yes | Yes | The inputs.conf is specifically designed for the Universal Forwarders


 Splunk Instance Type | Supported |  Comments
----------------------|----------------------|----------------------
Search Head Cluster | Yes | Required if you want to use the field extractions at search time
Indexer Clusters | Yes | Required to properly format the data and timestamps, deploy from your Cluster Master
Deployment Server | Yes | Supported for deploying the add-on to multiple clients

Installation:

### Tableau Desktop ###

* For Tableu Desktop, use the Tableau_Clients app in the deployment-clients directory
* Check that the file paths for the Monitor stanzas match your environment
* Place the app on your deployment server, or locally if single use.

### Tableau Windows Server ###

* Use the Tableau_Server_Windows app under the deployment-apps directory
* Install on a deployment server, or locally for a single instance.
* The Monitor paths for inputs.conf use the Environment Variable set by Tableau Server

Single Splunk System:

 * Download the addon
 * Place the TA-Tableau from the master-apps directoy in the $SPLUNK_HOME/etc/apps directory
 * Place the contents from the shcluster Tableau_TA into the same directory as above
 * Restart Splunk Enterprise to load the configs

Splunk Distributed Enterprise deployment 

Search Head Clusters

 * Place the Tableau_TA file from shcluster in the $SPLUNK_HOME/etc/shcluster/apps directory
 * Apply the new bundle

Indexers and Indexer Clusters

 * Install the contents of the master-apps Tableau_TA on your cluster master, or directly into the apps of the indexers
 * Restart the indexer or apply bundle from the cluster master

Universal Forwarders

 * Use the appropriate Tableau_Clients or Tableau_Server_Windows in the $SPLUNK_HOME/etc/apps directory on your forwarder (or use a deployment server)
 * Check the inputs.conf to match your environment
 * Add a default setting for the index target at the top of the file if desired
 * Restart the Forwarder

Heavy Forwarders

 * Install the components from master-apps Tableau_TA in the $SPLUNK_HOME/etc/apps  directory
 * Restart the heavy forwarder


Sourcetypes
-----------

The primary sourcetypes for this app are:

### apache:access ###
Inbound requests to Tableau Server hostnames.  Includes some internal traffic (heartbeats, dataserver, etc)

### apache_error ###
Error logs for web server component.

### log4j ###
Component runtime logs

### tts:native ###
View rendering and data handling, comprising the majority of data volume.  JSON format.

### tts:hyper ###
Variant of tts:native used by the Hyper data engine

### tts:pgsql ###
PostgreSQL's CSV logging.  Installations with an external repository will need additional work to ingest and parse those logs.

### tomcat:access:log ###
Cross-process traffic

### tomcat:runtime:log ###
Runtime log for Tomcat. Use the Splunk Add-on for Tomcat.

### tts:elastic ###
Logs from Elastic Server.

### tts:metrics ###
Tableau Metrics logs.

### tts:oauth ###
oauth client logs (generally passthrough to data sources).  May not be used in your installation.

### tts:redis ###
Cache server logging

### tts:stdout ###
Component start logs.  Rarely contain timestamps so you may need to localize with _indextime

### tts:tablicsrv ###
License service log

### tts:xml ###
Primarily configuration files.  Mostly unstructured.

### tts:yaml ###
Primarily configuration files.  Mostly unstructured.

Search Time Extractions
-----------------------

A partial list of Search Time Extractions for props.conf to be installed on the Search Heads.

### File Filtering ###
The following extractions appear as components of source in the order base_directory*processname*base_filename.  The extractions may be used for human convenience, or as statistical outputs:

* base_directory:
The root directory that Tableau Server is installed under.

* processname:
This distinguishes Tableau components, such as "hyper", "vizqlserver", or "backgrounder" etc.

* base_filename:
The specific file an event was drawn from

### General ###

* sev:
Log level (severity).  Unified name for both Log4J and tts:native files

* req:
Corresponds to a request ID from apache:access

* sess:
Pertaining to a vizQL session (typically a single workbook load or extract refresh)

* http_status_code:
Identifies the code returned for a given request.  Appears in apache:access as well as some log4j files.

* wb:
Workbook name.  Note that the format will be similar to what appears in URL rather than the fully punctuated display name


### Background Jobs ###

* BGJobID:
This correlates with the JobID from admin views and will be found in the log4j sourcetype. 

* BGJobOutcome:
This extraction tells you if your job was a SUCCESS or ERROR.

* BGJobType:
This extraction reports the type of the job that was run. 
  eg: enqueue_data_alerts,	kill_long_running_active_transactions, materialized_views_query_store_persist, etc.

Contributors
------------

 * George Walford <gwalford@salesforce.com>
 * Ian Stangl <istangl@salesforce.com>
