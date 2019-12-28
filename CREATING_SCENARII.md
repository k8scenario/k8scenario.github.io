
<!-- TODO: TEMPORARILY DISABLING IMAGE for lower bandwidth reloads!
![](images/kube-fixit.PNG)
-->

# Instructions for building the k8scenario tool

You will only need to build the tool if you wish to add new features to the tool itself.

If you wish to do that you must first install a go compiler on your system, then clone the source repository:

```bash
git clone https://k8scenario/k8scenario
```

To build the tool, go to the source directory and run the build.sh script
```bash
cd k8scenario
./script/build.sh
```

The tool uses the information in .setup.rc file to determine where to place the binary, and where to install the binary and scenarii.

By default the following values are set in .setup.rc:

```bash
export DEFAULT_PUBURL="https://k8scenario.github.io/static/k8scenarii"
export DEFAULT_PUBDIR="$HOME//z/TOOLS/k8scenario.github.io"
export K8SCENARIO_BINARY=k8scenario
```

If you only need to build you local copy, you can keep these default values.

# Instructions for creating new scenarii for the k8scenario tool

A scenario is a zip file, to be downloaded by the k8scenario tool.

The source for scenarii are kept in the https://github.com/k8scenario/k8scenario repo, under the SCENARII folder.

An example is "scenario0" which is a basic "hello world" type of scenario which is typically demonstrated to participants at the beginning of the workshop.

Note that the tool looks for check.sh, INSTRUCTIONS.txt and any *yaml* files (file names ending in .yaml or .yml - case insensitive)

These are the *source* files for scenario0.

```bash
> ls -altr SCENARII/scenario0/
total 4
-rw-rw-rw- 1 mjb mjb  172 Sep 19 16:48 EXCLUDE_SOLUTION.txt
-rwxr-xr-x 1 mjb mjb   51 Oct 30 09:15 check.sh
-rwxr-xr-x 1 mjb mjb  204 Oct 30 09:41 INSTRUCTIONS.txt
-rwxrwxrwx 1 mjb mjb  263 Dec 28 23:23 EXCLUDE_FIX_SCENARIO.sh

The above folder is comprised of several files:
- check.sh
- INSTRUCTIONS.txt
- EXCLUDE_SOLUTION.txt
- EXCLUDE_FIX_SCENARIO.sh

Let's see how the files are treated by the tool:

### *.yaml

In this scenario there are no yaml files, as the task set is for *you* to perform a task - there is no real *problem* to fix.

Generally k8scenario will run "*kubectl apply*" on any yaml files it encounters in the zip file.

More precisely it will use the following command:
```bash
kubectl apply -n k8scenario -f <yaml-file>
```
Thus any yaml files you provide will be used to create the conditions for a scenario, typically some bad application configuration which is to be fixed.

### check.sh

This script is used to detect if the assigned task/problem has been solved.

The script will be invoked periodically by the tool.

If it returns a zero code then the tool will consider the problem fixed.

For this scenario the files contains:

```bash
kubectl -n k8scenario get pods | grep "^basictest.* Running "
```

This command will return 0 if a Pod named basictest (name starting with basictest) is in the k8scenario namespace and it is in the *Running* state.


 as you might expect *EXCLUDE_SOLUTION.txt* is excluded from the scenario zip.  This file is a description of the scenario solution.


### INSTRUCTIONS.txt

This text file will be used to provide a basic hint to users.

This should be just useful enough ... to give a hint.

At a later stage it is planned to allow multiple levels of hint allowing users to see the solution.

### EXCLUDE_SOLUTION.txt

This file will be excluded from the scenario zip file, but is here to document the solution

### EXCLUDE_FIX_SCENARIO.sh

This file will be excluded from the scenario zip file, but is here to provide a runnable script which would fix the task/problem.

# Building the zip files

The script ./script/update_scenarii.sh builds a zip file from each folder under SCENARII/scenario*.

Running this script without arguments will generate the scenario zip files.

Running with the '*-u*' argument will also upload them to 
https://k8scenario.github.io/static/k8scenarii

# Instructions for testing new scenarii for the k8scenario tool

You can run a local web server ...

## TO BE DONE

Describe k8scenario arguments for a local server




