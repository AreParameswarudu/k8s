#  Lets setup a multi node cluster 
> [!NOTE]
> This multi node cluster setup using AWS instances will cost.


For single node cluster ==> MiniKube.    
For multi node cluster ==> KOPS.  

We have several options for mutinode cluster, lets go with KOPS.

<img width="753" height="406" alt="image" src="https://github.com/user-attachments/assets/afcd2cf0-6a22-44fb-9b29-46b7f56bb35c" />


Go to AWS ec2 console,   
	* Choose a t2.micro instance with Amazon Linux OS.  
 	* Attach an IAM role with trusted entity = EC2, and Permissions = Administrative permissions.
create instance.

--------------
> [!NOTE]
> Use of IAM role:  
> As this EC2 instance is going to create a cluster of a Master node and desired no. of worker nodes, and need to talk to the nodes in the cluster. so unless we have an IAM role with required permission, we cannot do that.  
> The permission for IAM role can be fined tuned.  
> The resources that this role going to create were, _Ec2 instances_, _dedicated VPC_, _ig = Instance group_, _CIDER block_.
>
> As Amazon Linux machine inherently contains Amazon CLI, we no need to install it additionaly.
--------------

Login to the instance, now settup the KOPS and Kubectl in this machine.

To elivate the permission to root user.  
set hostname for machine.  
Reflect the changes.  
```
sudo -s
```
```
hostnamectl set-hostname kops
```
```
sudo -i
```

to edit the .bachrc file and add a path.  
add this pathe in the file.  
to reflect changes /compile the edit.  
```
vi .bashrc
```
```
export PATH=$PATH:/usr/local/bin/
```
```
source .bashrc
```

Now lets add a kops.sh file and run it to install kops, kubectl.

create a shell script.   
Run the script.  

```
vi kops.sh
```    

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl";
wget https://github.com/kubernetes/kops/releases/download/v1.32.0/kops-linux-amd64
chmod +x kops-linux-amd64 kubectl
mv kubectl /usr/local/bin/kubectl
mv kops-linux-amd64 /usr/local/bin/kops
aws s3api create-bucket --bucket param-kops-testbkt143.k8s.local --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
aws s3api put-bucket-versioning --bucket param-kops-testbkt143.k8s.local --region ap-south-1 --versioning-configuration Status=Enabled
export KOPS_STATE_STORE=s3://param-kops-testbkt143.k8s.local
kops create cluster --name param.k8s.local --zones ap-south-1a --image ami-0f918f7e67a3323f0  --control-plane-count=1 --control-plane-size t2.medium --node-count=2 --node-size t2.micro
kops update cluster --name param.k8s.local --yes --admin
```

------------
> [!NOTE]
> 1. _param-kops-testbkt143.k8s.local_ = is the bucket name, change it according to your needs and choose your region as needed.
> 2. _param.k8s.local_ = is the cluster name.
> 3. In order to setup the main and worker nodes, iam using AMI machine image, **ami-0f918f7e67a3323f0** which is UBUNTU machine's AMI.
> 4. For this setup we are uisng t2.medium for main node ( control plane) and t2.micro for worker node.
------------------

```
sh kops.sh
```

It may take a while to setup as it need to configure multiple things.  
You can see the setup configuring in the your AWS account.  

It is advised to run the following command again ( even though it was mentioned in the above script).  
```
export KOPS_STATE_STORE=s3://param-kops-testbkt143.k8s.local
```  

To verify the setup of cluster, use the following command  
```
kops validate cluster --wait 10m
```  
This command will return the status of cluster for a period of 10 mints.  
The setup may take more than 10 mints. So Wait for untill setup was configured and you can see all 3 nodes up and healthy.  

After sucessfull setup,  
```
kops get cluster
```
to get the cluster details.  

NOTE:
-----------------------------------
During setup, it will suggest us about few commands under _suggestions_ section, make a note of them.  
```
kops get cluster
```
is to get the details of cluster.

```
kops edit cluster param.k8s.local
```
is to edit the cluster

```
kops edit ig --name=param.k8s.local nodes-ap-south-1a
```
is to edit the node (worker node) instace group.

```
kops edit ig --name=param.k8s.local control-plane-ap-south-1a
```
is to edit the control-plane (master node) instance type.

------------------------------------------


Lets try to create some nodes using deployment.

`vi deployment.yml`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ib-deployment
  labels:
    app: bank
spec:
  replicas: 4
  selector:
    matchLabels:
      app: bank
  template:
    metadata:
      labels:
        app: bank
    spec:
      containers:
      - name: cont1
        image: nginx
```

```
kubectl create -f deployment.yml
```
to run the deployment.

lets see the nodes of the deployments.   
To get the pods of deployment use,
```
kubectl get pods
```
or  
```
kubectl get pods -o wide
```
To get the nodes of the cluster use,  
```
kubectl get nodes
```
or  
```
kubectl get nodes -o wide
```  

##### Lets look how scaling works for multi node cluster

**_Scale out_** :  
To replicate the pods to 10.  
```
kubectl scale deployment/ib-deployment --replicas=10
```    

Lets check the pods count,  
```
kubectl get pods -o wide
```

**_Scale in_** :  
```
kubectl scale deployment/ib-deployment --replicas=4
```

check again the pods count and the nodes that pods belongs to.  

> [!NOTE]
> Remember, not to exit the cluster without deleting it, if not you will be charged on a HUGE amount from AWS for using AWS resources as we are using AWS servers for setting up multinode cluster.

### **To delete the cluster**:  
```
kops delete cluster --name param.k8s.local --yes
```
_param.k8s.local_ is the name that I have used/mentioned for the cluster in the kops.sh script.  

> [!Caution]
> Do not delete any ec2 instance directly, if the KOPS instance is lost without deleting the cluster, then its a huge problem.  
> Make sure you deleted the cluster fom CLI, with above mentioned command.  
> Ensure that `kops get cluster`  should return no cluster found message. By that all the resources from cluster will be deleted automatically.  
> Only the _**S3 bucket**_ and _**EC2 instance**_ we have created initially were left.  

Manually delete the initially setup EC2 instance and S3 bucket. 
