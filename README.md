Here’s a detailed guide on setting up an application load balancer for on-premises or custom environments using Ubuntu 20.04, specifically focusing on **HAProxy** and **NGINX**, as well as providing a general overview of hardware load balancers. The guide will walk you through installation, configuration, and maintenance tasks.

---

# Setting Up an Application Load Balancer in On-Premises or Custom Environments

Setting up an application load balancer typically involves configuring software or hardware-based solutions to distribute traffic among multiple virtual machines (VMs). This guide details how to achieve this using popular tools like **HAProxy** and **NGINX** on Ubuntu 20.04, as well as offering general instructions for hardware load balancers.

## **1. Using HAProxy**

HAProxy is a widely used open-source software load balancer and proxy server for TCP and HTTP-based applications. Here's how you can set it up for VMs in an on-premises environment.

### **1.1. Install HAProxy**

**On Debian/Ubuntu:**

```bash
sudo apt-get update
sudo apt-get install haproxy
```

**On CentOS/RHEL:**

```bash
sudo yum install haproxy
```

**On Fedora:**

```bash
sudo dnf install haproxy
```

**On macOS (using Homebrew):**

```bash
brew install haproxy
```

### **1.2. Configure HAProxy**

Edit the HAProxy configuration file, usually located at `/etc/haproxy/haproxy.cfg`. Below is a basic configuration example:

```haproxy
# Global settings
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 600
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

# Default settings
defaults
    log     global
    option  httplog
    option  dontlognull
    timeout connect 5000ms
    timeout client  50000ms
    timeout server  50000ms

# Frontend configuration
frontend http_front
    bind *:80
    default_backend http_back

# Backend configuration
backend http_back
    balance roundrobin
    server vm1 192.168.1.101:80 check
    server vm2 192.168.1.102:80 check
    server vm3 192.168.1.103:80 check
```

- **frontend http_front**: Defines how HAProxy listens for incoming traffic (on port 80 in this case).
- **backend http_back**: Specifies how traffic is distributed to backend servers (VMs in this case).

### **1.3. Restart HAProxy**

Apply the new configuration by restarting HAProxy:

```bash
sudo systemctl restart haproxy
```

Or, if using an older init system:

```bash
sudo service haproxy restart
```

### **1.4. Verify HAProxy Status**

Check the status of HAProxy to ensure it is running correctly:

```bash
sudo systemctl status haproxy
```

## **2. Using NGINX**

NGINX is another popular choice for load balancing. It is also used as a web server and reverse proxy.

### **2.1. Install NGINX**

**On Debian/Ubuntu:**

```bash
sudo apt-get update
sudo apt-get install nginx
```

**On CentOS/RHEL:**

```bash
sudo yum install nginx
```

**On Fedora:**

```bash
sudo dnf install nginx
```

**On macOS (using Homebrew):**

```bash
brew install nginx
```

### **2.2. Configure NGINX**

Edit the NGINX configuration file, typically located at `/etc/nginx/nginx.conf` or `/etc/nginx/conf.d/default.conf`. Below is an example configuration:

```nginx
http {
    upstream backend {
        server 192.168.1.101;
        server 192.168.1.102;
        server 192.168.1.103;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://backend;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
```

- **upstream backend**: Defines a group of backend servers (VMs).
- **proxy_pass http://backend**: Forwards requests to the backend servers.

### **2.3. Restart NGINX**

Apply the configuration changes by restarting NGINX:

```bash
sudo systemctl restart nginx
```

Or, if using an older init system:

```bash
sudo service nginx restart
```

### **2.4. Verify NGINX Status**

Check the status of NGINX to confirm it is running correctly:

```bash
sudo systemctl status nginx
```

## **3. Using Hardware Load Balancers**

For enterprise environments or high-availability setups, you may use hardware-based load balancers like those from F5, Cisco, or others. The steps generally involve:

1. **Connect to the Management Interface**:  
   Use the hardware load balancer's management console or web interface.

2. **Create a Virtual Server**:  
   Define a virtual IP (VIP) that will be used by clients to access the application.

3. **Configure Pools**:  
   Add your VMs to a pool. This involves specifying the IP addresses and ports of your VMs.

4. **Set Up Health Checks**:  
   Configure health checks to monitor the status of your VMs. This ensures traffic is only sent to healthy VMs.

5. **Configure Traffic Rules**:  
   Set up rules for how traffic is distributed (e.g., round-robin, least connections).

6. **Apply and Test Configuration**:  
   Save and apply your configuration. Test the setup to ensure traffic is correctly balanced across your VMs.

## **4. Monitoring and Maintenance**

Regardless of the method you choose, it’s crucial to monitor your load balancer and backend VMs:

- **Check Logs**: Regularly review logs to ensure there are no errors or issues.
- **Monitor Performance**: Use monitoring tools to track performance metrics and ensure the load balancer is functioning as expected.
- **Update Configurations**: Make necessary updates as your application or infrastructure evolves.

## **Summary**

- **HAProxy** and **NGINX** are popular open-source software load balancers suitable for many environments.
- **Hardware Load Balancers** offer advanced features and higher performance but are more expensive.
- **Monitoring and Maintenance** are crucial for ensuring your load balancer and backend VMs operate efficiently.

These steps provide a comprehensive guide to setting up load balancing in on-premises or custom environments. Adjust configurations based on your specific requirements and infrastructure.

--- 

This guide provides the necessary instructions to configure a load balancing solution using software like HAProxy and NGINX on Ubuntu 20.04. Whether you opt for software-based or hardware-based load balancers, this framework offers flexibility for different environments.
