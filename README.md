# JIRA_software-deployment-in-containers-using-Ansible-and-Docker
![e194738a-a1d4-4932-b5b2-7049dde1007c](https://github.com/user-attachments/assets/fa08b432-3def-4197-bfab-ee6f33b7cf23)

1. Launching 3 Amazon Linux 2 instances and naming them **“Ansible_Control”**, **“Worker_Node_1”** and **“Worker_Node_2”**. (The security group of Ansible_Control is configured to allow just SSH requests from a desired IP (e.g., my IP) address. The security group of the Worker_Node_1 and Worker_Node_2 is configured to allow SSH traffic from the Ansible_Control and SSH traffic from a desired IP (e.g., my IP).

## Ansible security group inbound rule
![image-20240717-192750 (1)](https://github.com/user-attachments/assets/6e465feb-a63b-41e8-9c98-4f2ed36a1574)

## Security Group inbound rules of Worker Nodes
![image-20240717-193102](https://github.com/user-attachments/assets/4c815241-91c1-417d-a7ca-0cd4dccec07a)

## Created Instances
![image-20240717-193325](https://github.com/user-attachments/assets/09a90b0e-fb51-4946-8c68-e320b45e2242)

2. Login to 3 three instances using Git Bash

![image-20240717-193457](https://github.com/user-attachments/assets/e2ae4bec-945f-444d-8148-90f63829042e)

To determine which Git Bash belongs to which node, rename the servers.
- In each server, Switch as a root user
  ```sh
  sudo su –

- Get into the **“hostname”** file in the /etc/ directory then edit the hostname of each server
  ```sh
  nano /etc/hostname

![image-20240717-194643](https://github.com/user-attachments/assets/d5aa3bfc-3691-4cae-a19d-f8d4814ea04a)

- Save the changes and reboot the servers
  ```sh
  reboot

- Log into the instances again and you will realize that you can identify which Git bash belongs to which server

![image-20240717-194927](https://github.com/user-attachments/assets/695fe011-e358-4735-aa18-783b1ffc1244)

3. For all 3 servers create a common user called “ansible”, set a common password, and allow for “Password authentication”  using the following commands for all 3 servers.
  ```sh
  sudo su - # Login as Root User
  useradd ansible
  passwd ansible # pass a password
  sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config # change password authentication permission to "yes"

- Navigate to the /etc/ssh/sshd_config path, and uncomment **"PermitRootLogin"** in the sshd_config file.
  ```sh
  nano /etc/ssh/sshd_config

- Restart the sshd service.
  ```sh
  sudo systemctl restart sshd

4. Add ansible to the “sudoers” group in each server.
- Navigate to the sudoers group file via the following command.
  ```sh
  nano /etc/sudoers

- Add **“ansible”** user in each of the following points in the file.

![image-20240717-200615](https://github.com/user-attachments/assets/15f10820-ad74-41a2-b0da-b6c17d4f4241)

- In the **“Ansible_Control”** Node switch to the created “ansible” user.
  ```sh
  sudo su ansible

- Try reaching the worker nodes one after the other from the Control Node. A password will be required.
  ```sh
  ssh ansible@private_ip

You will have something like the above picture

![image-20240717-201024](https://github.com/user-attachments/assets/aeb53d25-35d4-433a-8ab1-43581e9ed07d)

5. Create a Keypair in the Control node and copy it in the Worker Node so that it won't require a password when the Control node “SSH” into worker nodes. This will facilitate the configuration of the worker nodes or running the playbook via the control node since it won’t be hindered or blocked by asking for a password which we might not be available at every moment to pass that in.

- While in the control Node, ensure you are at the home directory of the ansible user or run the following lines to do so.
```sh
sudo su ansible 
cd ~

- Generate key pair and give required permissions to the generated keypair file(.ssh) so it can be copied to the worker nodes. After the code generates a key-pair, you can hit **“Enter”** till the end.
  ```sh
  ssh-keygen -t rsa # generate keypair
  sudo chmod 700 /home/ansible/.ssh # gives permission to .ssh file to copy it to worker_node

![image-20240717-201549](https://github.com/user-attachments/assets/a8ff4009-a5b7-426e-870c-aa2b3a95183c)

- Copy Keypair to each worker node.
  ```sh
  ssh-copy-id ansible@private_ip

![image-20240717-201731](https://github.com/user-attachments/assets/e7d09fc4-fb1c-4369-ac17-c1249ffe9173)

- Once done, try to “SSH” again to your worker nodes and you won’t be prompted to pass a password.

![image-20240717-201823](https://github.com/user-attachments/assets/d348adb7-e97e-4db7-ab4c-823812bd6029)

6. Installing Ansible and Docker packages.

- Install the ansible package in the Control Node.
  ```sh
  sudo amazon-linux-extras install ansible2 -y # install ansible in control node
  ansible --version # check if ansible is installed

- SSH into each worker node through the Control and install the Docker engine.
  ```sh
  sudo amazon-linux-extras install docker -y # install docker
  sudo service docker start # start docker
  sudo systemctl enable docker # enable docker
  sudo systemctl status docker # ensure docker runs
  sudo docker run hello-world # verify if docker is install

7. Update the Inventory file in Control Host so that Ansible knows identified the nodes it communicates with when the playbook is run

- In the home directory of the “ansible” user, run this code.
  ```sh
  cd /etc/ansible/
  sudo nano hosts

- At example 2 section uncomment "[webservers]" then pass the private Ip addresses of the Worker nodes beneath.
