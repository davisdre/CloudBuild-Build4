# Introduction
In this cloud build we will be utilizing whatever flavor of cloud in your choosing (ie. Azure/AWS/GCP/Linode). We will be getting familiarize with Docker Swarm and Linux. We will be deploying two compute resources (ie. Azure VM/AWS EC2/GCP VM/Linodes). First we will be deploying our compute resources. Second create our Docker Swarm Manager. Third join our nodes to the manager. Fourth deploy a service to confirm setup. 

## Cloud building time
### Deploy compute resource
You can deploy any compute resource of your choosing. We need two VMs so we can create a cluster of Docker hosts. We need to install Docker on each VM. 

1. Install Docker using a convenience script from [Docker](https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script). If included this step below, but feel free to give it a read some time.
```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
```

### Create the Docker Swarm Manager
The Docker Swarm Manager’s purpose is to receive commands on behalf of the cluster and assign containers to nodes.

1. Login to your VM you've chosent for Swarm manager and initialize the manager. Replace `PUBLIC_IP` IN THIS example with your VM's public IP address:
   ```shell
   docker swarm init --advertise-addr PUBLIC_IP
   ```
2. Use `docker info` to verify that your Swarm is running and active.
   ```shell
   docker info
   ```

### Join nodes to the manager
In Step 1 of the previous section, the `docker swarm init` command outputs instructions on how to join the manager.
```shell
docker swarm join --token TOKEN PUBLIC_IP:2377
```

1. To join the node to the Swarm, run `docker swarm join` from the node. Change `TOKEN` to the token from Step 1 in the previous section, and `PUIBLIC_IP` to the manager’s public IP:
   ```shell
   docker swarm join --token TOKEN PUBLIC_IP:2377
   ```
2. On the manager, use `docker node ls` to view information about the manager and a list of all nodes:
   ```shell
   docker node ls
   ```
### Deploy a service with Docker Swarm
To deploy a service with Docker Swarm, use the manager to prepare a single node, then scale the configuration to match your needs. In this example, you will install a Python app on one node, then scale it to a cluster (swarm) of two nodes.

1. From the Swarm Manager, use service create to deploy a service to a node. Change pythonExample to whatever you like:
   ```shell
   docker service create -p 80:80 --name pythonExample davisdre/hostname-webapp
   ```
2. Verify that the service has been deployed with `docker ps -a` from any node.
   ```shell
   docker ps -a
   ```
3. Scale the Python service to two nodes.
   ```shell
   docker service scale pythonExample=2
   ```
4. Verify that the service has been deployed with `docker ps -a` from any node.
   ```shell
   docker ps -a
   ```
5. To stop the `pythonExample` service, use the `service remove` command.
   ```shell
   docker service remove pythonExample
   ```
## Clean up resource
Now would be a good time to go back and delete your VM's accordingly. 

# Resource Links
- https://docs.docker.com/engine/install/ubuntu/#install-using-the-convenience-script
- https://hub.docker.com/r/davisdre/hostname-webapp
- https://docs.docker.com/engine/swarm/swarm-tutorial/
