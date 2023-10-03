# Lab 53 - creating gitlab-ci.yml
The objective of this lab is to **understand how to read and construct** `gitlab-ci.yml`, the file responsible for triggering CI processes within GitLab. When you add a `.gitlab-ci.yml` file to the root of your repository, GitLab detects it and an application called GitLab Runner runs the scripts defined in the jobs. It goes without saying, in addition to the `.gitlab-ci.yml` file, you'll also need application code hosted in a Git repository for this feature to be useful.

In the `.gitlab-ci.yml` file, you can define:

- The scripts you want to run.
- Other configuration files and templates you want to include.
- Dependencies and caches.
- The commands you want to run in sequence and those you want to run in parallel.
- The location to deploy your application to.
- Whether you want to run the scripts automatically or trigger any of them manually.

The scripts are grouped into **jobs**, and jobs run as a larger **pipeline**. These relationships are expressed using a standard called, YAML. Before you try reading a copy of `.gitlab-ci.yml`, review the [YAML Specification](https://yaml.org/spec/1.2/spec.html).

- Here is an example `.gitlab-ci.yaml` file
```yaml
stages:
  - build
  - test

build-code-job:
  stage: build
  script:
    - echo "Check the ruby version, then build some Ruby project files:"
    - ruby -v
    - rake

test-code-job1:
  stage: test
  script:
    - echo "If the files are built successfully, test some files with one command:"
    - rake test1

test-code-job2:
  stage: test
  script:
    - echo "If the files are built successfully, test other files with a different command:"
    - rake test2
```
- **Q: How many stages are shown? What are they?**
    - _A: Two (2), "build" and "test"._
- **Q: How many jobs are shown?**
    - _A: The full pipeline in the example is composed of three jobs, grouped into two stages, "build" and "test". The pipeline runs every time changes are pushed to any branch in the project._
- **Q: What order does it all run?**
    - _A: In this example, the "build-code-job" job in the "build" stage runs first. It outputs the Ruby version the job is using, then runs "rake" to build project files. If this job completes successfully, the two "test-code-job" jobs in the "test" stage start in parallel and run tests on the files._
- **Q: Do I have to write these files?**
    - _A: GitLab provides a series of ".gitlab-ci.yml" templates, and allows users to create their own templates. Therefor, depending on your ability level, it is possible you'll be writing these files, consuming these files, or a combination of both._
- .
# Kubernetes
# Lab 9 - Deploy Kubernetes using Ansible
The objective of this lab is to learn the Kubernetes components you will be working with. We will make sure we have IP connectivity to all of them and familiarize ourselves with the components that constitute the underlying Kubernetes cloud, the host IP addresses, and the host names. The lab environments you will be working in leverage the Alta3 Cloud Environment Services (ACES) (https://alta3.com/) to streamline provisioning of the computer infrastructure required to bootstrap a Kubernetes cluster.

The basic working components of Kubernetes are outlined here:  
[https://kubernetes.io/docs/concepts/overview/components/](https://kubernetes.io/docs/concepts/overview/components/)

Now it is time to deploy Kubernetes! To do this you will use an Ansible Playbook designed by Alta3. Ansible is an open source automation tool designed to automate the deployment of configurations and software. It's actually perfect for _deploying Kubernetes_.

You can find additional information about Ansible here:  
[https://alta3.com/overview-ansible](https://alta3.com/overview-ansible)

Let's take a look at your current environment. After the playbook runs, the following configuration will be observed.

- Our new environment is made of 4 VMs
```
+--------------------+    +--------------------+     +--------------------+
| Beachhead          |    | Controller         |     | Node-1             |
| bchd               |    |                    |     |                    |
|                    |    |                    |     |                    |
|                    |    |                    |     |                    |
+--------------------+    +--------------------+     +--------------------+

                                                     +--------------------+
                                                     | Node-2             |
                                                     |                    |
                                                     |                    |
                                                     |                    |
                                                     +--------------------+
```

- This lab will oconfigure Controller, Node1 and Node2 to become nodes
- We want to verify connectivity throughout our environment. Never used fping? https://fping.org/ is a program to send ICMP echo probes to network hosts. It's similar to ping, but enjoys much better performance when pinging multiple hosts.
```
fping -A controller  node-1 node-2 
```

- Let's initialize your current Kubernetes environment (this may take a while, somewhere around eight minutes). If you are interested in what changes occurred, look at the playbook readout. In short, running the playbook sets up your networking, puts certs in the right location, and deploys etcd, nodes, controllers, services, and load balancer.
- Run the Ansible playbook. Ansible is a tool that allows users to describe the state they want to achieve in code using a YAML document called a 'playbook.' Ansible then works to ensure that the state described in the playbook is achieved on the target machines. In short, Ansible makes it easy for anyone to write complex installation routines. The playbook you are about to run, **main.yml**, will install Kubernetes. The inventory file, **hosts.yml**, instructs Ansible to install Kubernetes on specific targets.
```
ansible-playbook main.yml
```

- Kubectl is a command line tool for controlling Kubernetes clusters. Issue **kubectl get nodes** list the nodes.
```
kubectl get nodes
```

- Use the **kubectl describe nodes** command to view detailed information about the nodes. Your instructor can help you parse back through this information, as it may scroll off your screen. You will learn more about the **get** and **describe** commands in subsequent labs.
```
kubectl describe nodes
```

- Next lets verify that this works as expected by creating our own `pod`, using our own image. We will discuss this in more depth later on.
```
apiVersion: v1
kind: Pod
metadata:
  name: myfirstpod
spec:
  containers:
  - name: webster
    image: registry.gitlab.com/alta3/webby:latest
```

- A kubernetes pod is a wrapper around a container
# Kubernetes
- is a container organizer
- a clutser is a cluster of nodes
## Nodes
- a node is a physical or VM machines
- provides the resources to run pods
- services on a node include
	- container runtime nvironment
	- kubelet
	- kube-proxy

- The **controller node** is the computer in your cluster that is tasked with being the amdinistrator 
	- they manage all the other nodes in the cluster
	- schedules Pods to run on those nodes
	- need at least 1 controller node on the cluster, but there are usually 3
- controller node usually contains:
	- api server
	- controller manager server
	- etcd
- .
```
kubectl get nodes
```
- will display the worker nodes for you

- kubernetes will set up docker details for you

- Kubernetes allow us to use containers as microserviceds
- kubernetes will wrap a container in a Pod
- pods are created inside nodes, and nodes make up a cluster
![[Pasted image 20230815135837.png]]
![[Pasted image 20230815141749.png]]

- Pods allow containers to share ports, domainsm IPC channels
![[Pasted image 20230815142058.png]]
![[Pasted image 20230815142114.png]]
![[Pasted image 20230815142230.png]]
![[Pasted image 20230815142323.png]]

## Pods
![[Pasted image 20230815142719.png]]

# Kubernetes manifest
![[Pasted image 20230815144727.png]]
![[Pasted image 20230815145148.png]]

- apply a kubernetes manifest against a cluster tor create a pod
```
kubectl apply -f [yaml file name]
```

![[Pasted image 20230815145950.png]]

# Lab 12 - Create an configure basic pods
The purpose of this lab is to create a Pod manifest which will then be used to create a Pod.

You have already launched Pods, however, you have yet to actually write any **Pod Manifests**. These **Pod manifests** are what declaratively define all of the information necessary to launch a Pod. The kubectl command is used to create many different Kubernetes resource objects, not just Pods.

To see the list of Kubernetes Resource Types, click this link:  
[https://kubernetes.io/docs/reference/kubectl/overview/#resource-types](https://kubernetes.io/docs/reference/kubectl/overview/#resource-types)

Pod manifests can be written using YAML or JSON. YAML is usually preferred because it is more human-readable and has the ability to add comments. Manifests should be written in the same sense as source code. Manifests include some key fields and attributes, such as: a metadata section for the pod and label description, a spec section for describing volumes, and a list of containers that will all run in the Pod.

If the concept of Pods is confusing, check out the following documentation:  
[https://kubernetes.io/docs/concepts/workloads/pods/pod/](https://kubernetes.io/docs/concepts/workloads/pods/pod/)

For assistance with writing manifests, here are some more resources for you to peruse through manifest examples:  
[https://static.alta3.com/files/Manifests.pdf](https://static.alta3.com/files/Manifests.pdf)

An extended pod manifest example:  
[https://static.alta3.com/files/ExtendedManifest.pdf](https://static.alta3.com/files/ExtendedManifest.pdf)

### [Questions](https://live.alta3.com/content/kubernetes/labs/content/kubernetes/pods/Create-and-Configure-Basic-Pods.html#questions)

**What is the difference between a Pod and a Pod manifest?**  
_A Pod is what is created by Kubernetes. To create a Pod, a user can use the command line or create a manifest, which is typically written in YAML_

**Can you name the steps (without looking) to deploy your Pod, from start to finish?**  
_Create a manifest, then issue "kubectl create -f "_

**Recall what's included in a Pod manifest. What can the value of "kind" be?**  
_The value of "kind" is the object you want Kubernetes to create. Some of the ones we will study in this course include: "Pod", "Deployment", "ReplicationController" (Manages Pods), "DeploymentController" (Manages Pods), "StatefulSets", "DaemonSets", "Services", "ConfigMaps", and "Volumes"._

- A pod manifest _must_ contain certain keys. Since this is YAML, these keys can be in any order, but the classic approach is alphabetical order- this approach is convenient because alphabetical order makes sense for reading the values.
```yaml
apiVersion:
kind:
metadata:
spec:

```
- **apiVersion:** is a required `key` field. It defines the Kubernetes API you are using.  
- **kind:** For this lab, the value field will be either `Pod` or `Deployment`.  
- **metadata:** A required `key` that helps uniquely identify the object. Subkeys are `name`, `uid`, `labels`, `namespace`
- **spec:** Specifies the desired state of an object. If a spec is deleted, the object will be purged from the system.  
- **status:** PURELY FYI - A system generated value that shows the actual state of the object at the current time.

- Focus on that key `kind:`. The `kind:` key describes the object you want Kubernetes to create. Curious what the **entire** list of objects is? Issue the following command. The far right column reflects the 'value' you could place to the right of the key `kind:` within your manifest.
```
kubectl api-resources
```

## boot up a kubernetes pod
```bash
kubectl apply -f [yaml file name]
```

- example kubernetes manifest file
```yaml
---
apiVersion:      # the apiVersion to use
kind:            # example Pod / Deployment
metadata:
  name:          # name to apply
  labels:
    name:        # label name
spec:
  containers:
  - name:        # name of container
    image:       # image to use if no URL provided hub.docker.com is used
    ports:
    - name:      # port name
      containerPort:      # access on this port (int)
      protocol:           # TCP or UDP
```

```yaml
batcat mycode/yaml/webby-pod01.yaml 
───────┬────────────────────────────────────────────────────
       │ File: mycode/yaml/webby-pod01.yaml
───────┼────────────────────────────────────────────────────
   1   │ apiVersion: v1
   2   │ kind: Pod
   3   │ metadata:
   4   │   name: webservice01
   5   │ spec:
   6   │   containers:
   7   │   - name: webby
   8   │     image: registry.gitlab.com/alta3/webby
   9   │     ports:
  10   │     - name: web
  11   │       containerPort: 8888
  12   │       protocol: TCP
  13   │ 
───────┴────────────────────────────────────────────────────
```

### list all pods
```
kubectl get pods
```
### delete a pod
```bash
kubectl delete -f ~/mycode/yaml/simpleservice-empty.yaml
```

