# Splunk App for Microsoft System Center Configuration Manager (SCCM)
> System Center Configuration Manager (officially called ConfigMgr 2012 or ConfigMgr 2007 or simply ConfigMgr), formerly Systems Management Server (SMS), is a systems management software product by Microsoft for managing large groups of computers running Windows, Windows Embedded, Mac OS X, Linux or UNIX, as well as various mobile operating systems such as Windows Phone, Symbian, iOS and Android. Configuration Manager provides remote control, patch management, software distribution, operating system deployment, network access protection and hardware and software inventory.

_- [Wikipedia: System Center Configuration Manager](https://en.wikipedia.org/wiki/System_Center_Configuration_Manager)_

The Splunk App for SCCM provides Windows System Administrators with an efficient overhead view
of their Windows Desktop and Server environments, allowing them to easily detect new systems
under management and spot outliers that may indicate a misconfiguration or a potential security risk.

The Splunk App for SCCM includes overview dashboards for:
* Hardware - Total CPUs, Architectures, Memory, Disks.
* Network - Number of unique MAC Addresses, NIC types and vendors.
* Operating System - Number of unique OS versions, builds, releases, and installation dates.
* Software - Number of software vendors, number of packages provided by each vendor, and product versions.

More detailed dashboards are alos provided for each of the above categories, allowing an administrator to
see information specific to a particular server within the app.

Dashboards for monitoring SCCM's internal logs provide an additional insight into the health and performance
of SCCM and its related components.

## Example Use Cases

* Using the Oldest Running Installation view under the Operating System Overview dashboard, administrators
can identify old systems that may be in need of a refresh.
* Administrators can get an overhead view of how many different software vendors they install products
from, allowing for accurate license cost prediction.
* Automatic detection of installed packages allows for auditing and detection of unauthorized software
installations across the environment.
* The Hardware Overview dashboard can be used to communicate the size and scale of an enterprise's
Windows Server footprint in terms of CPU, Memory, and Disk space.
* The Service Investigator can be used to identify Services running on specific servers or auditing
RunAs users for services (ex: company policy says non-Microsoft services cannot run as LocalSystem).
* Using the Database Query Log, an administrator can identify problematic, repated, or slow queries
being run against the SCCM database. The same log is also useful for helping an administraor discover
relationships between tables and views within the SCCM database, which is often lacking in documentation.
For more information on this use case, see the **Database Query Log** section below.


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


# Initial Configuration
_Steps below were originally provided by the Splunk-provided base application:_

1. Install pre-requisities
  * Splunk Enterprise
  * DBConnect App
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

```
[monitor://C:\Program Files\Microsoft Configuration Manager\Logs\*.log]
sourcetype = sccm_log_raw
index = sccm
```


# Closing Notes & License Information

This applicaiton was created by Rich Acosta and Erica Feldman as an entry
for the Splunk Apptitude contest. A sample application, `sccm.spl` was provided
to contestants as part of the [contest resources](http://splunk.challengepost.com/details/resources).

This application began as an improvement on top of the provided sample application, improving
on data integreity, correctness, performance, asthetics, and grammar throughout the application.

As the application was developed, most components of the original sample app were rewritten
or completely replaced.

Where applicable, the modifications to this app by Rich Acosta and
Erica Feldman are licensed under the MIT License. See `LICENSE` for full details.

