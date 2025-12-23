# Day 10/40 - Kubernetes Namespace Explained - CKA Full Course 2024


## Check out the video below for Day10 👇

[![Day10/40 - Kubernetes Namespace Explained - CKA Full Course 2024](https://img.youtube.com/vi/yVLXIydlU_0/sddefault.jpg)](https://youtu.be/yVLXIydlU_0)

### What is a Namespace in Kubernetes

- Provides isolation of resources
- Avoid accidental deletion/modification
- Separated by resource type or environment or domain and so on
- Resources can access each other in the same namespace with their first name by the DNS name for other namespaces


![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/d9ae95d5-7224-4d5b-b260-ed09fc53c6fd)

### How does the two pods communicate if they are in teh different namespace
   they uses fully qualified domain name.
  - default namespace is used if you dont specify the name.
  -k get all --namespace = kube-system
  - we have a pod core-dns,apiserver,controller manager is all are control plane components that is created inside      the kube system namespace.
  - kube-dns is a service which help in resolving ip to the hostname within the cluster

### how can we create a ns in a decalrative way
- create ns name_of_namespace
- Generally if you create a deployemt it would go in the default namespace so you have to specify the namespace name
-ex: k create deployemnt/nginx-demo --image=nginx -n demo




