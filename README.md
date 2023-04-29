# Rancher Dashboard

Setting up Rancher with Docker.
https://www.rancher.com/

## Prerequisite
- A physical machine or VM (recommended) to act as a Rancher Server
- We are using one with Ubuntu Desktop image

## Run the folowing on the Rancher Server

### 1.Install Docker on the Linux VM

We are using a known compatible docker version
```
{ 
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt-get update
  apt install -y docker.io=20.10.12-0ubuntu2~20.04.1 
}
```

Check if docker is running
```sudo systemctl status docker```

### 2.(Optional) Add user account to docker group
If we want to run docker command as non-root user (without sudo ) add your user account to docker group
```sudo gpasswd -a <usr-acc> docker```

### 3.(Optional) Copy the kube config file to rancher-server
If you wish to run kubectl commands from rancher-server instead of the Kubernetes master machine, copy the kube config from the master to the rancher-server
- Create a directory under home
```mkdir ~/.kube```
- Copy the file
```scp root@<master-machine>:/etc/kubernetes/admin.conf ~/.kube/config```

### 4.Spin up rancher container
Refer https://www.rancher.com/quick-start
```sudo docker run --privileged -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher```
The above command will pull the rancher image and expose it on port 80, if the container is deleted it won’t persist, and configurations will be lost. To make data persist we will mount volumes.
```sudo docker run --privileged -d --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher rancher/rancher```

### 5.Access Rancher Dashboard
Use the IP address of the rancher-server machine at post 80. Access with any machine
Eg:- http://<IP of Rancher Server VM>10.164.64.79:80

You will be asked to login
Find the container ID for the Rancher
```docker ps```
To find the password
```docker logs <container-id> 2>&1 | grep "Bootstrap Password:"```

