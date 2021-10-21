# The Littlest JupyterHub behind nginx on your own server
Tutorial to install the littlest JupyterHub using nginx as a reverse proxy

## 1. Install TLJH on your own server
- Using a terminal program, SSH into your server. This should give you a prompt where you can type commands.
- Make sure you have python3, python3-dev, curl and git installed.

    ```bash 
    sudo apt install python3 python3-dev git curl 
    ```
- Copy the text below, and paste it into the terminal. Replace <admin-user-name> with the name of the first admin user for this JupyterHub. Choose any name you like (don’t forget to remove the brackets!). This admin user can log in after the JupyterHub is set up, and can configure it to their needs. Remember to add your username!

    ```bash 
    curl -L https://tljh.jupyter.org/bootstrap.py | sudo -E python3 - --admin <admin-user-name>
    ``` 
  **Example:** ```curl -L https://tljh.jupyter.org/bootstrap.py | sudo -E python3 - --admin johan```   
  
 - Press Enter to start the installation process. This will take 5-10 minutes, and will say ‘Done!’ when the installation process is complete.
 - Copy the Public IP of your server, and try accessing **http://_public-ip_** from your browser. If everything went well, this should give you a JupyterHub login page.
 <p align="center">
  <img src="https://tljh.jupyter.org/en/latest/_images/first-login.png" />
</p>


## 2. Change Traefik's configuration
- In order to use Nginx as reverse Proxy on ports 80 and 443 (ssl), use another port as Traefix's proxy listening port. Replace <TRAEFIK_LISTENING_PORT> by the port you want to use:
    ```bash 
    tljh-config set http.port <TRAEFIK_LISTENING_PORT>
    ``` 
  **Example:** ```tljh-config set http.port 8000```
       
  
 - You can now restart Traefik and the hub in order to apply changes:  
    ```bash 
    tljh-config reload proxy
    ```   
    ```bash 
    tljh-config reload hub
    ```    
- If everything works correctly, you should be able to access the JupyterHub login page there: **http://_public-ip_:<TRAEFIK_LISTENING_PORT>**.
  
  **In our case**: ```http://public-ip:8000```  
  
  
## 3. Change your DNS settings
- Add a "A record" pointing to the public-ip address of your server.
    ```bash 
    JUPYTER-DOMAIN. 0 IN A <PUBLIC-IP>
    ```  
- Wait a few minutes. Try accessing http://JUPYTER-DOMAIN:<TRAEFIK_LISTENING_PORT>. If there is no problem, you should see the JupyterHub login page again.  
  
  
# 3. Install Nginx as a reverse proxy
- Install Nginx.
```bash 
apt-get install nginx
```  
- Make sure the Nginx Service is not running by stopping it.
```bash 
sudo systemctl stop nginx
```  
  
- Generate SSL certificates for your domain:
   - Go the the "sites-enabled" folder and open the Nginx configuration file.
      ```bash 
      cd /etc/nginx/sites-enabled
      nano default  
      ```    
  - Remove everything and use the following simple (and temporary) configuration (replace \<JUPYTER-DOMAIN\> by your domain):
    ```bash 
    server {
        listen 80;
        server_name <JUPYTER-DOMAIN>;

        return 302 https://$host$request_uri;
    }
    ```      
  

[!["Buy Me A Coffee"](https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png)](https://paypal.me/johjob?locale.x=fr_FR)
