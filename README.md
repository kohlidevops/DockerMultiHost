# How to manage multiple Docker Host from Docker Client

I have launched 3 EC2 servers called DockerHost-1, DockerHost-2 and DockerClient in AWS Console and I assuming DockerClient as my local system to connect the multiple DockerHosts.

![image](https://github.com/user-attachments/assets/ad2a91f9-0c9e-4a8d-a0bc-ce579ceb7e7e)


## Install Docker on all the machines

To install Docker and configure on all the machines using below codes

```
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get install docker.io -y
sudo systemctl start docker.service
sudo chmod 777 /var/run/docker.socket
sudo usermod -aG docker $USER
sudo systemctl restart docker.service
```

### To create a SSH keygen in Docker client and copy this key to all Docker Host machines

#### SSH to Docker Client

```
ssh-keygen
sudo cat ~/.ssh/id_rsa.pub
//copy this key and paste in the Docker Host machine inside ~/.ssh/authorized_keys
sudo systemctl restart ssh.service
sudo systemctl restart sshd.service
```

#### SSH to Docker Host

```
sudo nano ~/.ssh/authorized_keys
//paste the public key here and save it
sudo systemctl restart ssh.service
sudo systemctl restart sshd.service
```

#### SSH to Docker client

```
ssh ubuntu@DockerhostPrivateIP-1
ssh ubuntu@DockerhostPrivateIP-2
//Should be connected
```

### To configure remote access with system unit file for all Docker Host machines

#### SSH to Docker Host machines

```
sudo systemctl edit docker.service
//Add or modify below lines
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://127.0.0.1:2375

sudo systemctl daemon-reload
sudo systemctl restart docker.service
sudo systemctl status docker.service
```

### To create a context in Docker Client machine for all Docker host machines

#### SSH to Docker Client

```
docker context create remote1 ‐‐docker "host=ssh://ubuntu@privateip-1"
docker context create remote2 ‐‐docker "host=ssh://ubuntu@privateip-2"
docker context ls
docker --context remote1 images
docker --context remote1 ps
```

### To create a container on Docker Host from Docker Client

#### SSH to Docker Client

```
docker --context remote1 run httpd
docker --context remote1 ps
```



