# k8scenario.github.io

![](images/kube-fixit.PNG)

k8scenario/k8scenario is an open source tool for running Kubernetes Application Troubleshooting Workshops.

k8scenario/k8scenario contains
- the sources for a Go binary k8scenario
- the sources for scripts which implement the scenarii to be troubleshooted.

The k8scenario/k8scenario tool is a command-line application
- with a minimal textual menu to allow users to see what scenarii are available to run
- downloads a selected scenario
- deletes/recreates the *k8scenario* namespace
- installs the selected scenario into the *k8scenario* namespace
- provides some minimal hint of the task to perform/problem to fix
- loops until the "problem" is fixed

The k8scenario/k8scenario tool automatically downloads available scenarii as zip files from the [https://k8scenario.github.io](https://k8scenario.github.io) site.

The Go binary is also available from the [https://k8scenario.github.io](https://k8scenario.github.io) site.

## Downloading k8scenario

<details><summary>show</summary>

The tool can be downloaded from:

<a href="https://k8scenario.github.io/static/bin/k8scenario"> https://k8scenario.github.io/static/bin/k8scenario </a>

<br/>
<p>
<b>Note</b>: The tool operates on the <i>k8scenario</i> namespace which it creates/deletes for each scenario.  The tool - as written - requires full cluster permissions, so will likely not run on your production cluster - and it shouldn't !
</p>

<br/>
<p>
<b>Note</b>: A more complete version exists at <a href="https://mjbright.github.io/static/bin/k8scenario"> https://mjbright.github.io/static/bin/k8scenario </a>, but it is not open source as it is used for paid Kubernetes trainings
</p>
<!-- </p> -->
</details>


## Debugging resources

<details><summary>show</summary>
<!-- <p> -->

An excellent resource to introduce you to the process of debugging applications running on Kubernetes is the "Visual guide on troubleshooting Kubernetes deployments" available on the <i>@learnk8s</i> blog at
<a href="https://learnk8s.io/troubleshooting-deployments"> https://learnk8s.io/troubleshooting-deployments </a>.

<img src="https://learnk8s.io/a/36ab1a196436668c7dcc3aff1cb20821.svg" />

Follow <i>@learnk8s</i> on twitter at <a href="https://twitter.com/learnk8s"> https://twitter.com/learnk8s </a> for some excellent Kubernetes resources.

<!-- </p> -->
</details>

## Using k8scenario

k8scenario is a command line tool.

When the tool is launched, it downloads a list of available scenarii and presents the user with a very basic menu of available scenarii:

### Launching the tool

```bash
./bin/k8scenario
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

Did you see the hint?
```
# hint1:

Just launch a Pod (and/or a Deployment/ReplicaSet) with a name starting with 'basictest', e.g. basictest-xxxx in the namespace called k8scenario (created for you)

# subjects:
- pods, namespace
```

In the above example, it is sufficient to create a Pod with an appropriate name, and the simplest way to do this might be to create a Deployment as follows:
```bash
kubectl -n k8scenario create deploy basictest --image=mjbright/ckad-demo:1
```

**Note:**
- It is necessary to specify the namespace with the '*-n*' option, see **change context/namespace** below for an alternative
- For this particular task, there is no constraint on the image, so you can choose any available/accessible image

### Change context/namespace

<details><summary>show</summary>
<!-- <p> -->

<b>Note</b>: <b>WARNING</b> - the below assumes you are on a <i>vanilla</i> cluster, not a <i>production</i> cluster !

<br/>
<br/>
<br/>
Note that to eliminate the need to set the namespace on each command, it is possible to modify the namespace of the current context

<br/>
<br/>
<br/>
<pre>
kubectl config set-context $(kubectl config current-context) --namespace k8scenario
</pre>

<br/>
<br/>
<br/>
You can check the result as:
<pre>
kubectl config get-contexts
CURRENT   NAME          CLUSTER       AUTHINFO      NAMESPACE
*         kind-kind_2   kind-kind_2   kind-kind_2
</pre>

<br/>
After use you should reset using

<pre>
kubectl config set-context $(kubectl config current-context) --namespace ""
</pre>

<!-- </p> -->

</details>


### Wait for the tool to detect that the problem is fixed

The tool loops, checking periodically to see if the task is complete/problem is fixed.

When it detects completion it will report the fact and drop back to the selection menu as show below:

```
 - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ... - exit status: 1
[scenario0] - cluster broken - Sleeping <10> secs ...

---- [scenario0] WELL DONE !!!! - The scenario appears to be fixed !!!!

Available scenarii: 0  1  2  20  21  3  40
select scenario>>>
```

# Instructions for creating scenarii for the k8scenario tool

Refer to [Creating Scenarii](CREATING_SCENARII) to learn how to create and test your own scenarii.


