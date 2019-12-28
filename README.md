# k8scenario.github.io

![](images/kube-fixit.PNG)

k8scenario/k8scenario is an open source tool for running Kubernetes Application Troubleshooting Workshops.

k8scenario/k8scenario contains
- the sources for a Go binary k8scenario
- the sources for scripts which implement the scenarii to be troubleshooted.

The k8scenario/k8scenario tool is a command-line application
- with a minimal textual menu to allow users to see what scenarii are available to run
- downloads a selected scenario
- installs the selected scenario
- provides some minimal hint of the task to perform/problem to fix
- loops until the "problem" is fixed

The k8scenario/k8scenario tool downloads available scenarii as zip files from the https://k8scenario.github.io site.

The Go binary is also available from the https://k8scenario.github.io site.

## Downloading k8scenario

The tool can be downloaded from:

https://k8scenario.github.io/static/bin/k8scenario

**Note**: A more complete version exists at https://mjbright.github.io/static/bin/k8scenario, but it is not open source

**Note**: The tool operates on the *k8scenario* namespace which it creates/deletes for each scenario.  The tool - as written - requires full cluster permissions, so will likely not run on your production cluster - or at least it shouldn't !

## Debugging resources

An excellent resource to introduce you to the process of debugging applications running on Kubernetes is the "Visual guide on troubleshooting Kubernetes deployments" available on the *@learnk8s* blog at https://learnk8s.io/troubleshooting-deployments.

![](https://learnk8s.io/a/36ab1a196436668c7dcc3aff1cb20821.svg)

Follow *@learnk8s* on twitter at https://twitter.com/learnk8s for some excellent Kubernetes resources.

## Using k8scenario

k8scenario is a command line tool.

When the tool is launched, it downloads a list of available scenarii and presents the user with a very basic menu of available scenarii:

### Launching the tool

```bash
> ./bin/k8scenario
Version: 2019-Dec-28_20h33m29
serverUrl: https://k8scenario.github.io/static/k8scenarii
Downloading index.list
Available scenarii: 0  1  2  20  21  3  40
```
### Selecting a scenario:

To run scenario0, enter 0 and press the &lt;ENTER&gt; key and you will see that the tool
- first deletes the *k8scenario* namespace if it already exists
- creates the *k8scenario* namespace in which the task will be performed
- installs the 'problem scenario' into the namespace
- outputs a *hint message* which may be rather obscure ...
- loops until the problem is fixed
  - Note: There may be some transient Pods which are used to detect if the problem is fixed or not

```bash
select scenario>>> 0
Installing scenario ... into namespace k8scenario

namespace/k8scenario created

namespace/k8scenario created

k8scenario           Active   0s

k8scenario           Active   0s





# hint1:

Just launch a Pod (and/or a Deployment/ReplicaSet) with a name starting with 'basictest', e.g. basictest-xxxx in the namespace called k8scenario (created for you)

# subjects:
- pods, namespace

 - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ...
```

### Solve the problem

In the above example, it is sufficient to create a Pod with an appropriate name, and the simplest way to do this might be to create a Deployment as follows:


### Wait for the tool to detect that the problem is fixed

The tool

```
 - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ...

---- [scenario0] WELL DONE !!!! - The scenario appears to be fixed !!!!

Available scenarii: 0  1  2  20  21  3  40
select scenario>>>
```





# Instructions for creating scenarii for the k8scenario tool

A scenario is a zip file, to be downloaded by the k8scenario tool.

The source for scenarii is in the https://github.com/k8scenario/k8scenario directory, under the SCENARII folder.

An example is "scenario0" which is a basic "hello world" type of scenario which is typically demonstrated to participants at the beginning of the workshop.

These are the *source* files for scenario0, as you might expect *EXCLUDE_SOLUTION.txt* is excluded from the scenario zip.  This file is a description of the scenario solution.

```bash
> ls -altr SCENARII/scenario0/
total 4
-rw-rw-rw- 1 mjb mjb    0 Sep 17 11:18 9.empty.txt
-rw-rw-rw- 1 mjb mjb  545 Sep 19 16:30 functions.rc
-rw-rw-rw- 1 mjb mjb  172 Sep 19 16:48 EXCLUDE_SOLUTION.txt
-rwxr-xr-x 1 mjb mjb   51 Oct 30 09:15 check.sh
-rwxr-xr-x 1 mjb mjb  204 Oct 30 09:41 INSTRUCTIONS.txt
```

The above folder is comprised of several files:
- 9.empty.txt
- functions.rc
- EXCLUDE_SOLUTION.txt
- check.sh
- INSTRUCTIONS.txt


# Instructions for testing new scenarii for the k8scenario tool



## TODO:
- Create scenario YAML manifests (and optionally bash scripts) to
  - Check cluster functionality - OK with regards scenario
  - Install scenario
  - Check cluster functionality - broken
  - Uninstall scenario
  - Check cluster functionality - OK with regards scenario


# Instructions for using k8scenario tool

