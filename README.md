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

# 1. Taking part in a Troubleshooting Lab

To run the lab you will be provided with a Virtual Machine in the Cloud.

Your instructor will have provided login credentials.

**Note**: **AFTER THE WORKSHOP** you are free to run this workshop on your own laptop with a Kubernetes cluster installed - please don't do this during the workshop as the download of images will kill the conference WiFi ... **for everyone!!**

Once logged in to your VM you will need to
- Create a ~/bin directory and place it in your PATH
- Download some tools (kubectl, kind, k8scenario) to ~/bin
- Launch k8scenario

Create a local directory ~/bin and place it in your PATH as follows:

```bash
mkdir ~/bin
echo 'export PATH=~/bin:$PATH' >> ~/.bashrc
. ~/.bashrc
```

## 1.1 Installing Docker

```bash
sudo bash -i

  apt-get update -y
    apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    apt-key fingerprint 0EBFCD88
    add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io

    docker run hello-world

    usermod -aG docker ubuntu
    docker version
```

Now logout and login as user ubuntu and verify

```bash
    docker version
```

## 1.1.1 Verifying Docker

<details><summary>Verify Docker</summary>
Verify that Docker runs correctly on your system:

```bash
docker version
```

should show output similar to:
```
Client: Docker Engine - Community
 Version:           19.03.5
 API version:       1.40
 Go version:        go1.12.12
 Git commit:        633a0ea838
 Built:             Wed Nov 13 07:29:52 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.5
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.12
  Git commit:       633a0ea838
  Built:            Wed Nov 13 07:28:22 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.10
  GitCommit:        b34a5c8af56e510852c35414db4c1f4fa6172339
 runc:
  Version:          1.0.0-rc8+dev
  GitCommit:        3e425f80a8c931f88e6d94a8c831b9d5aa481657
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

Reference: https://docs.docker.com/install/linux/docker-ce/ubuntu/

</details>

## 1.2 Downloading kubectl

<details><summary>Install kubectl</summary>

The tool can be downloaded using the command:

```bash
wget -O ~/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```

**NOTE**: or in case of copy/paste problems do this as 2 separate commands:
```bash
RELEASE=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)

wget -O ~/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/$RELEASE/bin/linux/amd64/kubectl
```

Then check that kubectl is available:
```bash
chmod +x ~/bin/kubectl
kubectl version
```

Reference: https://kubernetes.io/docs/tasks/tools/install-kubectl/

</details>

## 1.3 Downloading KIND

<details><summary>Install KIND</summary>

The tool can be downloaded from:
https://github.com/kubernetes-sigs/kind/releases/download/v0.7.0/kind-linux-amd64

Download the kind executable and place it in your ~/bin directory

```bash
wget -O ~/bin/kind https://github.com/kubernetes-sigs/kind/releases/download/v0.7.0/kind-linux-amd64
chmod +x ~/bin/kind
kind version
```

</details>
   
## 1.5 Downloading k8scenario

<details><summary>show</summary>

The tool can be downloaded from: 
<a href="https://mjbright.github.io/static/bin/k8scenario"> https://mjbright.github.io/static/bin/k8scenario </a>

<b>Note</b>: The open source version of this repository is available at the URL below but has less scenarii available:
https://k8scenario.github.io/static/bin/k8scenario

<br/>
<p>
<b>Note</b>: The tool operates on the <i>k8scenario</i> namespace which it creates/deletes for each scenario.  The tool - as written - requires full cluster permissions, so will likely not run on your production cluster - and it shouldn't !
</p>

<br/>

```bash
wget -O ~/bin/k8scenario https://mjbright.github.io/static/bin/k8scenario

chmod +x ~/bin/k8scenario
k8scenario --version
```
</details>

# 2. Creating a KIND Kubernetes cluster

Create a file called '*kind_2.yaml*' with the following content:

```yaml
kind: Cluster
apiVersion: kind.sigs.k8s.io/v1alpha3
networking:
    disableDefaultCNI: true
    podSubnet: 192.168.0.0/16
nodes:
        - role: control-plane
        - role: worker
```

**Note:** We are disabling the default CNI, kindnet.  We will install Calico in a later step.

## 2.1 Deleting any existing cluster:

If you already have a cluster which you wish to replace, first delete that cluster.

You can list current clusters with the
```bash
kind get clusters
``` command.

Supposing that the cluster is named '*kind*', delete it as follows:
```bash
kind delete cluster --name kind
```

## 2.2 Create the 2-node cluster:

Create the 2-node cluster as shown, and check that the cluster is created.

```bash
kind create cluster --config kind_2.yaml
kind get clusters
```
Calico

**Note**: In case of a problem to create the cluster, delete the cluster and try again.

Verify that you have a 2-node cluster:
```bash
kubectl get nodes
```

You should see output similar to
```
Creating cluster "kind" ...
 ✓ Ensuring node image (kindest/node:v1.17.0) 🖼�
 ✓ Preparing nodes 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹��️️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-kind"
