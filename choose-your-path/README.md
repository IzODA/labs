# IBM Open Data Analytics for z/OS: Choose Your Path—Spark and Scala or Anaconda and Python

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
- [Getting Started](#getting-started)
  - [Connecting to z/OS system](#connecting-to-z/os-system)
    - [Connecting through Windows](#connecting-through-windows)
    - [Connecting through Mac](#connecting-through-mac)
- [Choosing Your Lab](#choosing-your-lab)
- [Spark / Scala Lab](#spark-/-scala-lab)
  - [Starting up spark-shell](#starting-up-spark-shell)
  - [Working with our data](#working-with-our-data)
    - [Loading our data](#loading-our-data)
    - [Viewing our data](#viewing-our-data)
    - [Understanding our data](#understanding-our-data)
    - [Limiting our data](#limiting-our-data)
    - [Counting our data](#counting-our-data)
    - [Grouping our data](#grouping-our-data)
    - [Ordering our data](#ordering-our-data)
  - [Conclusion from our data](#conclusion-from-our-data)
    - [What did we find?](#what-did-we-find?)
  - [Exiting the spark-shell](#exiting-the-spark-shell)
  - [Spark through a jupyter notebook](#spark-through-a-jupyter-notebook)
    - [Starting the jupyter notebook for Spark](#starting-the-jupyter-notebook-for-spark)
    - [Viewing the Spark jupyter notebooks](#viewing-the-spark-jupyter-notebooks)
- [Anaconda / Python Lab](#anaconda-/-python-lab)
  - [Starting up the python interpreter](#starting-up-the-python-interpreter)
  - [Anaconda and its environments](#anaconda-and-its-environments)
  - [Python through a jupyter notebook](#python-through-a-jupyter-notebook)
    - [Starting the jupyter notebook for Python](#starting-the-jupyter-notebook-for-python)
    - [Viewing the Python jupyter notebooks](#viewing-the-python-jupyter-notebooks)
- [References](#references)
  - [Jupyter notebook reference](#jupyter-notebook-reference)
    - [About jupyter](#about-jupyter)
    - [Launching the jupyter notebook server](#launching-the-jupyter-notebook-server)
    - [Viewing the jupyter notebook server](#viewing-the-jupyter-notebook-server)
    - [Starting a jupyter notebook](#starting-a-jupyter-notebook)    
    - [Stopping a jupyter notebook](#stopping-a-jupyter-notebook)
  


## Overview
Join us for a lab all about IBM Open Data Analytics for z/OS's two analytic stacks. 
Choose between Spark and the Scala programming language or Anaconda and the Python 
programming language. Choosing the Spark analytic stack, we will dive into z/OS data, 
such as SMF datasets, to investigate the data, visualize it, and finally grab various 
SMF operational insights. Choosing the Anaconda analytics stack, we will dive into 
Anaconda environments along with the various data analytics packages, as well as work 
in Python to dive into z/OS data to gather various insights. If you're able to complete 
one analytics stack, you may choose to continue onto the other, all within this one lab.

## About 
 - Interactive Lab
    - Remotely log into a z/OS system in IBM Poughkeepsie using SSH through PuTTY or
    Terminal
 - Two Environments
    - Unix System Services and Jupyter notebook
    
 - Today's Exercise
    - We need to catch a run-away job that is generating SMF records and filling up our 
    logstream!
    - SYSLOG isn't helpful, we need to do some ad-hoc processing of SMF30 to figure this
    out.
___

## Getting Started
### Connecting to z/OS system
For both Interactive labs you will need to connect and log into a z/OS system in 
Poughkeepsie. Depending on which type of system you're on, initial setup and connection
is slightly different.

During the next portion you will be required to have a username and password. Please use 
the provided username and password when prompted, or see your lab instructor for this 
information.

#### Connecting through Windows
Locate and start-up the PuTTY application on your system. Once launched add the following 
hostname: 

```
mvs1.centers.ihost.com
``` 

Once hostname has been entered, click `Open`. You may receive a popup to verify server's 
host key. Click `accept` to continue. You will then be prompted for a username and password.

#### Connecting through Mac
Launch Mac terminal. Once launched, you are going to ssh into the server, using the provided 
username, with the following command:

```
ssh -c aes128-cbc $PROVIDED_USERNAME@mvs1.centers.ihost.com
``` 

You may receive a popup to verify server's host key. Click accept to continue.
You will then be prompted for a password.

## Choosing Your Lab
Once logged into our z/OS system under your provided username, you will be prompted to 
choose your lab.

```
Enter the number corresponding to which IBM Open Data Analytics 
lab you are doing today:
     1 - Spark and Scala
     2 - Anaconda and Python
```
- Type `1` followed by `ENTER` to begin working on the Spark and Scala lab. 
[Spark / Scala Lab](#spark-/-scala-lab)
- Type `2` followed by `ENTER` to begin working on the Anaconda and Python lab. 
[Anacond / Python Lab](#anaconda-/-python-lab)

___

## Spark / Scala Lab
In today's lab you will learn about:
- Spark's interactive shell
- Read SMF data from an USS file containing SMF30 records into Spark
- Interacting with the data by organizing, filtering, etc
- Discovering who the cause of our issue is
- Jupyter notebook environments and the additional benefit of visualizing data

### Starting up spark-shell
We'll start by entering the Spark interactive shell known as spark-shell. spark-shell
provides a simple way to learn the API, as well as a powerful tool to analyze data 
interactively. 

When Spark's interactive shell starts up, a spark context and spark session are
automatically created for you. They set up internal services and a connection to Spark,
as well as create an entry point to programming in Scala with the Dataset and DataFrame
APIs. 

To launch the spark-shell, we will issue the following command:

```
$SPARK_HOME/bin/spark-shell
```

This command consists of an environment variable, SPARK_HOME. This variable has been 
configured to point to our Spark installation on the z/OS system. This allows us to
reference Spark and it's available executables under the bin directory.

Once you launch the spark-shell, you will see a scala prompt on the bottom.

```
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel).
19/02/10 16:16:06 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
19/02/10 16:16:06 WARN NetUtil: Failed to find the loopback interface
19/02/10 16:16:12 WARN ObjectStore: Failed to get database global_temp, returning NoSuchObjectException
Spark context Web UI available at http://x.x.x.x:xxxx
Spark context available as 'sc' (master = local, app id = local-1549833367079).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.2.0
      /_/
         
IBM Open Data Analytics for z/OS - Spark Version 2.2.0
Using Scala version 2.11.8, Java JRE 1.8.0_171
Type in expressions to have them evaluated.
Type :help for more information.

scala> 
```

### Working with our data
#### Loading our data
Now that we're in our spark-shell, we'll want to load our data into a DataFrame. 

*Note, for the sake of this lab, we've taken an SMF30 Dataset, mapped over it with an MDS
mapping, the Identification Section of the SMF30 Record, *SMF_03000_SMF30ID*, and converted
to a json for you to read.

By executing the following command, we're going to use Spark to read the json file and 
create our DataFrame to work with.

```
val df = spark.read.json("../../data/SMF30.json")
```

#### Viewing our data
Using the show() method, you display the contents of the DataFrame. By default, only the
first 20 rows will be displayed. You may pass an integer to display more or less rows.

Issue the following command:

```
df.show()
```

After executing the command, you can see that there is a lot of data, too much to display
on a single screen. We are displaying all the columns in teh Identification Section of the 
SMF30 Record, but we only need to use some columns.

```
WARN Utils: Truncated the string representation of a plan since it was too large. This behavior can be adjusted by setting 'spark.debug.maxToStringFields' in SparkEnv.conf.
+--------+----------+---------+--------+--------+--------+--------+--------------------+----------------+--------+--------------------+--------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
|BASE_KEY|PARENT_KEY|ROW_INDEX|SMF30ASI|SMF30AST|SMF30CL8|SMF30CLS|            SMF30COR|        SMF30EXN|SMF30GRP|            SMF30IET|            SMF30ISS|SMF30JBN|SMF30JF1|SMF30JNM|SMF30JPT|SMF30PGM|SMF30PGN|SMF30PPS|SMF30PSN|SMF30RED|SMF30RET|SMF30RSD|SMF30RST|SMF30RUD|SMF30SIT|SMF30SSN|SMF30STD|SMF30STM|SMF30STN|SMF30TID|SMF30TSN|SMF30UIF|SMF30USR|
+--------+----------+---------+--------+--------+--------+--------+--------------------+----------------+--------+--------------------+--------------------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+--------+
|00000001|  00000001|        1|     253|       0|     STC|      64|S0007786PLPSCHO.D...||   TASKS|0001-01-01T00:00:...|0001-01-01T00:00:...| SMFDRSL|00000000|STC07786|       0|        |       0|       0|        |  117055| 3940885|  117055| 3940884| SYSUSER|       0|       0|       0|        |       0||        |        |        |
|00000002|  00000002|        1|     253| 3940887|     STC|      64|S0007786PLPSCHO.D...||   TASKS|2017-02-24T15:56:...|2017-02-24T15:56:...| SMFDRSL|00000000|STC07786|       0|IKJEFT01|       0| 3940888|STARTING|  117055| 3940885|  117055| 3940884| SYSUSER| 3940887|       0|  117055| SMFDRSL|       1||        |        |        |
|00000003|  00000003|        1|     253| 3940887|     STC|      64|S0007786PLPSCHO.D...||   TASKS|2017-02-24T15:56:...|2017-02-24T15:56:...| SMFDRSL|00000000|STC07786|       0|IKJEFT01|       0| 3940888|STARTING|  117055| 3940885|  117055| 3940884| SYSUSER| 3940887|       0|  117055| SMFDRSL|       1||        |        |        |
|00000004|  00000004|        1|     253| 3940887|     STC|      64|S0007786PLPSCHO.D...||   TASKS|0001-01-01T00:00:...|0001-01-01T00:00:...| SMFDRSL|00000000|STC07786|       0|        |       0| 3940888|        |  117055| 3940885|  117055| 3940884| SYSUSER| 3940887|       0|  117055|        |       1||        |        |        |
|00000005|  00000005|        1|     253|       0|     STC|      64|S0007787PLPSCHO.D...||   TASKS|0001-01-01T00:00:...|0001-01-01T00:00:...| SMFDRSL|00000000|STC07787|       0|        |       0|       0|        |  117055| 3941327|  117055| 3941283| SYSUSER|       0|       0|       0|        |
...  
```

#### Understanding our data
In order to better understand our data, we need to understand what is being show to us. 
By looking at the headers of our data, we should be able to get a better idea of what
we're looking at. In order to see the headers in a clean format we can issue a method of
DataFrame API. 

Issue the following command:

```
df.printSchema()
```

After executing the command, you can see a tree of all available headers of the 
Identification Section of the SMF30 Record.

```
 |-- BASE_KEY: string (nullable = true)
 |-- PARENT_KEY: string (nullable = true)
 |-- ROW_INDEX: long (nullable = true)
 |-- SMF30ASI: long (nullable = true)
 |-- SMF30AST: long (nullable = true)
 |-- SMF30CL8: string (nullable = true)
...
 |-- SMF30JBN: string (nullable = true)
 |-- SMF30JF1: string (nullable = true)
 |-- SMF30JNM: string (nullable = true)
 |-- SMF30JPT: long (nullable = true)
 |-- SMF30PGM: string (nullable = true)
...
 |-- SMF30RUD: string (nullable = true)
 |-- SMF30SIT: long (nullable = true)
 |-- SMF30SSN: long (nullable = true)
 |-- SMF30STD: long (nullable = true)
...
```

#### Limiting our data
We are interested in the Job Name *SMF30JBN* and RACF User ID *SMF30RUD* for our exercise
today. Now that we have the column names we're interested in, we can display just what is
important to us. Lets display only the columns we're interested in.

Issue the following command:

```
df.select("SMF30JBN", "SMF30RUD").show()
```

After executing the command, you can see that we are displaying only the Job Names and
RACF User IDs. Some Job Names may not be associated with a User ID.

```
+--------+--------+
|SMF30JBN|SMF30RUD|
+--------+--------+
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
| SMFDRSL| SYSUSER|
|MSTJCL00|+MASTER+|
|  PCAUTH||
|IEFSCHAS||
|     GRS||
|    RASP||
|   TRACE||
| CONSOLE||
| SMSPDSE||
|SMSPDSE1||
| ALLOCAS||
|  BBOWTR| SYSUSER|
|  DEVMAN|       *|
+--------+--------+
```

#### Counting our data
As mentioned previously, the show() method only shows the top 20 rows if it exceeds 20.
Lets see how many rows we actually have.

Issue the following command:

```
df.select("SMF30JBN", "SMF30RUD").count()
```

After executing the command, the number of rows for our DataFrame is returned. For our
Dataframe, that is 1167 rows, which are really the number of SMF30 records.

#### Grouping our data
Within our 1167 SMF30 records, there may be a number of duplicate Job Names or RACF User
IDs. We can group them by a specific column in order to bring them all together.
Additionally we can add a new count column by adding the count method. We can create
two new DataFrames, one for jobs and one for users by issuing the following commands:

```
val jobs = df.groupBy("SMF30JBN").count()
val users = df.groupBy("SMF30RUD").count()
```

#### Ordering our data
Now that we've created our two new DataFrames, we can order them in a descending order. 
We can also look for the top 10. 

Issue the following command against our jobs DataFrame:

```
jobs.orderBy(jobs.col("count").desc).limit(10).show()
```

After executing the command, we see the top 10 job names from our SMF30 records.

```
+--------+-----+                                                                
|SMF30JBN|count|
+--------+-----+
| SATURN9|  116|
| SATURN8|  114|
| SATURN1|  114|
| SATURN4|  110|
| SATURN7|  110|
| SATURN2|  110|
| SATURN6|  110|
| SATURN5|  110|
| SATURN3|  110|
| SMFDRSL|    8|
+--------+-----+
```

Issue the following command against our users DataFrame:

```
users.orderBy(users.col("count").desc).limit(10).show()
```

After executing the command, we see the top 10 users from our SMF30 records.

```
+--------+-----+                                                                
|SMF30RUD|count|
+--------+-----+
|  SATURN| 1004|
| SYSUSER|   22|
|  CANSID|   15|
|       *|   11|
| STASKID|   11|
|     TCP|   10|
||   10|
|    OMVS|    9|
|  WEBPKI|    9|
|     PFA|    9|
+--------+-----+
```

It has become pretty clear which user caused the spike. SATURN has a count (or number
of occurrences) 50 times greater than the second highest. More incriminating is that
the number of occurrences (1004) just happens to equal the sum of all occurrences of
the top 10 jobs.

### Conclusion from our data
#### What did we find?
- Circling back to our original problem, all of these jobs came from one User ID.
- Due to the suffix of the jobnames being a random number, they were z/OS Unix processes.
- Apparently the user's script went into some kind of loop.

### Exiting the spark-shell
You may continue to interact with our given DataFrame if you'd like to investigate some
other columns. Once you are done interacting with the spark-shell, we can exit the
spark-shell.

Issue the following command:

```
:quit
```

Once issued, you will return back to the bash shell and the directory that you were in
when you called the Spark interactive shell.

### Spark through a jupyter notebook
#### Starting the jupyter notebook for Spark
The Spark interactive shell, spark-shell, isn't the only way to interact with Spark. 
You can also use a jupyter notebook to work with Spark. This will allow us to add
some visualization to our data. Lets repeat what we did in USS within a jupyter
notebook. If you have not worked with jupyter notebooks before, you can visit the 
[jupyter notebook reference section](#jupyter-notebook-reference) for a basic 
description and usage of jupyter notebooks.

Issue the following command to launch a jupyter notebook:

```
jupyter notebook
```

After executing the command, a notebook will launch and provide you with the host URL.
You will want to copy and paste this URL into your browser.

*Note: This ssh session just remain open while you are using the jupyter notebook.*

#### Viewing the Spark jupyter notebooks
Launch the notebook `choose-your-path-scala.ipynb`. Once loaded, you will see the first
several cells filled in with code already. Run these cells to repeat what we've 
done already in our Unix environment. Although we've come up with the same conclusion, 
lets see what additional validation we can get by simply visualizing the data.

First we will want to load our visualization package known as brunel.

Issue the following command in the first open cell:

```
%AddJar -magic https://brunelvis.org/jar/spark-kernel-brunel-all-2.3.jar
```

The Apache Toree kernel allows us to load brunel as a magic, which allows us to 
visualize right in our browser.

Next lets save two DataFrames with the Top 10 JobNames and UserIDs that we
identified before, so we will be able to graph them.

Issue the following command in the cell:

```
val jobsTop10 = jobs.orderBy(jobs.col("count").desc).limit(10)
val usersTop10 = users.orderBy(users.col("count").desc).limit(10)
```

Now that we have our new Top 10 DataFrames created, lets graph them. First lets
create a bar graph of the Top 10 JobNames, followed by one for the Top 10 UserIDs.
These commands will go in two separate cells, as you can only have a single
graph per cell.

Issue the following command in the cell:

```
%%brunel data('jobsTop10') bar x(SMF30JBN) y(count)
```

Issue the following command in the cell:

```
%%brunel data('usersTop10') bar x(SMF30RUD) y(count)
```

The bargraphs that we displayed in the last two cells, starts to gives us a better
representation of our data. With this data however, a bar graph may not be the
best way to show the magnitude of our culprits behavior. Brunel supports a number
of different types of graphs. Lets graph the userids as a pie graph.

Issue the following command in the cell:

```
%%brunel data('usersTop10') stack polar bar x(SMF30RUD) y(count) color(SMF30RUD) label(SMF30RUD)
```

Although we are graphing the same data, the pie graph shows the magnitude of what
our culprit has cause.

If you'd like to see what other types of graphs are supported and try some yourself
you can visit the 
[Brunel Visualization Cookbook](https://github.com/Brunel-Visualization/Brunel/wiki/Brunel-Visualization-Cookbook).

**Congratulations! You have completed the Spark and Scala Lab.**

Thank you for choosing to participate in this lab today, you may:
- Try looking at a couple other pre-populated notebooks available in the jupyter notebook
server simply by closing out of this notebook by going to `File` -> `Close and Halt`.
- Trying out the Anaconda and Python Lab. *Please let your lab instructor know
so we can reset your environment for you.*
___

## Anaconda / Python Lab
In today's lab you will learn about:
- Python interpreter along with some basic python programming
- Anaconda package manager and how environments work
- Discovering who the cause of our issue is
- Jupyter notebook environments
- Read SMF data from an USS file containing SMF30 records into a pandas DataFrame
- Interacting with the data by organizing, filtering, etc
- Discovering who the cause of our issue is

### Starting up the python interpreter
We'll start by entering the python interpreter inside of z/OS. Here we will be able to
write a few lines of the python programming language.

To launch the python interpreter, issue the following command:

```
python
```

When the python interpreter launches, you will see the version of python running and
the >>> prompt.

```
Python 3.6.1 (heads/v3.6.1-anaconda:00f76f8, Feb 21 2018, 05:07:54) [C] on zos
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

Now that we are inside of our python interpreter, lets do some basic python programming.
We will first start by simply printing to the screen. 

Issue the following command:

```
print("Hello World")
```

The print command is a built-in function, which allows you to output to the screen. 
As we are using Python 3, a pair of parenthesis are required. 

Now lets take it a step further and do a little bit of arithmetic. Lets find the sum
of all the numbers between 1 and 10. In order to do this, we will first want to create
a variable to store our sum in. *Python uses dynamic typing, so we do not need to 
specify the type when declaring our variable.*

Issue the following command:

```
sum = 0
```

Next we can create a simple for-loop, to iterate through our numbers and add it to our
sum. *Be aware that, python follows white-space rules and requires indentations to be 
used when applicable.*

Issue the following command:

```
for num in range(1,10):
    sum += num

```

Python programming is meant to be easy to read. The snippet above can simply be read
as, for every number in the range of 1 to 10, add to the sum. Now lets output our
sum.

Issue the following command:

```
print(sum)
```

When you hit enter, you will see the output of 45.

Now that we've taken a small look into python, lets dive a bit into the package 
manager known as Anaconda. As part of IBM Open Data Analytics for z/OS, over 200+
packages have been brought into z/OS and is managed by Anaconda. By default all
these packages are made available to us. While inside the python interpreter we
can attempt to import some of these packages.

Issue the following command:

```
import numpy as np
```

The above will import the numpy package that is available to you. 
You can see where they're imported from by simply calling the package you imported.

```
>>> np
<module 'numpy' from '/usr/lpp/IBM/izoda/anaconda/lib/python3.6/site-packages/numpy/__init__.py'>
```

This and other packages are available to you because they're already in your 
environment. Lets look at how Anaconda environments work. 

Exit out of the python interpreter by issuing the following command:

```
quit()
```

### Anaconda and its environments
By default, Anaconda contains a root environment that contains all available packages
that have been installed on the system. Lets take a look at the available packages
inside of Anaconda.

Issue the following command:

```
conda list
```

You will see a long list of available packages. Each row lists information about a 
given package. Each package has a version and the location they were installed from.

```
# packages in environment at /usr/lpp/IBM/izoda/anaconda:
#
alabaster                 0.7.10                   py36_0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
alembic                   0.9.1                    py36_0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
anaconda                  1.1.0                         6    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
anaconda-client           1.2.2                    py36_0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
...
xlwt                      1.2.0                    py36_0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
xz                        5.2.3                         0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
yaml                      0.1.6                         1    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
zeromq                    4.2.1                         2    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
zlib                      1.2.11                        1    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
```

Most times you do not need all of these packages, or you may require a certain version
of a package. This is where Anaconda environments come into play. Lets start by creating
an environment that only contains python in it.

Issue the following command:

```
conda create -n myEnv python
```

As you can see in the command, we are creating an environment, with a name myEnv,
followed by a list of packages we want installed. In this case, only python.

Upon entering the command you will be prompted about installing packages and their
dependencies.

```
Fetching package metadata .....
Solving package specifications: .

Package plan for installation in environment /sharelab/sharb01/.conda/envs/myEnv:

The following NEW packages will be INSTALLED:

    appdirs:    1.4.3-py36_0  file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    bzip2:      1.0.6-2       file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    gettext:    0.19.8.1-0    file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    libffi:     3.2.1-3       file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    ncurses:    6.0-1         file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    openssl:    1.0.2k-3      file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    packaging:  16.8-py36_0   file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    pip:        9.0.1-py36_0  file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    pyparsing:  2.2.0-py36_0  file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    python:     3.6.1-24      file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    readline:   6.3-2         file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    setuptools: 34.3.3-py36_0 file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    six:        1.10.0-py36_0 file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    sqlite:     3.14.2-0      file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    wheel:      0.29.0-py36_0 file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    xz:         5.2.3-0       file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    zlib:       1.2.11-1      file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA

Proceed ([y]/n)? 
```

Type `y` followed by `ENTER`.

```
Proceed ([y]/n)? y

#
# To activate this environment, use:
# > source activate myEnv
#
# To deactivate this environment, use:
# > source deactivate myEnv
#
```

When completed, you will have the new environment was created, but at this
time you will still be in the root environment. In order to get into your 
new environment, we need to activate it. 

Issue the following command:

```
source activate myEnv
```

Upon entering this command, you will see that the prompt is now prefixed with the
name of your environment.

```
(myEnv) MVS1:XXXXXX:../izodalab/anacondalab/notebooks: >
```

Lets verify that python is available in our environment.

Issue the following command:

```
python
```

Our python interpreter starts up without any issues. Now lets try to import a 
package we did a little while ago.

Issue the following command:

```
import numpy as np
```

You will get an error, stating no module available. It worked previously because
we were in the root environment, containing all packages. Now that we're in our
newEnv environment, we no longer have this package.

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ModuleNotFoundError: No module named 'numpy'
```

However, we can simply install this package into our environment. Exit out of 
the python interpreter.

Issue the following commands:

```
quit()
```

Once you've returned to the bash shell, install the numpy package we had imported
prior. 

Issue the following command:

```
conda install numpy
```

This command will install the listed packages. Again you will be prompted to about
installing the packages and dependencies. Again type `y` followed by `ENTER`.

```
Fetching package metadata .....
Solving package specifications: .

Package plan for installation in environment /sharelab/sharb01/.conda/envs/myEnv:

The following NEW packages will be INSTALLED:

    f2c:         1.0-3         file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    getopt:      1.1.6-0       file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    lapack:      3.6.1-3       file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    numpy:       1.12.1-py36_8 file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA
    xlc-wrapper: 0.4-2         file:///usr/lpp/IBM/izoda/anaconda/channels/IzODA

Proceed ([y]/n)? y
```

Now that we've installed this package and its dependencies, lets return to the 
python interpreter and attempt to import again.

Issue the following commands:

```
python

# Interpreter started

import numpy as np
```

As you can see, we no longer have an error related to a missing package.  

Lets return to the bash shell.

Issue the following command:

```
quit()
```

When you're done working inside an environment you can return to the root environment.
 
Issue the following command:
 
```
source deactivate
```
 
You will see that the prefix to your prompt has disappeared. At this point you have
returned to the root environment.

### Python through a jupyter notebook
#### Starting the jupyter notebook for Python
A popular method to interact with python is through a jupyter notebook. If you have
not worked with jupyter notebooks before, you can visit the 
[jupyter notebook reference section](#jupyter-notebook-reference) for a basic 
description and usage of jupyter notebooks.

Issue the following command to launch a jupyter notebook server:

```
jupyter notebook
```

After executing the command, a notebook server will launch and provide you with the host URL.
You will want to copy and paste this URL into your browser.

*Note: This ssh session just remain open while you are using the jupyter notebook.*

#### Viewing the Python jupyter notebooks
Lets see how python within the jupyter notebook can help us find the culprit that is filling
up our logstreams that we discussed earlier. 
 
Launch the notebook `choose-your-path-python.ipynb`. Once loaded, you will see comments
describing what each cell will contain. Please copy and add each code snippet to the
appropriate cells. Then run them after adding the code snippet.

We will first want to import the packages that we'll be using. The following packages
will be imported:
- pandas
    - Needed to load our json files into dataframes
- matplotlib
    - Needed for plotting graphs from our data.
    
Issue the following command in the cell:

```
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```

There are two json files that have pre-populated for you. 
- SMF30_CAS.json
  - Contains the CPU Account Section of SMF30 records.
- SMF30_ID.json
  - Contains the Identification Section of SMF30 records. 
  
Lets start by loading our first json file, the SMF30 CPU Accounting 
Section into a dataframe using pandas and displaying some rows.

Issue the following command in the cell:

```
cas_df = pd.read_json(path_or_buf="../../data/SMF30_CAS.json")
cas_df.head()
```

Do the same for our second json file, the SMF30 Identification
Section.

Issue the following command in the cell:

```
id_df = pd.read_json(path_or_buf="../../data/SMF30_ID.json")
id_df.head()
```

Now that we've loaded these two separate files into two separate
DataFrames, lets join the two dataframes and only look at MIPs usage 
and Jobname of each record.

Issue the following command in the cell:

```
smf_df = cas_df[['SMF30CPT']].join(id_df[['SMF30JBN']])
smf_df.head(30)
```

As you can see sometimes a single job may have multiple records 
generated. We can group these together and get a count of the 
number of records each job created.

Issue the following command in the cell:

```
user_usage_df = smf_df.groupby('SMF30JBN').sum()
user_usage_df.head(15)
```

Now lets use matplotlib to take a look at our data visually by 
plotting a graph.

Issue the following command in the cell:

```
my_plot = user_usage_df.plot(kind='bar')
plt.axes().get_xaxis().set_visible(False)
```

In our grab, we can see a spike, so there seems to be some sort of
job or jobs that generated a large number of records that could have
been filling up our logstreams. Lets sort our data and see if we can
come up with something.

Issue the following command in the cell:

```
user_usage_sort_df = user_usage_df.sort_values(by='SMF30CPT', ascending=False).head(10)
user_usage_sort_df
```

After we have organized and sorted our data, we can clearly see 
what our culprit jobs are. Lets see the magnitude of the problem by
graphing our dataframe into pie graph.

Issue the following command in the cell:

```
plot = user_usage_df.plot.pie(y="SMF30CPT")
```

The magnitude of the problem is clealy shown here that SATURN* jobs
have created the most records compared to anyone else on the system.
This ended up being the SATURN user having a Unix process stuck in an 
infinite loop, generating a number of SMF30 records through multiple
jobs running.

Here we can see, some simple Mappings and Dataset investigation can 
help you pinpoint an anomoly or cause of a problem on your system. 
With the added benefit of graphs, it can aide in providing you 
direction in your search.

**Congratulations! You have completed the Anaconda and Python Lab.**

Thank you for choosing to participate in this lab today, you may:
- Try looking at a couple other pre-populated notebooks available in the jupyter notebook
server simply by closing out of this notebook by going to `File` -> `Close and Halt`.
- Trying out the Spark and Scala Lab. *Please let your lab instructor know
so we can reset your environment for you.*
___

## References
### Jupyter notebook reference
[jupyter-reference-notebook-cell-run]: images/jupyter-reference-notebook-cell-run.png "jupyter-reference-notebook-cell-run"
[jupyter-reference-notebook-overview]: images/jupyter-reference-notebook-overview.png "jupyter-reference-notebook-overview"
[jupyter-reference-landing-page-overview]: images/jupyter-reference-landing-page-overview.png "jupyter-reference-landing-page-overview"
#### About jupyter
Jupyter notebook is a web application that allows you to create and share documents 
that contain live code, equations, visualizations and narrative text. A number of
kernels are supported, including Apache Toree, which interacts with Apache Spark, and
the Python kernel, which interacts with the python interpreter.

Within this reference page, you will learn how to:
- Launch a jupyter notebook server.
- Launch a jupyter notebook.
- Run a cell containing a snippet of code.
- Stop a jupyter notebook.

#### Launching the jupyter notebook server
Issue the following command to launch a jupyter notebook server:

```
jupyter notebook
```

After executing the command, a notebook will launch and provide you with the host URL.
You will want to copy and paste this URL into your browser.

```
[I 20:18:55.331 NotebookApp] Serving notebooks from local directory: ../izodalab/sparklab/notebooks
[I 20:18:55.331 NotebookApp] 0 active kernels 
[I 20:18:55.331 NotebookApp] The Jupyter Notebook is running at: http://mvs1.centers.ihost.com:XXXX/?token=XXXXXXXXXXXXXXX
[I 20:18:55.331 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 20:18:55.331 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://mvs1.centers.ihost.com:XXXX/?token=XXXXXXXXXXXXXXX
```

*Note: This ssh session just remain open while you are using the jupyter notebook.*

#### Viewing the jupyter notebook server
When you first visit the url provided by launching the jupyter notebook, you will 
be brought to the landing page. The page will look like this:

!["jupyter-reference-landing-page-overview"][jupyter-reference-landing-page-overview]

On the landing page, you will find the following:
- **A**
    - Files and Directories starting from the current working directory that you
    started the jupyter notebook server from.
- **B**
    - Upload new files, such as data or jupyter notebooks, from your local machine.
- **C**
    - Create a new file, directory, or notebook with specific kernel.


#### Starting a jupyter notebook    
Lets open the jupyter-reference notebook by clicking on the file, `jupyter-reference.ipynb`.

!["jupyter-reference-notebook-overview"][jupyter-reference-notebook-overview]

On the jupyter notebook page, we have a number of sections available to us:
- **A**
    - This is the name of the file.
- **B**
    - This is the current kernel that we're running with.
- **C**
    - As part of the toolbar, this is one way to run code in a cell.
- **D**
    - This is a cell, this is where we can place code snippets, text, or images.
    
Lets launch the cell. There are two ways to do so, you can click on the available 
button in the toolbar, or you can press `SHIFT`+`ENTER` on your keyboard while
the cursor is within the cell.

!["jupyter-reference-notebook-cell-run"][jupyter-reference-notebook-cell-run]

As you can see in the image, a number appears next to the cell. In our example,
it runs very quickly. If it were to be a larger snippet of code or have some
calculations to perform, it may take longer. When the kernel is busy and processing
a cell, it will have an asterisk where the number is. The number indicates in
what order the cells were run.

Underneath the cell we can see the output of the cell, if there is any.

#### Stopping a jupyter notebook
Now that we've seen the basics of the jupyter notebook server and running cells
inside of a jupyter notebook, lets stop our currently running notebook.

Go to `File` -> `Close and Halt` to stop your kernel and exit out of your notebook.
This will bring you back to the jupyter notebook server landing page.

Now return to your lab from where you left off:

- [Spark / Scala Lab](#viewing-the-spark-jupyter-notebooks)
- [Anaconda / Python Lab](#viewing-the-python-jupyter-notebooks)
