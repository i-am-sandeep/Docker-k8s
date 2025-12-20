## Check out the task.md file for day07 task details

## Different ways of creating a Kubernetes object
- Imperative way ( Through command or API calls)
- Declarative way ( By creating manifest files)

![image](https://github.com/piyushsachdeva/CKA-2024/assets/40286378/b038c4d3-87b7-474d-a3aa-5983d978f885)


## Below is the sample pod YAML used in the video:

```YAML
# This is a sample pod yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    env: demo
    type: frontend
spec:
  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
```
<img width="602" height="232" alt="image" src="https://github.com/user-attachments/assets/7f3ea289-caf5-43ce-bf2d-3e77d416fd68" />

- kubectl explain pods
- kubectl config set-context kind-sancluster2
-  current-context   Display the current-context
-  delete-cluster    Delete the specified cluster from the kubeconfig
-  delete-context    Delete the specified context from the kubeconfig
-  delete-user       Delete the specified user from the kubeconfig
-  get-clusters      Display clusters defined in the kubeconfig
-  get-contexts      Describe one or many contexts
-  get-users         Display users defined in the kubeconfig
-  rename-context    Rename a context from the kubeconfig file
-  set               Set an individual value in a kubeconfig file
-  set-cluster       Set a cluster entry in kubeconfig
-  set-context       Set a context entry in kubeconfig
-  set-credentials   Set a user entry in kubeconfig
-  unset             Unset an individual value in a kubeconfig file
- use-context       Set the current-context in a kubeconfig file
-  view              Display merged kubeconfig settings or a specified kubec

-  kubectl run nginx --image=nginx --dry-run-client -o yaml > new-pod.yaml
-  k get pods --show-labels
## Status	Meaning
### ErrImagePull	- Image download failed
### ImagePullBackOff	- Kubernetes is retrying
### Running - 	✅ All good