You can now use your cluster with:

kubectl cluster-info --context kind-kind

Have a nice day! 👋
```

Verify that you can access the cluster
```bash
 kubectl get nodes
```

You should see output similar to:
```
NAME                 STATUS   ROLES    AGE     VERSION
kind-control-plane   NotReady master   3m18s   v1.17.0
kind-worker          NotReady <none>   2m41s   v1.17.0
```

Note that the nodes are '*NotReady*' because we have not yet installed the Pod network - for this we need to install the Calico CNI networking extension.

# 2.3 Installing the Calico CNI

### Download the Calico manifest

We will install the *latest* 3.11 release

*See*: https://docs.projectcalico.org/v3.11/getting-started/kubernetes/installation/calico

Perform the following steps to install Calico

```bash
    wget -O calico_v3.11.yaml \
        https://docs.projectcalico.org/v3.11/manifests/calico.yaml

    kubectl apply -f calico_v3.11.yaml

    kubectl -n kube-system set env daemonset/calico-node FELIX_IGNORELOOSERPF=true
```

You should now see several calico Pods running in the kube-system namespace.

```bash
> kubectl get pods -n kube-system | grep calico
calico-kube-controllers-5b644bc49c-8h6pw     1/1     Running   0          125m
calico-node-f6j49                            1/1     Running   0          114m
calico-node-zjxl9                            1/1     Running   0          114m
```

Once those Pods are all in the Running state, you should see your nodes in the Ready state as shown below.

**Note:** It is possible that we need to restart the Pods if the Nodes do not go to the *Ready* state - we describe this below.


After a few seconds, but wait up to a minute, you should see output similar to:

```
> kubectl get nodes
NAME                 STATUS   ROLES    AGE     VERSION
kind-control-plane   Ready    master   3m18s   v1.17.0
kind-worker          Ready    <none>   2m41s   v1.17.0
```

You may now move on to section 3 to start the tool

### If your Nodes don't go to the Ready state (or calico Pods are not Running)

In this case we need to restart the '*calico-node-*' Calico Pods.

You can do this in one command as follows:

```bash
    kubectl -n kube-system delete $(kubectl get deploy,pods --no-headers -A
-l k8s-app=calico-node | awk '{ print $2; }'
```

Check now that your Nodes are all *Ready*.

# 2.4 Allowing Pods to be scheduled to the Master Node

By default Kubernetes installers configure Master nodes to disallow user (non-system) Pods.

Verify that this is the case for the *Master* node (kind-control-plane) but not for the *Worker* node (kind-worker):

```bash
  > kubectl describe node kind-control-plane | grep -i taint
  Taints:             node-role.kubernetes.io/master:NoSchedule

  > kubectl describe node kind-worker | grep -i taint
  Taints:             <none>
```

We will remove the taint on the *Master* to allow us to schedule Pods across all nodes
**Note**: This should not be done on production clusters

```bash
  > kubectl taint node kind-control-plane node-role.kubernetes.io/master-
  node/kind-control-plane untainted

  > kubectl describe node kind-control-plane | grep -i taint
  Taints:             <none>
```

# 3. Debugging resources

<details><summary>show</summary>
<!-- <p> -->

An excellent resource to introduce you to the process of debugging applications running on Kubernetes is the "Visual guide on troubleshooting Kubernetes deployments" available on the <i>@learnk8s</i> blog at
<a href="https://learnk8s.io/troubleshooting-deployments"> https://learnk8s.io/troubleshooting-deployments </a>.

<img src="https://learnk8s.io/a/36ab1a196436668c7dcc3aff1cb20821.svg" />

Follow <i>@learnk8s</i> on twitter at <a href="https://twitter.com/learnk8s"> https://twitter.com/learnk8s </a> for some excellent Kubernetes resources.

<!-- </p> -->
</details>

# 4. Using k8scenario

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
[scenario0]/1 - fix incomplete - Sleeping <10> secs ...
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

Alternatively, you can install the kubectx/kubens tool to change the namespace of the current context as
```bash
kubens k8scenario
```

You can install the tool from https://github.com/ahmetb/kubectx

**Note:** You can simply git clone that repository and copy the kubens script into your PATH at ~/bin/

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


