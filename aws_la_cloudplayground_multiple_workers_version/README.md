
<h1>Setup Requirements for Terraform</h1><br/>

```
1. Terraform binary => This project has error in Terraform 0.13. "Destroy-time provisioners and their connection configurations may only reference attributes of the related resource, via 'self', 'count.index', or 'each.key'." at the line 61 of the file instances.tf 
   1) Get the binary file of Terraform 0.12.x. 
     # wget -c https://releases.hashicorp.com/terraform/0.12.28/terraform_0.12.28_linux_amd64.zip
   2) Unzip the file
     # unzip terraform_0.12.28_linux_amd64.zip
   3) Check the path and copy it to /usr/local/bin/
     # echo $PATH
     # sudo mv terraform /usr/local/bin/

2. Python3 & PIP needs to be installed on all nodes(on most , modern Linux systems it's available by default) 
   1) Check which version of python is installed in the machine.
    # whereis python| tr " " "\n"
   2) Check the available version of Pythone
    # yum list | grep python3
   3) If needed, installed the latest
    # yum -y install python3-pip  # or 
   4) If python 3 is not default then set it as default using alias
    # python --version
    # alias python=python3 
   5) Install boto3  -- It is needed for Ansible.
    #pip3 install boto3 --user


3. Ansible (install via pip) 
   # pip3 install ansible --user
    1) If pip3 is not installed, Install pip3 if it is not installed. 
    2) Download get-pip to current directory. It won't install anything, as of now
    # curl -O https://bootstrap.pypa.io/get-pip.py
    3) Use python3.6 to install pip
    # python3 get-pip.py
    
4. AWS CLI (install via pip) 
   # pip3 install awscli --user 
   
5. jq (install via package manager) - OPTIONAL # yum -y install jq
```

`This project has been tested on MacOS(Mojave), CentOS7. Author provides no guarantees for working with other OS's,
although the steps are generic enough that with little tweaking or even with no tweaking this might work 
on a range of OS's which support above 5 requirments.`

<h2>Notes and Instructions</h2><br />

*For Terraform Part*
```
The regional AWS providers are defined in providers.tf
Terraform configuration and backend is defined in backend.tf.


If you want to read and understand the deployment in sequence. Read through templates in the following order:
1. network_setup.tf
2. instances.tf --> local-exec provisioners in this templates kick-off Ansible playbooks in ansible_templates/
3. alb_acm.tf
4. dns.tf
```
*S3 Backend*
```
This project requires an S3 backend for storing Terraform state file, therefore in the terraform block in the backend.tf file you'll need to plug in the an actual bucket name before you can run "terraform init".
Please also note that the "terraform" block does not allow usage of variables so values HAVE to be hardcoded.
```
Sample command for bucket creation via CLI:
```
aws s3api create-bucket --bucket <YOUR-UNIQUE-BUCKET-NAME-GOES-HERE>
```

Example
```
aws s3api create-bucket --bucket myawesomebucketthatmayormaynotexistalready
```

<h2>Supplementary files </h2> <br />

```
1. ansible.cfg #A modified Ansible default config file with SSH host key checking and warnings disabled
2. aws_get_cp_hostedzone #An AWS CLI command for fetching your hosted zone for DNS part of this project
3. null_provisioners.tf #For setting up and deleting Ansible inventory files 
4. variables.tf #Defines variables and default values for them for the TF templates
5. outputs.tf #Defines the outputs presented at successful completion of execution of TF apply.
```

<h2>Ansible playbooks</h2><br />

```
1. cred-privkey.j2 #Jinja template for creating Jenkins credentials via Jenkins API call(populates private key)
2. install_jenkins.yaml #Playbook for Jenkins Master
3. install_worker.yaml #Playbook for Jenkins Worker
4. node.j2 #Jinja templates for registering worker node with Jenkins Master via Jenkins CLI(populates IP)
5. jenkins_auth #Provides the file with preset credentials for our Jenkins Master
```
