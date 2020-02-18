# EKS Getting Started Guide Configuration

This is the full configuration from https://www.terraform.io/docs/providers/aws/guides/eks-getting-started.html

See that guide for additional information.

NOTE: This full configuration utilizes the [Terraform http provider](https://www.terraform.io/docs/providers/http/index.html) to call out to determine your local workstation external IP for easily configuring EC2 Security Group access to the Kubernetes master servers. Feel free to replace this as necessary.

# Short notes :

Steps for run Terraform code
1. Install terraform and aws cli with kubectl package on system if not installed. 
2. Configure aws credentials using aws cli. 
3. Change region inside providers.tf file. 
4. Run terraform init to initialize. 
5. Run terraform plan -o terraform.out to save plan. 
6. Run terraform apply “terraform.out” 
7. Once eks cluster created, then use kubectl to install any applications on top of cluster such as Jenkins, nginx and others. 
8. Use kubectl commands to perform actions.

Setting Up kubectl:
You will need the configuration output from Terraform in order to use kubectl to interact with your new cluster. Create your kube configuration directory, and output the configuration from Terraform into the config file using the Terraform output command:

mkdir ~/.kube/

terraform output kubeconfig>~/.kube/config

You’ll need kubectl, a command line tool to run commands against Kubernetes clusters, for the next step. Installation instructions can be found here. Once you’ve got this installed, you’ll want to check to make sure that you’re connected to your cluster by running kubectl version. Your output may vary slightly here:

$ kubectl version

Client Version: version.Info{Major:"1", Minor:"12", GitVersion:"v1.12.1", GitCommit:"4ed3216f3ec431b140b1d899130a69fc671678f4", GitTreeState:"clean", BuildDate:"2018-10-05T16:46:06Z", GoVersion:"go1.10.4", Compiler:"gc", Platform:"linux/amd64"}

Server Version: version.Info{Major:"1", Minor:"12+", GitVersion:"v1.12.6-eks-d69f1b", GitCommit:"d69f1bf3669bf00b7f4a758e978e0e7a1e3a68f7", GitTreeState:"clean", BuildDate:"2019-02-28T20:26:10Z", GoVersion:"go1.10.8", Compiler:"gc", Platform:"linux/amd64"}

Now let’s add the ConfigMap to the cluster from Terraform as well. The ConfigMap is a Kubernetes configuration, in this case for granting access to our EKS cluster. This ConfigMap allows our ec2 instances in the cluster to communicate with the EKS master, as well as allowing our user account access to run commands against the cluster. You’ll run the Terraform output command to a file, and the kubectl apply command to apply that file:

terraform output config_map_aws_auth > configmap.yml
kubectl apply -f configmap.yml

Once this is complete, you should see your nodes from your autoscaling group either starting to join or joined to the cluster. Once the second column reads Ready the node can have deployments pushed to it. Again, your output may vary here:

$ kubectl get nodes -o wide
