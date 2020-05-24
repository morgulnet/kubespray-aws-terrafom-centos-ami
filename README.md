# kubespray-aws-terrafom-centos-ami

start from https://github.com/morgulnet/kubespray-aws-terrafom-centos-ami/tree/master/contrib/terraform/aws

1. Create Key-pair in aws  (in my case morgul)

https://eu-central-1.console.aws.amazon.com/ec2/v2/home?region=eu-central-1#KeyPairs:

chmod 600 morgul.pem

location - credentials.tfvars/AWS_SSH_KEY_NAME = "morgul"

eval $(ssh-agent)

ssh-add -D

ssh-add ~/morgul.pem

2. cd contrib/terraform/aws

3. terraform init

4. add info in credentials.tfvars 
AWS_ACCESS_KEY_ID = "AKIAasdasdaWREWERWERWEsdfsdfsdfsdfsdf"
AWS_SECRET_ACCESS_KEY = "IDSvtosadsdwe234f434f2fdadfascdascasc460Mhxd"

create user IAM with Programmatic access
Enables an access key ID and secret access key for the AWS API, CLI, SDK, and other development tools.

https://console.aws.amazon.com/iam/home?region=eu-central-1#/users

5. terraform apply --var-file="credentials.tfvars"

6. cd ~/kubespray-aws-terrafom-centos-ami

7. ansible-playbook -i inventory/hosts -e ansible_user=centos --become --become-user=root cluster.yml  --flush-cache -e kube_version=v1.16.3

Get error like

RUNNING HANDLER [container-engine/docker : Docker | reload docker] ***************************************************************************************************************
fatal: [ip-10-250-205-140.eu-central-1.compute.internal]: FAILED! => {"changed": false, "msg": "Unable to restart service docker: Job for docker.service failed because the control process exited with error code. See \"systemctl status docker.service\" and \"journalctl -xe\" for details.\n"}
Sunday 24 May 2020  12:04:17 +0300 (0:00:00.937)       0:02:50.793 ************

8. ansible-playbook -i inventory/hosts -e ansible_user=centos --become --become-user=root copy_daemon_json.yaml --flush-cache

Change /etc/docker/daemon.json , and remove kubelet (old version)

9. restart cluster.yaml
 ansible-playbook -i inventory/hosts -e ansible_user=centos --become --become-user=root cluster.yml  --flush-cache -e kube_version=v1.16.3

10. cat inventory/hosts                                                          at ⎈ kubernetes-admin@cluster.local at 12:46:06
[all]
ip-10-250-195-149.eu-central-1.compute.internal ansible_host=10.250.195.149

ssh -F ssh-bastion.conf centos@10.250.195.149

sudo su , cat ~/.kube/config  , copy config,  exit, exit

11.  save config >  ~/.kube/config

12. cat inventory/host ,  copy apiserver_loadbalancer_domain_name="kubernetes-elb-devtest-987157892.eu-central-1.elb.amazonaws.com"

change server in ~/.kube/config 

server: https://kubernetes-elb-devtest-987157892.eu-central-1.elb.amazonaws.com:6443

13. kubectl get nodes                                                            at ⎈ kubernetes-admin@cluster.local at 12:54:09
NAME                                              STATUS   ROLES    AGE   VERSION
ip-10-250-195-149.eu-central-1.compute.internal   Ready    master   42m   v1.16.3
ip-10-250-205-140.eu-central-1.compute.internal   Ready    <none>   40m   v1.16.3

