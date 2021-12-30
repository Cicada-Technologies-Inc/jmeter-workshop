# JMeter

JMeter is a tool designed to load test Web Applications. However, it can also be used to run simple functional tests.

It simulates a group of users sending requests to a target server and returns statistics of the performance of the target application.

## Installation

### Mac

Macports:
```
sudo port install jmeter
```

## Usage

### Run JMeter

Run the jmeter binary.
```
jmeter
```

The jmeter gui will open.

### Configure the test suite through the GUI

A JMeter project consists in a tree of `Test Elements` whose root is a `Test Plan`

There are several categories of `Test Elements` in JMeter:

- Samplers used to send request of various kinds (HTTP, JDBC, JMS, etc.),
- Logic Controllers like If conditions, containers and transactions,
- Post Processors, mostly variable extractors and scripts,
- Assertions like JSON or XPATH assertions,
- Timers to slow down the test execution,
- Listeners to debug the test and view results.

Check the different examples in this repo for reference:
- [hello_world](hello_world/README.md)



### Run test suite through GUI

Click the run button on the test

### Run test suite through command line

Execute the jmeter binary passing the test file and other options as arguments.
```
jmeter -n -l testresults.jtl –t HelloWorld.jmx 
```
-n: Run in non-gui mode  
-l: name of the file where results will be logged  
-j: name of the file where run log will be stored  
-t: Path to the file that contains the test plan  

Note: Ensure the -t parameter is passed at the end, as it can fail otherwise.


## Reference
<https://octoperf.com/blog/2018/06/05/jmeter-vs-soapui/#jmeter-scripting>
