# JIRA_software-deployment-in-containers-using-Ansible-and-Docker
![e194738a-a1d4-4932-b5b2-7049dde1007c](https://github.com/user-attachments/assets/fa08b432-3def-4197-bfab-ee6f33b7cf23)

Launching 3 Amazon Linux 2 instances and naming them **“Ansible_Control”**, **“Worker_Node_1”** and **“Worker_Node_2”**. (The security group of Ansible_Control is configured to allow just SSH requests from a desired IP (e.g., my IP) address. The security group of the Worker_Node_1 and Worker_Node_2 is configured to allow SSH traffic from the Ansible_Control and SSH traffic from a desired IP (e.g., my IP).

## Ansible security group inbound rule
![image-20240717-192750 (1)](https://github.com/user-attachments/assets/6e465feb-a63b-41e8-9c98-4f2ed36a1574)

## Security Group inbound rules of Worker Nodes
![image-20240717-193102](https://github.com/user-attachments/assets/4c815241-91c1-417d-a7ca-0cd4dccec07a)

## Created Instances
![image-20240717-193325](https://github.com/user-attachments/assets/09a90b0e-fb51-4946-8c68-e320b45e2242)
