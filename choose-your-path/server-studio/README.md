# IBM Open Data Analytics for z/OS: Choose Your Path—Data Service - Server or Studio

<!-- # Licensed Materials - Property of IBM
# 5655-OD1
# COPYRIGHT IBM CORP. 2019
#
# US Government Users Restricted Rights - Use, duplication or
# disclosure restricted by GSA ADP Schedule Contract with
# IBM Corp. -->

## Table of Contents
- [Overview](#overview)
- [About](#about)
- [Choosing Your Lab](#choosing-your-lab)
- [Data Service Server Lab](#data-service-server-lab)
  - [Logging onto Z with PCOMM](#logging-onto-z-with-pcomm)
  - [Creating the ODL Started Task](#creating-the-odl-started-task)
  - [Customize the ODL Configuration Members](#customize-the-odl-configuration-members)
  - [Create the Required Datasets for the ODL Server](#create-the-required-datasets-for-the-odl-server)
  - [Start the ODL Server](#start-the-odl-server)
  - [Start the ISPF Administrative Application](#start-the-ispf-administrative-application)
- [Data Service Studio Lab](#data-service-studio-lab)
  - [Installing Data Service Studio](#installing-data-service-studio)
    - [Installation](#installation)
  - [Using Data Service Studio](#using-data-service-studio)
    - [Basic Overview of Perspective](#basic-overview-of-perspective)
    - [Establishing a Connection](#establishing-a-connection)
    - [Virtual Tables and Virtual Views](#virtual-tables-and-virtual-views)
    - [Using SQL Statements to Retrieve Data](#using-sql-statements-to-retrieve-data)
  - [Code Generation](#code-generation)
  - [Creating Your Own](#creating-your-own)
    - [Creating a Virtual Table](#creating-a-virtual-table)
    - [Creating a Virtual View](#creating-a-virtual-view)
- [Appendix](#appendix)
  - [Data Service Studio Full Install](#data-service-studio-full-install)
  - [Obtaining and Installing Maven](#obtaining-and-installing-maven)

## Overview
Come in and join us for a lab all about IBM Open Data Analytics for 
z/OS's Data Service. Choose between the Server or the Studio.

Choosing the Server, you will be taken through the steps to configure the MDS started 
task from existing program product libraries. Learn how to bring up the started task and 
configure the server to read from your z/OS data. 

Choosing the Studio, you will be taken through the steps on how to use the eclipse-based 
Data Service Studio, that allows you to use your workstation and interact with your 
datasets on z/OS. The available features that you'll be introduced to include virtual 
tables, virtual views, and code generation.

## About 
- Its interactive – depending on the path you choose, remotely log into a z/OS system 
using PCOMM/TSO to interact with ISPF & SDSF OR connect to a z/OS system in Poughkeepsie 
and explore some SMF datasets from within the Studio.

- If you haven’t, you may want to check out an IBM Open Data Analytics for z/OS session 
for an overview.

- Configure the ODL started task, configuration parameters and utility datasets. Start 
up a Data Services server, connect to it from the Studio and interact with virtual 
tables of the sample data on z/OS.

- The workstation Data Services Studio will be pre-installed, and used to learn about 
Virtual Table mappings, SQL queries of data using those virtual maps.
    - Create your own virtual table (mapping) to explore your own data.
    - Create your own virtual view to help contain the data that can be explored.
    
- SMF and VSAM data are available on the z/OS system to query against.

___

## Choosing Your Lab
- [Data Service Server Lab](#data-service-server-lab)
- [Data Service Studio Lab](#data-service-studio-lab)
___

## Data Service Server Lab
In today's lab you will learn about:
- Configuring the Data Service Server started task, configuration parameters and utility 
datasets.
- Starting up your configured Data Services server, connect to it from an administrative 
ISPF application and interact with  Server Trace facility and dynamic Rules Management 
options that the Data Services Server provides.
- z/OS userids will be assigned per Data Services server such that SPKLAB*n* will be associated with AZK*n*. For example, userid SPKLAB**F** can be used to log on TSO and configure server with SSID AZK**F**

_Note: The following instructions have been tailored for this lab. See Chapter 4:
Customizing the Data Service Server in the 
[IBM Open Data Analytics for z/OS Installation and Customization Guide](https://www-01.ibm.com/servers/resourcelink/svc00100.nsf/pages/izodav110sc279033/$file/azk1a100.pdf) 
for complete installation instructions._

### Logging onto Z with PCOMM
1) Log into a z/OS SHARE LPAR TSO session with the `SPKLABn` user assigned to you.

![tso-login]

2) Once logged in, navigate to ISPF Option 3.4 and list datasets with high-level qualifiers:
    
    a) <b>SPARKS.SAZK*</b>
    
    These are the IzODA program libraries that are pre-installed on the SHARE lab system.
        
    We'll use copies of members from **SPARKS.SAZKCNTL** & **SPARKS.SAZKEXEC** for our server
    customization. We'll run the executable code in **SPARKS.SAZKLOAD** and download binary
    members from **SPARKS.SAZKBIN** to the workstation for use there.
    
    b) <b>SPARKS.AZKn.SAZK*</b>
    
    Datasets with this high-level qualifier will be used for customizing 1 Data Services
    server per lab user in the following datasets:
    
    ![data-sets-with-hlq]
    
_Note:_
- The **SPARKS** high-level qualifier will be required later in the lab for substituting
wherever _**HLQ & SHLQ1**_ are referred to.

- The **SPARKS.AZKn** high-level qualifier will be required later in the lab for
substituting wherever _**SSID & SHLQ2**_ are referred to. 

### Creating the ODL Started Task

A started task procedure has already been created for your assigned Data Services server, 
by copying sample **SPARKS.SAZKCNTL(AZ1KPROC)**. In this section you will customize the JCL 
statements in this proc, which you’ll find in **SHARE.USER.PROCLIB(AZKn)**.

1. Edit your **SHARE.USER.PROCLIB(AZKn)** to customize it for your lab user/server
    1. Update the following parameter substitution values on the PROC statement 
        1. Set SSID=’AZKn’ – Used to customize the subsystem name that this server will 
        be referred to. It will also be used in the proc as part of the high-level 
        qualifier to allocate server-specific datasets. 
        2. Set HLQ=’SPARKS’ – Used to set up the high-level qualifier for the server data 
        set allocations.
    2. Find the SYSEXEC DD. 
        1. Its DSN= should resolve to your assigned **SPARKS.AZKn.SAZKEXEC**. 
        
        _This will allocate the correct data set name that contains the customized server 
        initialization member AZKnIN00. You will customize your configuration member later._
    3. Find the AZKMAPP DD; 
        1. Recognize that it includes a concatenation line that points its DSN= to your 
        assigned **SPARKS.AZKn.MAP** dataset that you will create later. This data set should be 
        first in the AZKMAPP concatenation and is used for storing user-defined virtual table 
        maps.
            1. Uncomment the following concatenation line to the AZKMAPP DD to pick up the 
            pre-packaged Virtual Table mappings for SMF, SYSLOG and OPERLOG that comes with 
            the product:

            ```//             DD   DISP=SHR,DSN=&HLQ..SAZKSMAP```
    4. Find the AZKTRACE DD; Its DSN= should resolve to your assigned **SPARKS.AZKn.TRACE** 
    dataset that you will create later.
    5. Find the AZKCHK1 DD; Its DSN= should resolve to your assigned **SPARKS.AZKn.SYSCHK1** dataset that you will create later.
    6. Find and review the STEPLIB DD for the key load libraries
        1. **SPARKS.SAZKLOAD** is required and should be APF-authorized.
        
        _You can use z/OS operator command D PROG,APF to confirm APF authorization from SDSF._
        
        2. For database manager access via ODL, various libraries would also be included in 
        the STEPLIB concatenation. For example, for DB2 DBMS access, you would need 
        DSN=&DB2LIB..SDSNEXIT and DSN=&DB2LIB..SDSNLOAD where &DB2LIB would point to your 
        local Db2 HLQ. 
        
        _This is beyond the scope of this lab so please ensure these lines are commented out._
        
    7. Review the AZKRPCLB DD for the other key executable code that are required to be 
    APF-authorized    
    	1. **SPARKS.SAZKRPC** is required and should be APF-authorized.
    8. Save and exit from your AZKn proc.

### Customize the ODL Configuration Members

In this section you will update your servers’ configuration members that were already created 
by copying samples from **SPARKS.SAZKEXEC** to **SPARKS.AZKn.SAZKEXEC**:
- AZKSIN00 renamed AZKnIN00 - this is the server initialization member and is a REXX program 
that you use to set product parameters and define links and databases.
- AZKSINEF renamed AZKnINEF - is a REXX program used by server during its System Event 
Facility (SEF) initialization step. No changes are necessary for our lab, but it must exist.
- AZK – the server’s ISPF application initialization REXX program.

1. Edit your new **SPARKS.AZKn.SAZKEXEC(AZKnIN00)**
    1. Update it so SHLQ1 = SPARKS  and SHLQ2 = SPARKS.AZKn 
    
    _Note that several program library datasets for Server Event Facility (SEF) support are 
    referred to in the AZKnIN00 by the SHLQ2 variable, and have been already been copied to 
    server-specific SPARKS.AZKn  datasets on the lab system. These are referred to in 
    Section 6 below and will be used for customizing SEF rules._
    
    ![azknin00-update]
    
    2. Update the TCP/IP port number values provided for your specific lab user/server
        ```
        "MODIFY PARM NAME(OEPORTNUMBER)         VALUE(xxxx)"   
        "MODIFY PARM NAME(WSOEPORT)             VALUE(yyyy)"   
        "MODIFY PARM NAME(OEPIOPORTNUMBER)      VALUE(zzzz)"
        ```
       
       ![tcp-ip-values]
       
    3. Save and exit from your AZKn member.

2. No changes are needed in your new **SPARKS.AZKn.SAZKEXEC(AZKnINEF)**. It just needs to exist.

3. Review your new **SPARKS.AZKn.SAZKEXEC(AZK)**:
    1. HLQ may need to be changed to SPARKS so that 
	```// llib = "SPARKS.SAZKLOAD"```

### Create the Required Datasets for the ODL Server

In this section you will create the required utility data sets for your server. The 
following batch jobs have been copied from **SPARKS.SAZKCNTL** to **SPARKS.AZKn.SAZKCNTL**. 

1. AZKGNMP1 – Creates the server specific MAP dataset referred to in the AZKMAPP DD in the 
started task proc. This dataset will be used to preserve any custom Virtual Table mapping 
you create.
    1. Modify the job per the prolog instructions in the job and submit it when ready. 
    When successful completed, you should have the following dataset created for your 
    server:
        1. **SPARKS.AZKn.SAZKMAP**

2. AZKDFDIV – Creates the server specific trace and metadata checkpoint DIV datasets 
referred to in the AZKTRACE and AZKCHK1 DDs in the started task proc.
    1. Modify the job per the prolog instructions in the job and submit it when ready. 
    When successful completed, you should have the following datasets created for your 
    server:
        1. **SPARKS.AZKn.TRACE**
        2. **SPARKS.AZKn.SYSCHK1**

3. AZKEXSWI – The Installation and Customization Guide requires you to also customize and 
run job AZKEXSWI job from SAZKCNTL to create the System Web Interface (SWI) facility rules 
that are shipped with the product. The job will place the rules into a server specific 
datasets _(e.g. **SPARKS.AZKn.SAZKXOBJ**). 
This has already been done for your server._

### Start the ODL Server
Now that we’ve created and customized the required datasets, we should be able to start 
the ODL server. 

_Important notes to be aware of at this point: 
- A STARTED class RACF profile has already been created for each server. 
- The AZKn address spaces could be configured to WLM to be classified as an AZK workload 
with a suitable service. This will not be done on the ShareLab system so it will run 
under the default service class is STCLO.

1. Start your server via SDSF
	`S AZKn`

2. Navigate to SDSF Status (ST) or Active Users (DA) to view your ODL server task’s job 
log to confirm it started up correctly. Message ID AZK3253I should report initialization 
complete:
    ```
    11.20.52 S0004828  AZK3253I Data Service for Apache Spark version 
    01.02.00 build 0000 SubSystem AZK8 initialization complete
    ```

3. Otherwise review the job for other AZK prefixed-messages to understand what went wrong.

### Start the ISPF Administrative Application
1. Navigate to the ISPF command shell (Option 6 in ISPF) and execute the AZK script that 
we customized in your **SPARKS.AZKn.SAZKEXEC**, passing it the SSID AZKn of your server:
    `EX 'SPARKS.AZKF.SAZKEXEC(AZK)' 'SUB(AZKF)'`
    
    ![ispf-admin-panel]

2. Navigate to the following Options to familiarize yourself with the panels
    1. B – Server Trace - Server Trace Facility: Use the Help (Option PF1) to learn how to 
    navigate the trace listing. For example: 
        ```
        DISPLAY DATE TIME MSGNO
        D DATE TIME USERID CNID
        ```
    2. C AZK Admin. - Server Management: This menu has many options, most of which are too 
    complex to tackle in this lab, illustrating how flexible the server configuration can 
    be. For example, you can navigate to option 2 - AZK Parms, then enter D next to the 
    PRODTRACE trace parameters group, to display the active trace settings and explore 
    which trace parameters can be enabled/disabled dynamically.

    _NOTE: Changing these options should be handled with care in a real server environment; 
    Please refer to your L2 support team before modifying any of them._
    
    3. E. Rules Mgmt. – Event Facility Management:
        1. Choose option 2 SEF Rule Management and hit enter twice.
        
        ![sef-rule-management]
        
        There are customizable ATH, CMD, EXC, SQL and TOD rulesets for the Server Event 
        Facility support. We will only touch on the Virtual Table (VTB) support in this lab.
        
        2. Select the VTB ruleset. 
        3. Then Select the AZKSMFT1 rule.
        
        ![azksmft1-rule]
        
        Review the REXX code, noting any of the variables it refers to. Cancel out of this 
        ISPF Edit session at this point since customizing this rule is beyond the scope of 
        this lab. If you’re handy with REXX &/or scripting, know that you can customize and 
        save these rules for your own purposes from here. 
        Modified VTB (as well as all other types of) rules can and should be saved in a 
        custom library, via the DEFINE RULESET statement in the AZKnIN00 configuration 
        member.
        
    4. Finally, note that all the rules are currently DISABLED, so we’ll need to enable any 
    that we need later.
    
    Enter line command E next to the AZKSMFT1 rule to enable it. 
    
    _NOTE: You may need to Select the rule, edit it (with something trivial), and save it 
    to create dataset statistics before the Enable will work._
    
    5. Navigate back to the Rule Management main menu Option E, then choose option 1 Global 
    Variables. 
    
    6. Enter GLOBAL2.SMFTBL2 (case sensitive) as the Global Prefix value, since this is the 
    global variable prefix that AZKSMFT1 uses in its event processing.
    
    ![global-prefix]
    
    7. Add a new variable Subnode called LABSMF88 to GLOBAL2.SMFTBL2 with the following command:
        `S LABSMF88 SPARKS.SHARPLEX.SMF88.D180801.T111846`
        
        ![subnode-labsmf88]

At this point, you can navigate back to the Primary Option menu; Leave the PCOMM session active 
there while we work on the Data Services Studio next. 
 
## Data Service Studio Lab
In today's lab you will learn about:
- Installing Data Service Studio as a plugin into an existing
Eclipse environment.
- Available mappings within Data Service Studio.
- Creating your own virtual table (mapping) to explore your own data.
- Creating your own virtual view to help contain the data that can be explored.

### Installing Data Service Studio

_The Data Service Studio installation file was already obtained. If you'd like to see how to
perform this step on your own system please see Installing the Data Service Studio in the 
[IBM Open Data Analytics for z/OS Installation and Customization Guide](https://www-01.ibm.com/servers/resourcelink/svc00100.nsf/pages/izodav110sc279033/$file/azk1a100.pdf) 
for complete installation instructions._

#### Installation

*Types of Installation:*

Data Service Studio can be installed in two different ways, as a full install or as a plugin. 
In today’s lab we will focus on installing Data Service Studio as a plugin. For further 
information on installing Data Service Studio as a full install, please see the end of lab: 
[Data Service Studio Full Install](#data-service-studio-full-install).
 
As a plugin install, Data Service Studio requires an available Eclipse environment. IBM 
Explorer for z/OS is available on the lab systems and can be used as your Eclipse Environment. 

1. Let’s launch our IBM Explorer for z/OS and continue into our workspace. 
2. Once it opens, we will want to navigate to the taskbar on top and click on *Help > 
Install New Software*

    ![help-install-new-software]
    
3. Under install window, click *Add*.
4. Under the Add Repository Window, click *Archive*

    ![add-repository-window-archive]
    
5. A file explorer window will open, locate the mds.zip file in the studio\install 
directory, then click *Open*
6. Name the repository and click *OK*

    ![add-repository-complete]
    
7. Select the box next to Mainframe Data Service, click *Next*

    ![mainframe-data-service-box]
    
8. Navigate to *Window > Perspective > Open Perspective > Other…*

    ![data-service-perspective]
    
9. Select *Data Service*, now *Open*

    ![open-perspectives]
 
### Using Data Service Studio
#### Basic Overview of Perspective
The Data Service Studio is initially broken up into 5 different sections. 

![basic-perspective-overview]

- Studio Navigator, in red, contains shortcuts to key task views, wizards, and editors.
- Explorer, in green, lists data resources, stored procedures, and metadata. 
- Active Connections, in purple, lists open JDBC connections.
- Editors View, in orange, is your workspace to create SQL Queries and execute them.
- SQL Results and Server Trace, in blue, displays output of SQL Query or Server Trace during execution.
 
#### Establishing a Connection
A connection is required in order to interact with the MDS (Optimized Data Layer).
1. Click on the Set Current Server shortcut under Studio Navigator. 

![set-current-server]

2. Enter connection information for MDS Server
    1. Host: 		mvs1.centers.ihost.com
    2. Port: 		1200
    3. Userid:      _See lab instructor_
    4. User Pass:	_See lab instructor_
    
    ![connection-information]
 
#### Virtual Tables and Virtual Views
When you finally establish a connection to your MDS server, you will be able to see the 
available Virtual Tables and Virtual Views on the server.
Inside of the Explorer section, select the server tab and navigating through SQL > Data > 
AZKS, you will see the available Virtual Tables and Virtual Views

![available-virtual-tables] ![available-virtual-views] 

Virtual Tables are libraries that are mapped to existing mainframe libraries which contain 
information required to virtualize the source data. Some examples are OPERLOG, SMF, and 
SYSLOG.
Virtual Views take advantage of Virtual Table Mappings and allow you to control the 
columns being returned. Comprised of the SQL SELECT Statement, a virtual view contains 
the columns from the source data that are used to read data directly from the data a 
source.

#### Using SQL Statements to Retrieve Data
The main interaction between Data Service Studio and your data sources, is through SQL 
Queries. You can create your own SQL Queries, or you can base it off of a Virtual Table 
Mapping.
 
Let’s query our server for some SMF data. 
1. Under Virtual Tables, lets navigate down until we find the mapping for SMF30 record 
types. 
2. Right-click on *SMF_03000_SMF30ID*, this is the Identification Table mapping of the 
SMF30 records.
3. Select *Generate Query*

![generate-query]

4. On your Editor you will notice an SMF Query has been generated for you for the SMF 
30 Identification Table. 
5. You may be prompted to execute the query, select Yes. If you are not prompted, you 
can select the editor and press *F5* to execute the query.
```
-- Description:   Retrieve the result set for SMF_03000_SMF30ID (up to 1000 rows)
-- Tree Location: zos-hostname/1200/SQL/Data/AZKS/Virtual Tables/SMF_03000_SMF30ID
-- Remarks:       SUBTABLE - SMFDATA
SELECT SMF30JBN, SMF30PGM, SMF30STM, SMF30UIF, SMF30JNM, SMF30STN, SMF30CLS, 
  SMF30JF1, SMF30PGN, SMF30JPT, SMF30AST, SMF30PPS, SMF30SIT, SMF30STD, SMF30RST, 
  SMF30RSD, SMF30RET, SMF30RED, SMF30USR, SMF30GRP, SMF30RUD, SMF30TID, SMF30TSN, 
  SMF30PSN, SMF30CL8, SMF30ISS, SMF30IET, SMF30SSN, SMF30EXN, SMF30ASI, SMF30COR, 
  ROW_INDEX, PARENT_KEY, BASE_KEY
FROM SMF_03000_SMF30ID LIMIT 1000;
```
6. After the query runs, you’ll see the results appear in the SQL Results section.

![run-query]

*Hint: When just a virtual table or view is specified, the global dataset will be 
queried. MDS comes with a set of virtual table rules that can be enabled, if the 
virtual table rule AZKSMFT1 is enabled, you are able to specify which dataset to query 
by adding a double underscore to the end of the mapping followed by the dataset name, 
changing any ‘.’ into ‘_’. You can try this with the following dataset:*
**SPARKS_SHARPLEX_SMF_D180801_T111846**

![smf-double-underscore]
 
### Code Generation
Data Service Studio has the ability to take your SQL query and generate a simple 
application. Currently there is support for a Java Spark Application, Scala Spark 
Application, and a Scala Jupyter Notebook.

1. Start by right-clicking the same Virtual Table, but instead choose *Generate Code 
from SQL*

![generate-code]

2. Name and Compose your SQL then click *Next*

![name-sql]

3. Verify the columns in the Result Set, click *Next*

![view-columns-of-sql]

4. Choose your generation target, let’s choose Scala, then click *Done*

![code-target]

5. Scala Code will appear in your editor.
```
object SMF30ID {

  // MDS JDBC Driver Properties
  object Properties {
    // Credentials
    val user = "username"
    val password = “62b668f4bf6030a646“
[... omitted ...]
    val baseUrl = "jdbc:rs:dv://zos-hostname:1200;DatabaseType=DVS;“
[... omitted ...]
    val sql = "(SELECT * FROM SMF_03000_SMF30ID__HLQ_DATA_SET_SMF30 LIMIT 1000)“
[... omitted ...]
```

With your Scala code, you will notice that it automatically hashes your password. 
This is because your username and password are used when making a JDBC call through 
a Scala application. Additionally, Data Service Studio takes your existing settings 
and applies them to your application, such as your MDS server information and the 
SQL query.

_Note: As this is not a development system, key plugins are missing to complete the 
application and submit it through Spark on z/OS. For today’s lab we will not be 
going any further with the application, but if you’d like to obtain the maven plugin 
that can help you build an application, please see 
[Obtaining and Installing Maven](#obtaining-and-installing-maven) at the end of the 
lab for further information._

_Optionally, you can switch back to your PCOMM session, and use the ISPF Admin 
interface to review the Server Trace to familiarize yourself with the messages, 
especially recognizing which messages represent your SQL query being executed and 
SQL messages & results being logged._
 
_The ISPF Admin interface is started from the ISPF command shell (Option 6 in ISPF) 
with command:_
 
 `EX 'SPARKS.AZKS.SAZKEXEC(AZK)' 'SUB(AZKS)'`  

### Creating Your Own
We have gone over many of the capabilities of Data Service Studio, including virtual 
tables and virtual views. Data Service Studio comes with a large amount of these 
tables for already pre-defined SMF and SYSLOG, but what happens if you have your own 
structured data stored in datasets that you’d like to be able to access? 

Data Service Studio has the ability to create new Virtual Tables and Virtual Views to 
do just this. 

#### Creating a Virtual Table
There are a number of different virtual table mappings that can be created, such as 
Adabas, RDBMS, IMS, Sequential, VSAM. For today’s lab we will create a Sequential 
dataset mapping. Sequential dataset mappings require a Cobol copybook that describes 
the structure of the data. 

We will be creating a Virtual Table for our Employee Data. A Cobol copybook has 
already been created to describe the structure of our data.

```
         IDENTIFICATION DIVISION.     
         PROGRAM-ID. PGM1.            
         ENVIRONMENT DIVISION.        
         DATA DIVISION.               
         LINKAGE SECTION.             
                                      
         01 Employee.                 
            05 FirstName    PIC X(12).
            05 LastName     PIC X(12).
            05 EmpID        PIC X(12).
            05 EmpDOB       PIC X(12).
            05 HomeCity     PIC X(16).
```

```
Josephine   Darakjy     285586      10/11/1906  Las Vegas      
Art         Venere      399664      4/2/1931    Norfolk        
Lenna       Paprocki    901192      5/28/1932   Memphis        
Donette     Foller      987267      6/3/1932    Dallas         
Simona      Morasca     396768      5/17/1935   Raleigh        
Mitsue      Tollner     408316      9/2/1936    Pittsburgh     
Leota       Dilliard    245120      10/30/1938  Los Angeles   
... 
```

To get started, we need to load our Cobol copybook into the Virtual Source Library. 
We can do this through the Virtual Source Library Wizard.
1. Under Explorer, navigate to *Admin > Source Libraries*
2. Double-click *Create Virtual Source Library*

![create-virtual-source-library]

3. Name the Virtual Source Library and then set the Library Name to our dataset 
with our Cobol copybook.
    1. Name:		SPKLABn_EMPLOYEE_SRC_LIB
    2. Dataset: 	SPARKS.COBOL.MDS.SRCLIB
    
![name-virtual-source-library]    

4. Navigate to the Virtual Table directory under the Explorer section
5. Right-click and select *Create Virtual Table*

![create-virtual-table]

6. Select Sequential as the type of virtual table

![sequential-type]

7. Provide the mapping with a name
    1. Name:			SPKLABn_EMPLOYEE_INFO
    2. Metadata Library: 	SPARKS.AZKS.SAZKMAP
    3. Next
    
    [mapping-name]: images/studio/mapping-name.png "mapping-name"
    
8. Choose your new Source Library
    1. Choose Source Library Member (CBT101)
    2. Download
    3. Next
    
    ![source-library-member]
    
9. Look over your Virtual Table Layout, Next

![virtual-table-layout]

10. Provide the dataset name you’d like the mapping to read.
    1. Dataset Name: SPARKS.EMPLOYEE
    2. Click Validate
    3. Finish
    
    ![sparks-employee]
    
11. Generate an SQL Query and execute, if not already done for you. You’ll see 
results in our SQL Query Results.

![sql-query-results]
 
#### Creating a Virtual View
Even though you may want to have access to all the data available in the dataset, 
you may want to restrict some information from being available to everyone. This 
information may contain very sensitive information such as PI. In our example, 
our virtual table is able to read all of our employee’s information, even their 
date of birth. Although it is part of our data’s structure, our end user, such as 
a data scientist does not need access to this information. 

We can create a Virtual View that allows us to control the information that 
someone is able to see. The creation of a Virtual View requires a Virtual Table 
of our data to be available but is simple to create.

1. Navigate to the Virtual Table directory under the Explorer section
2. Right-click and select *Create Virtual View*

![create-virtual-view]

3. Name the Virtual View
    1. Name:		SPKLABn_EMPLOYEE_INFO_RESTRICTED
    2. Next
    
    ![name-virtual-view]
    
4. Choose the Virtual Table we’re basing on.
    1. Table Browser	SPKLABn_EMPLOYEE_INFO
    2. Next
    
    ![virtual-view-review]
    
5. The SQL statement provided will show a list of all available columns in our 
Virtual Table. 
    1. In order to prevent the sensitive information from being seen in this view, 
    we will simply remove EMPDOB from the SQL statement.
    2. Validate
    
    ![virtual-table-sql]
    
6. Our Virtual Table will appear under the servers available Virtual Tables

![our-view-in-server]

7. Right-Click and Generate Query

![generated-view-query]

8. Execute Query

![restricted-view]

You can now see that with our restricted view, our employee’s date of birth is no
longer available.

You have successfully made it to the end of today’s lab.

## Appendix
### Data Service Studio Full Install
Data Service Studio has the ability to be installed as its own Eclipse environment. 
This allows you to use Data Service Studio without having to install an alternative 
Eclipse program. 

_Note: Data Service Studio full install is only available for *Windows 64-bit*._

After the contents of the zip file have been extracted:
1. Navigate to your extracted files
2. Open the *studio* directory
3. Go into the *install* directory
4. Run *setup.bat*
5. Return to the main directory
6. Run *launch.bat*

![full-install-directories]

### Obtaining and Installing Maven
In order to successfully build and deploy a Spark application, a build tool needs to 
be used in order to build the application jar and submit it through Spark. 
A recommended build tool would be Apache Maven. You can install the maven plugin into 
your Eclipse environment by using the same steps found in Obtaining and Installing 
Data Service Studio, section 3 and instead using the link below:
http://download.eclipse.org/technology/m2e/releases/

There are many tutorials online that can help you further with your application 
development after you’ve obtained the maven plugin and installed it into your Eclipse 
environment.

[tcp-ip-values]: images/server/tcp-ip-values.png "tcp-ip-values"
[tso-login]: images/server/tso-login.png "tso-login"
[data-sets-with-hlq]: images/server/data-sets-with-hlq.png "data-sets-with-hlq"
[azknin00-update]: images/server/azknin00-update.png "azknin00-update"
[ispf-admin-panel]: images/server/ispf-admin-panel.png "ispf-admin-panel"
[sef-rule-management]: images/server/sef-rule-management.png "sef-rule-management"
[azksmft1-rule]: images/server/azksmft1-rule.png "azksmft1-rule"
[global-prefix]: images/server/global-prefix.png "global-prefix"
[subnode-labsmf88]: images/server/subnode-labsmf88.png "subnode-labsmf88"

[help-install-new-software]: images/studio/help-install-new-software.png "help-install-new-software"
[add-repository-window-archive]: images/studio/add-repository-window-archive.png "add-repository-window-archive"
[add-repository-complete]: images/studio/add-repository-complete.png "add-repository-complete"
[mainframe-data-service-box]: images/studio/mainframe-data-service-box.png "mainframe-data-service-box"
[data-service-perspective]: images/studio/data-service-perspective.png "data-service-perspective"
[open-perspectives]: images/studio/open-perspectives.png "open-perspectives"
[basic-perspective-overview]: images/studio/basic-perspective-overview.png "basic-perspective-overview"
[set-current-server]: images/studio/set-current-server.png "set-current-server"
[connection-information]: images/studio/connection-information.png "connection-information"
[available-virtual-tables]: images/studio/available-virtual-tables.png "available-virtual-tables"
[available-virtual-views]: images/studio/available-virtual-views.png "available-virtual-views"
[generate-query]: images/studio/generate-query.png "generate-query"
[run-query]: images/studio/run-query.png "run-query"
[smf-double-underscore]: images/studio/smf-double-underscore.png "smf-double-underscore"
[generate-code]: images/studio/generate-code.png "generate-code"
[name-sql]: images/studio/name-sql.png "name-sql"
[view-columns-of-sql]: images/studio/view-columns-of-sql.png "view-columns-of-sql"
[code-target]: images/studio/code-target.png "code-target"
[create-virtual-source-library]: images/studio/create-virtual-source-library.png "create-virtual-source-library"
[name-virtual-source-library]: images/studio/name-virtual-source-library.png "name-virtual-source-library"
[create-virtual-table]: images/studio/create-virtual-table.png "create-virtual-table"
[sequential-type]: images/studio/sequential-type.png "sequential-type"
[source-library-member]: images/studio/source-library-member.png "source-library-member"
[virtual-table-layout]: images/studio/virtual-table-layout.png "virtual-table-layout"
[sparks-employee]: images/studio/sparks-employee.png "sparks-employee"
[sql-query-results]: images/studio/sql-query-results.png "sql-query-results"
[create-virtual-view]: images/studio/create-virtual-view.png "create-virtual-view"
[name-virtual-view]: images/studio/name-virtual-view.png "name-virtual-view"
[virtual-view-review]: images/studio/virtual-view-review.png "virtual-view-review"
[virtual-table-sql]: images/studio/virtual-table-sql.png "virtual-table-sql"
[our-view-in-server]: images/studio/our-view-in-server.png "our-view-in-server"
[generated-view-query]: images/studio/generated-view-query.png "generated-view-query"
[restricted-view]: images/studio/restricted-view.png "restricted-view"
[full-install-directories]: images/studio/full-install-directories.png "full-install-directories"
