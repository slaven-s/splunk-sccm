# Splunk App for Microsoft System Center Configuration Manager (SCCM)
> System Center Configuration Manager (officially called ConfigMgr 2012 or ConfigMgr 2007 or simply ConfigMgr), formerly Systems Management Server (SMS), is a systems management software product by Microsoft for managing large groups of computers running Windows, Windows Embedded, Mac OS X, Linux or UNIX, as well as various mobile operating systems such as Windows Phone, Symbian, iOS and Android. Configuration Manager provides remote control, patch management, software distribution, operating system deployment, network access protection and hardware and software inventory.

_- [Wikipedia: System Center Configuration Manager](https://en.wikipedia.org/wiki/System_Center_Configuration_Manager)_

The Splunk App for SCCM enhances a basic SCCM app provided by Splunk by:

* providing a more streamlined app navigation
* providing consistent dashboard naming
* adding support for monitoring of SCCM's internal log files
* providing dashboards for select SCCM logs, including detailed views for internal query performance.
* adjusting colors and images to closer align with Microsoft's SCCM branding

# Requirements

## Splunk Requirements
* Splunk 6 or greater (tested on 6.2.1)
* Splunk App: DB Connect
* An domain or local account with read permissions on the SCCM MS SQL Server database.
* An index titled `sccm` (provided by `inputs.conf`, packaged in the app)
* Ability to receive events from a Windows server running SCCM

## SCCM Requirements
* An SCCM environment
* Splunk Universal Forwarder running on the same machine as SCCM, or a way to collect logs from SCCM's installation directory
* Knowledge of SCCM's installation directory (usually located at: `C:\Program Files\Microsoft Configuration Manager\`).

# Dashboard Overview

The app's navigation is broken down into four sections:

* System Center (View) - An overview/entry screen into the SCCM app.
* Config Overview (Menu) - Various dashboards displaying an overhead view of data collected from SCCM.
* Config Details (Menu) - A more detailed set of investigative dashboards generated from data collected from SCCM.
* SCCM Logs (Menu) - A set of dashboards displaying information gathered from SCCM's internal logging.

## The Database Query Log
SCCM uses an instance of Microsoft SQL Server to store the core of its configuration details. Since an
administrator is able to delegate or give access to the SQL Server DB, a large amount of external reporting
capabilities are opended up. However, navigating the schema and fully understanding all of the relationships
between tables can often be difficult.

Various people have had success using SCCM's SMSProv log to better understand what tables SCCM queries and
accesses directly when performing specific tasks, in hopes of being able to "sniff" out the executed query
and use it as a starting point for an external report.


In setups where SCCM provides a mission-critical role, placing too much load on the database could result
in poor system performance. For this purpose, the dashboard includes a "transaction" view of all
queries run against the database. Info about the queries includes:

* Table
* Number of times the query was run (count)
* Average run time for the query (Avg_Time)
* Maximum run time for the query (Max_Time)
* Total Time spent running the query over the total number of runs. (Total_Time, expressed as the calculation of: duration * count)


# Initial Configuration
_Steps below were originally provided by the Splunk-provided base application:_

1. Install pre-requisities
..* Splunk Enterprise
..* DBConnect App
2. Login to Splunk
3. Click on Settings->Access Controls->Users
4. Grant your logged in use the DBX Role to your account
5. Unzip/Untar the file in the the /opt/splunk/etc/apps directory, or the C:\Program
Files\Splunk\etc\apps directory if you’re on Windows.
6. In the sccm directory it creates, you will find a inputs.conf file in the root of the directory.
DBConnect does not recognize dmon-tail inputs from the app, so you will need to add the stanzas to your inputs.conf located in /opt/splunk/etc/apps/dbx/local or C:\Program Files\Splunk\etc\apps\dbx\local directory.
7. After adding, log back into Splunk and you will now notice an SCCM App. Click on the SCCM App.
8. Click on the Reports Tab
9. Click on Lookup Generator – Step1
10. Click on Lookup Generator – Step2
11. Click on Lookup Generator – Step 3
12. Within 15 minutes, you should start to see data populate each of the dashboards.

**Additional steps for log analysis and monitoring:**

1. Install the Splunk Universal Forwarder on a machine running SCCM.
2. Configure the forwarder to send events to your indexer(s).
3. Create an `inputs.conf` entry with the stanza noted in the section below.

`inputs.conf`:
```
[monitor://C:\Program Files\Microsoft Configuration Manager\Logs\*.log]
sourcetype = sccm_log_raw
index = sccm
```


# License
The foundation for this application package was created by Splunk 
as a starting point for the Splunk Apptitude Challenge. A license 
for this foundation was not specified.

Where specified, the modifications to this app by Rich Acosta and
Erica Feldman are licensed under the MIT License. See `LICENSE` for full details.

