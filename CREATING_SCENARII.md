
![](images/kube-fixit.PNG)

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

If you only need to build your local copy, you can keep these default values.

# Instructions for creating new scenarii for the k8scenario tool

## TEMPLATE files

Some examples files are provided under SCENARII/TEMPLATE.

These files can be used as the basis for new scenarii.

## 1.create_deploy.yaml

An example yaml file for creating a deployment

## functions.rc

Contains bash utility functions to be used in SETUP_SCENARIO.sh, CHECK_SCENARIO.sh, EXCLUDE_FIX_SCENARIO.sh scripts

## SETUP_SCENARIO.sh

An example SETUP script

## CHECK_SCENARIO.sh

An example CHECK script to verify when a task/fix is completed

## EXCLUDE_SOLUTION.txt

For documentation only - not included in zip files

## HINTS.txt
Not used: could be the basis of extra hints - make notes here

## INSTRUCTIONS.txt
Instructions to be shown at start of scenario, and periodically

## TAGS.txt

Not currently used: Indicate subject/resources involved in this scenario

## QUIZ.txt

Not currently used: will be basis of quiz questions

## QUESTION.txt

TO be done


## Scenario zip files

A scenario is a zip file, to be downloaded by the k8scenario tool.

The source for scenarii are kept in the https://github.com/k8scenario/k8scenario repo, under the SCENARII folder.

Scenarii are each of one of the following types:
- tasks: You are told "*what to do*" and the tool detects when you have succeeded
- fix: You are told about an application problem and the tool detects when you have successfully fixed the problem
- [**not implemented**] question: you to recuperate some value from the platform/app, and will detect if you reply correctly or not
- [**not implemented**] quiz:

An example is "scenario0" which is a basic "hello world" 'task' scenario which is typically demonstrated to participants at the beginning of the workshop.

Scenario are stored in the repo under SCENARII, e.g. under SCENARII/scenario0.

The script *update_scenarii.sh* is used to create zip files and upload them to the github repository.

Note:
- The zip files include the files in the scenario directory, except for files whose names begin with '*EXCLUDE_*'


The tool will download the zip for the selected scenario and interpret the files there:
- To setup the scenario
  - It will "*kubectl apply*" any yaml files (.yml/.yaml)
  - If present, the tool will execute file "*SETUP_SCENARIO.sh*" before (with option --pre-yaml) and after (--post-yaml) applying the yaml files

- When running the scenario
  - Periodically show the contents of *INSTRUCTIONS.txt* to describe the task/fix
  - The tool will execute file "*CHECK_SCENARIO.sh*" to check the scenario
  - If this returns exit code 0 the scenario is deemed to be fixed

- Some other files exist in the zip which are not currently used:
  - CHALLENGE_TYPE.txt: specifies if a *task*, *fix* (LATER: *quiz*, *question*)
  - TAGS.txt: specifies tags applied to scenarii such as *Pods*, *Deployments* etc ...

Files which are present in the scenarii but excluded from the zip are
- EXCLUDE_SOLUTION.txt:    A ("*secret*") description of the scenario
- EXCLUDE_FIX_SCENARIO.sh  A script to fix the scenario (for *regression testing*, and for reference)
  
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

### CHECK_SCENARIO.sh

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

# Instructions for testing new scenarii - using *setup_check_fix_scenario.sh*

From the repo directory you can run a scenario using the *setup_check_fix_scenario.sh* script:
e.g.
    ```setup_check_fix_scenario.sh 1```

###  test_scenario_local.sh

Then run the script
    ```test_scenario_local.sh -a```

You should see indications of pass/fail

# switch_context.sh

Tool for switching between your current kubeconfig context and k8scenario context (with namespace '*k8scenario*')

# Update/upload zip files - update_scenarii.sh

# setup_check_fix_scenario.sh

#### You can run a local web server ...

TODO: Describe k8scenario arguments for a local server

Not needed can run directly from filesystem:
e.g.

```bash
k8scenario --zip SCENARIO/scenario0.zip
```


