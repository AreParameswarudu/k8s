Version1 (= KOPS-multinodeCluster-setup) uses t2.micro for worker nodes and are sometimes cannot handle load while exercising.  
So came up with slight changes in the setup.  

### Step1 
Launch Amazon Linux 2023 , t2.micro  and name it as **KOPS**.

Attach a IAM ROLE TE=EC2, Permisions = admin.  

Login to the **KOPS** instances.  
Set the Hostname for instances.  

### step2 
Edit the `.bashrc` file.  
Add the path `export PATH=$PATH:/usr/local/bin/` to the file.  
Finally, run the file to save and reflect changes.
```
vi .bashrc
```
```
export PATH=$PATH:/usr/local/bin/
```
```
source .bashrc
```

### Step3 
Generate a key to allote to the cluster's instances so as to login to those machines.  
Change the name or simply copy the key to a pem/pub file.  
Add executable rights to the users for the file.
```
ssh-keygen
```
```
cp /root/.ssh/id_rsa.pub my-keypair.pub
```
```
chmod 777 my-keypair.pub
```

### step4 
KOPS script
```
vi kops.sh
```

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
wget https://github.com/kubernetes/kops/releases/download/v1.32.0/kops-linux-amd64
chmod +x kops-linux-amd64 kubectl
mv kubectl /usr/local/bin/kubectl
mv kops-linux-amd64 /usr/local/bin/kops
aws s3api create-bucket --bucket param-kops-testbkt143.k8s.local --region ap-south-1 --create-bucket-configuration LocationConstraint=ap-south-1
aws s3api put-bucket-versioning --bucket param-kops-testbkt143.k8s.local --region ap-south-1 --versioning-configuration Status=Enabled
export KOPS_STATE_STORE=s3://param-kops-testbkt143.k8s.local
kops create cluster --name=param.k8s.local --zones=ap-south-1a --control-plane-count=1 --control-plane-size=t3.medium --node-count=2 --node-size=t3.small --node-volume-size=20 --control-plane-volume-size=20 --ssh-public-key=my-keypair.pub --image=ami-0f918f7e67a3323f0 --networking=calico
kops update cluster --name param.k8s.local --yes --admin
```

Create the K8S cluster with kops by running above script.
```
sh kops.sh
```

Following command is necessesary 
```
export KOPS_STATE_STORE=s3://param-kops-testbkt143.k8s.local
```

Lets validate the cluster.
```
kops validate cluster --wait 10m
```


### Cleanup 
This is the command to be used for closing/cleaningup the cluster.  
```
kops delete cluster --name param.k8s.local --yes
```
