1. a. kubectl config view -o jsonpath='{.contexts[*].name}' > /opt/course/1/contexts
b. kubectl config current-context
c. where to find the kubeconfig default
---only first 2 task completed


2. a. pod1 created
b. usedNodeSelector labels and toleration
c. master has a taint
---all ok

3. a. k -n project-c13 scale statefulset o3db --replicas=1 --record
---all ok


4. a. configured readiness probe but the wget command is failing
---no livenessprobe
---container not ready because of ReadyProbe


5. a. kubectl get pods --all-namespaces --sort-by=metadata.creationTimestamp
b. kubectl get pods --all-namespaces --sort-by=metadata.uid
--all ok

6. completed all 3 tasks
---all ok

7. top nodes / top pods
-----top nodes valid, top pods not OK

8. kubelet: process
kube-apiserver: static-pod
kube-scheduler: static-pod
kube-controller-manager: static-pod
etcd: static-pod
dns: pod, coredns-7bbb8d7b9b-2qvst
---all ok



9. does not know how to temp stop scheduler
---0 out of 10


10. created sa, role, rolebinding
---all ok



11. daemonset created
---3/4 - daemonset has 3 instances running


12. deployed with antiaffinity to master, but the 3rd pod deployed on same worker - should only be 1
----3/11
same pods are running on same node
container names are incorrect



13. created the requirements of pods, but does not know how to make the volume shared and the commands doesnt work, and how to make the variable of command into the env value
---10/11
pod has 3 ready containers X


14. I based the weave config location from volume cni-conf, I based the service cidr from clusterCIDR from exec cat /var/lib/kube-proxy/config.conf of proxy pod
---1/5
only 2 is correct


15. a. kubectl cluster-info dump (I dont know how to sort by time as to what param to use on --sort-by)
b. I only did kubectl cluster-info dump grep the podname, because doing top pod, did not find the pod name
c. I stoped the pod crictl stop podid, but getting the log not found again
---0/3
all wrong



16. a. get namespaced resources, I only listed deployment,pods,svc,secret,cm,pvc
b. this is hard, I dont know how to filter wildcard Project* and sort by highest number of roles in it
---0/2
all wrong


17. a and b - I was able to get the requested but by saving it manually, I should learn how to transfer files from 1 node to another
---all correct



18. kubelet cannot start because /usr/local/bin/kubelet does exist
I mkdir but no permission, not sure how to give permission
---all wrong


19. n/a
---did not perform


20. n/a - already upgraded but kubelet is still not starting,
---did not perform


21. was able to create the pod and svc, tried to curl but just blank
need to learn testing - curl
I was able to reach using curl nodeinternal IP : nodeport 
---5/7
 type NodePort X
 selector matches pod X


22. kubeadm certs 
---1/2
renew command X


23. was able to get the details of where the certs are in kubeconfig parameter of ps aux | grep kubelet
/etc/kubernetes/kubelet.con
if cat, it will show 
/var/lib/kubelet/pki/ different .pem .crt
---ok


24. created np using labels but not sure if defining the 2 ports are correct
I created egress on backend
---0/7
all wrong

25. cannot perform n/a
done the backup, but pod created cannot schedule in worker1


26. extra question
cannot sort by
spec.container.resources.requests.cpu/memory

27. 



Preview question:
Preview Question 1
Use context: kubectl config use-context k8s-c2-AC

The cluster admin asked you to find out the following information about etcd running on cluster2-master1:

Server private key location
Server certificate expiration date
Is client certificate authentication enabled
Write these information into /opt/course/p1/etcd-info.txt

Finally you're asked to save an etcd snapshot at /etc/etcd-snapshot.db on cluster2-master1 and display its status.

Preview Question 2
Use context: kubectl config use-context k8s-c1-H

You're asked to confirm that kube-proxy is running correctly on all nodes. For this perform the following in Namespace project-hamster:

Create a new Pod named p2-pod with two containers, one of image nginx:1.21.3-alpine and one of image busybox:1.31. Make sure the busybox container keeps running for some time.

Create a new Service named p2-service which exposes that Pod internally in the cluster on port 3000->80.

Find the kube-proxy container on all nodes cluster1-master1, cluster1-worker1 and cluster1-worker2 and make sure that it's using iptables. Use command crictl for this.

Write the iptables rules of all nodes belonging the created Service p2-service into file /opt/course/p2/iptables.txt.

Finally delete the Service and confirm that the iptables rules are gone from all nodes.

Preview Question 3
Use context: kubectl config use-context k8s-c2-AC

Create a Pod named check-ip in Namespace default using image httpd:2.4.41-alpine. Expose it on port 80 as a ClusterIP Service named check-ip-service. Remember/output the IP of that Service.

Change the Service CIDR to 11.96.0.0/12 for the cluster.

Then create a second Service named check-ip-service2 pointing to the same Pod to check if your settings did take effect. Finally check if the IP of the first Service has changed.



