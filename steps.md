# Using Ansible update 3 remote server

- Launch an instance on the ec2 instance (master) using key pair named as django_key_pair.pem 
- Launch 3 server instances (server1, server2, server3) on ec2 using key pair named as django_key_pair.pem

  <img width="604" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/0a3e152c-b7e8-44c7-8ee1-36e8fcb26c5d">

- ssh master on terminal

      ssh -i "django_key_pair.pem" ubuntu@ec2-52-91-196-136.compute-1.amazonaws.com

  <img width="789" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/952ded65-a43e-40fc-b644-494a3c2062c2">

- Installing ansible

      sudo apt-add-repository ppa:ansible/ansible

  <img width="697" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/b94942e6-9956-48a7-894b-2da45608b127">

Update the system

    sudo apt update

<img width="560" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/2d0873a9-06c4-428a-9e88-8f3f2a13fc4c">

install ansible

    sudo apt install ansible

  <img width="453" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/354d8cf3-3aed-4eb4-a33f-d23bd2f6840b">

- Check ansible version

      ansible --version

  <img width="486" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/c7408986-7b1c-4ec2-8c02-8eada7376025">


- Copying the keys from local to server

      scp -i "django_key_pair.pem" django_key_pair.pem ubuntu@ec2-52-91-196-136.compute-1.amazonaws.com:/home/ubuntu/keys

  <img width="848" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/cef53d0d-4fdb-4f74-8c58-24aa821bbba7">  

- Updating the host file

      sudo vim /etc/ansible/hosts

  Edit the hosts file

      [servers]
      server_1 ansible_host=44.205.252.25
      server_2 ansible_host=52.87.178.158
      server_3 ansible_host=54.162.85.185

      [servers:vars]
      ansible_python_interpreter=/usr/bin/python3
      ansible_user=ubuntu
      ansible_ssh_private_key_file=/home/ubuntu/keys.django_key_pair.pem

- Ping the server

      ansible servers -m ping 

  <img width="661" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/19a690ec-38a2-4591-84d2-291702ea57d6">

- To know the free space in every server

      ansible servers -a "free -h"

<img width="578" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/e3f12711-3b34-4ddb-b487-66859666932b">

- Update all the servers

      ansible servers -a "sudo apt update"

 <img width="571" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/95248b73-513a-4188-986f-4e6190b92276">

- Edit the hosts file and make another group of server 3 as [prod]

        [servers]
      server_1 ansible_host=44.205.252.25
      server_2 ansible_host=52.87.178.158
      
      
      [prod]
      server_3 ansible_host=54.162.85.185
      
      [servers:vars]
      ansible_python_interpreter=/usr/bin/python3
      ansible_user=ubuntu
      ansible_ssh_private_key_file=/home/ubuntu/keys/django_key_pair.pem

  - Now ping [prod]

        ansible prod -m ping

<img width="759" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/e6f07070-0e87-4bcd-8c71-088d5832fbc8">
ERROR: Shown error as it needs access to keys

- To see all the servers

      ansible-inventory --list

<img width="698" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/f769b8f8-af8c-4f86-a525-89ee1d2e67f9">

- Fixing the Error

       [servers]
          server_1 ansible_host=44.205.252.25
          server_2 ansible_host=52.87.178.158
          
          
          [prod]
          server_3 ansible_host=54.162.85.185
          
          [all:vars]
          ansible_python_interpreter=/usr/bin/python3
          ansible_user=ubuntu
          ansible_ssh_private_key_file=/home/ubuntu/keys/django_key_pair.pem

  Edit the [servers:vars] to [all:vars]

- Now ping [prod]

      ansible prod -m ping

  <img width="425" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/1a67d644-7d56-4cc2-9c83-29b0864544af">

# Creating Playbook in ansible

- Create a playbook to show the date and uptime of system

      -
      name: Dates playbook
      hosts: servers
      tasks:
        - name: Show date
          command: date
        - name: System up time
          command: uptime

- Run the playbook

      ansible-playbook dat_play.yml

  <img width="730" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/4f6f3481-a5e2-4ab0-af5f-684a2d8f0268">


- Run the playbook and show the output

      ansible-playbook -v date_play.yml

  <img width="887" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/6a97c479-e23b-412f-8e2a-65222b706eb8">

# Installing nginx on the servers

- Create playbook

      -
      name: Nginx install and start
      hosts: servers
      become: yes
      tasks:
        - name: Install nginx
          apt:
            name: nginx
            state: latest
        - name: Start nginx
          service:
            name: nginx
            state: started
            enabled: yes

  - Run the playbook
 
        ansible-playbook install_nginx_play.yml

    <img width="732" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/8223260f-f80e-405f-96e5-e43281ccb3e4">

- Nginx running on server 1

<img width="604" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/0ad4f530-b6cd-45c0-9bab-b24f8d54e33f">


- Nginx running on server 2

<img width="591" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/0c37d85b-c9be-4bf0-a014-ae6338a3cc87">

# Deploying a static website on nginx web server

- Create playbook

      -
      name: Install nginx and serve static website
      hosts: prod
      become: yes
      tasks:
      - name: install nginx
        apt:
          name: nginx
          state: latest
    
      - name: start nginx
        service:
          name: nginx
          state: started
          enabled: yes
    
      - name: Deploy web page
        copy:
          src: index.html
          dest: /var/www/html

  - Create index.html page taken from chatgpt by using the prompt act as a web developer and make a cool static web page
 
            <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>Cool Static Page</title>
            <style>
                /* Add some basic styles */
                body {
                    font-family: Arial, sans-serif;
                    background-color: #f0f0f0;
                    margin: 0;
                    padding: 0;
                }
        
                header {
                    background-color: #333;
                    color: #fff;
                    text-align: center;
                    padding: 1em 0;
                }
        
                h1 {
                    font-size: 2.5em;
                }
        
                .container {
                    max-width: 800px;
                    margin: 0 auto;
                    padding: 20px;
                }
        
                p {
                    font-size: 1.2em;
                    line-height: 1.5;
                }
        
                /* Add some additional styles for a cool effect */
                .cool-box {
                    background-color: #fff;
                    padding: 20px;
                    border-radius: 10px;
                    box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.2);
                    text-align: center;
                }
        
                .cool-button {
                    background-color: #333;
                    color: #fff;
                    padding: 10px 20px;
                    border: none;
                    border-radius: 5px;
                    cursor: pointer;
                    transition: background-color 0.3s;
                }
        
                .cool-button:hover {
                    background-color: #555;
                }
            </style>
        </head>
        <body>
            <header>
                <h1>Welcome to Our Cool Page</h1>
            </header>
            <div class="container">
                <div class="cool-box">
                    <h2>About Us</h2>
                    <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed euismod, sapien in bibendum malesuada, nisl nunc sagittis libero, nec ullamcorper urna justo ac quam.</p>
                    <button class="cool-button">Learn More</button>
                </div>
            </div>
        </body>
        </html>

 - Run the playbook
   
          ansible-playbook deploy_static_page_play.yml

<img width="878" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/63f92df3-9dfe-43df-84f6-b1cd03f09757">

- Static page is deployed

  <img width="933" alt="image" src="https://github.com/ManishNegi963/ansible_playbooks/assets/124788172/e5d27f9b-6e9e-4697-84c7-7affb5965b57">


