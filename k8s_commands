<u>RUN a command in POD's container:</u>
How to debug a container? </br>
using kubectl exec command </br>
syntax: kubectl exec -it <podname> --container <containername> -- <commands to run> </br>
or.     kubectl exec -it <podname> -c <containername> -- <command to run inside container> </br>
example: kubectl exec -it myfirstpod --container firstconatiner -- env </br>
To list all the directories inside container: </br>
kubectl exec -it myfirstpod -c firstcontainer -- ls </br> 
<u> How to set command in POD's container sleep 500</u> </br>
set args in yaml file under spec --> container

---
apiVersion: v1
kind: Pod
metadata:
  labels:
    developer: minaxi
  name: myfirstpod
spec:
  containers:
    -
      env:
        -
          name: myname
          value: minaxi
      image: coolgourav147/nginx-custom
      name: firstcontainer
      args: ["sleep","50"]

========================================= <br />
kubectl delete pod <podname>
kubectl apply -f myfirstpod.yaml 
========================================= <br />
Create multiple containers in a pod: <br />

