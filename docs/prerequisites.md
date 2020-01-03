# Prerequisites
To use this configuration, the first thing you have to do is to  install the Docker runtime in your system.

- If running Windows or MacOS as your OS you can simply install [Docker desktop](https://www.docker.com/products/docker-desktop) program.

- If running Linux install the [Docker engine](https://docs.docker.com/install/linux/docker-ce) first, followed by the [Docker compose](https://docs.docker.com/compose/install/) utility installation and finally follow the [post install instructions](https://docs.docker.com/install/linux/linux-postinstall/)
    ```bash
    # Example procedure to install the runtime on a standard Ubuntu (16.04+) installation
  
    sudo apt remove docker docker-engine docker.io containerd runc
    sudo apt update
    sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -  
    sudo apt-key fingerprint 0EBFCD88
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io
    
    # docker compose installation
    sudo apt intsall docker-compose
  
    # Post install steps
    sudo groupadd docker
    sudo usermod -aG docker $USER
    newgrp docker 
    sudo systemctl enable docker  
  
    # Restarting the machine, in order to have the Docker engine successfully up an running
    sudo reboot now
    ```
      
    If running *Linux*, take also note of Docker IP address by running in a terminal window the following snippet: 
    ```bash
    ifconfig docker0 | grep inet
    ```
    The result will be something like this:
    ```bash
    user@example$ ifconfig docker0 | grep inet
        inet 172.17.0.1     netmask 255.255.255     broadcast 172.17.255.255
    ```
    You must take note of the first shown IP address (the one following the `inet` keyword)
