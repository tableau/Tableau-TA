TA-Tableau
==========

[![Community Supported](https://img.shields.io/badge/Support%20Level-Community%20Supported-457387.svg)](https://www.tableau.com/support-levels-it-and-developer-tools)

**Release:** v0.1

This is a Splunk add-on for Tableau Server logs.  
This TA allows you to ingest and search Tableau Logs directly in Splunk.
Ingest has been tested (and included) for Desktop, Prep, and Bridge but little work has been done to utilize those inputs.

This has been tested with Splunk 8.0 and above, but should also support older versions.

Requirements
------------

In order to use the Tomcat logs, install the Splunk Add-on for Tomcat from Splunk. Tableau logs also use apache:access 
and log4j which are Splunk pretrained sourcetypes. 


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

* Remove the inputs.conf file
* Rename client_inputs.conf to inputs.conf
* Check that the file paths for the Monitor stanzas match your environment
* Deploy to a Universal Splunk Forwarder on Tableau Desktop

### Tableau Server ###

* Remove the client_inputs.conf
* Deploy the inputs.conf to Tableau Server's running the Universal Forwarder
* The Monitor paths for inputs.conf use the Environment Variable set by Tableau Server

Single Splunk System:

 * Download the addon
 * Place the TA-Tableau in the $SPLUNK_HOME/etc/apps directory
 * Restart Splunk Enterprise to load the configs

Splunk Distributed Enterprise deployment

Search Head Clusters

 * Remove the inputs.conf files
 * Deploy the TA-Tableau folder in $SPLUNK_HOME/etc/apps (or via search head deployer)

Indexers and Indexer Clusters

 * Remove the inputs.conf files
 * Restart the indexer or apply bundle from the cluster master

Universal Forwarders

 * Place the TA-Tableau in the $SPLUNK_HOME/etc/apps directory on your forwarder (or use a deployment server)
 * Restart the forwarder
 * Check the inputs.conf to match your environment
 * Add a default setting for the index target at the top of the file if desired.

Heavy Forwarders

 * Install the TA-Tableau in the $SPLUNK_HOME/etc/apps  directory
 * Remove the inputs.conf files
 * Restart the heavy forwarder


Sourcetypes
-----------

This addon supports the following Sourcetypes:

### apache:access ###
Data processed by the Apache server, the naming follows Splunk pretrained defaults.

### log4j ###
Component runtime logs in log4j format.  This is a pretrained sourcetype supported by Splunk

### tomcat:access:log ###
Logs in the Tomcat format. Use the Splunk Add-on for Tomcat.

### tomcat:runtime:log ###
Runtime log for Tomcat. Use the Splunk Add-on for Tomcat.

### tts:elastic ###
Logs from Elastic Server.

### tts:metrics ###
Tableau Metrics logs.

### tts:native ###
nativeAPI logs, the bulk of the data is in these logs. JSON format.

### tts:oauth ###
oauth client logs (generally passthrough to data sources).  May not be used in your installation.

### tts:pgsql ###
PostgreSQL's CSV logging. 
Installations with an external repository will need additional work to ingest and parse those logs.

### tts:stdout ###
Component start logs.  Rarely contain timestamps so you may need to localize with _indextime

### tts:unknown ###
New logs that do not fit into an existing sourcetype. 
Can be used to rapidly include unhandled logs until a proper sourcetype is defined.  
Will be unused in normal circumstances.

### tts:yaml ###
Logs written in YAML format.

Search Time Extractions
-----------------------

Included in this TA are some Search Time Extractions for props.conf to be installed on the Search Heads.

* processname:

Distingusihing various Tableau services from one aother is best handled via the source path. A regex based on the source path
will give you the processname of of the job, such as "hyper", "vizqlserver", or "backgrounder" etc.

* BGJobID:

This extraction will give you the BGJobID from the logs. 

  eg: kill_long_running_active_transactions, enqueue_data_alerts, sos_reconcile, etc.

* BGJobOutcome:

This extraction tells you if your job was a SUCCESS or ERROR.

* BGJobType:

This extraction reports the type of the job that was run. 
  eg: enqueue_data_alerts,	kill_long_running_active_transactions, materialized_views_query_store_persist, etc.

Future
------

Planned features:
 * Map Logs to Common Information Model (CIM) 
 * Include components for a Splunk SA to enhance searching the logs 

Contributors
------------

 * George Walford <gwalford@salesforce.com>
 * Ian Stangl <istangl@salesforce.com>
