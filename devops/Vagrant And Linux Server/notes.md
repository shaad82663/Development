# Vagrant Setup and Configuration Guide

This guide covers advanced Vagrant concepts for setting up and managing virtual machines (VMs), based on provided notes. It’s designed for backend developers familiar with tools like Node.js and MySQL, aiming to create development environments (e.g., for web servers). The guide explains Vagrant configuration, synced folders, provisioning, and common commands, with practical examples for running a web server accessible via a browser.

## 1. Vagrant Configuration

Vagrant uses a `Vagrantfile` to define VM settings, such as the base box, resources, networking, and provisioning scripts. Below is an example configuration with explanations.

### Example Vagrantfile
```ruby
Vagrant.configure("2") do |config|
  # Specify the base box (OS image)
  config.vm.box = "spox/ubuntu-arm"

  # Configure VM resources for VMware Fusion provider
  config.vm.provider "vmware_fusion" do |v|
    v.memory = 1024  # Allocate 1GB RAM
    v.cpus = 2       # Allocate 2 CPUs
  end

  # Sync a folder from host to VM
  config.vm.synced_folder "./scripts", "/opt/scripts"

  # Provision the VM with a shell script
  config.vm.provision "shell", inline: <<-SHELL
    cd /opt/scripts
    touch /opt/scripts/provisioned.txt
  SHELL

  # Configure networking
  config.vm.network "private_network", ip: "192.168.56.17", bridge: nil  # Private IP for host-VM communication
  config.vm.network "public_network", bridge: true                        # Bridge to host's network
end
```

### Key Components
- **Box**: The base image (e.g., `spox/ubuntu-arm`) defines the OS for the VM.
- **Provider**: Specifies the virtualization platform (e.g., `vmware_fusion`). Alternatives include VirtualBox or Hyper-V.
- **Synced Folder**: Maps a host directory (`./scripts`) to a VM directory (`/opt/scripts`) for sharing files.
- **Provisioning**: Runs setup commands (e.g., creating `provisioned.txt`) when the VM is created.
- **Networking**:
  - `private_network`: Assigns a static IP (e.g., `192.168.56.17`) for host-VM access.
  - `public_network`: Bridges the VM to the host’s network, allowing external access (e.g., via browser).

## 2. Synced Folders

Synced folders synchronize files between the host machine and the VM, useful for sharing scripts or code.

- **Purpose**: Keep host and VM files in sync (e.g., for development files).
- **Example**: `config.vm.synced_folder "./scripts", "/opt/scripts"` maps the local `./scripts` folder to `/opt/scripts` in the VM.
- **Note**: Changes in either directory are reflected in the other, but performance may vary based on the provider.

## 3. Provisioning

Provisioning runs setup commands or scripts when the VM is created to configure it (e.g., install packages, create files).

- **Behavior**: Runs automatically when you execute `vagrant up` for the first time.
- **Reloading**: `vagrant reload` does **not** re-run provisioning by default. Use `vagrant reload --provision` or `vagrant provision` to re-run provisioning.
- **Example**: The `Vagrantfile` above creates a file `/opt/scripts/provisioned.txt` during provisioning.

**Example (Extended Provisioning)**:
```ruby
config.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y apache2
  systemctl enable apache2
  systemctl start apache2
SHELL
```
- Installs and starts Apache web server on an Ubuntu-based VM.

## 4. Setting Up a Web Server (Accessible via Browser)

To make the VM accessible via a browser (e.g., for a web server), configure networking and install a web server like Apache (`httpd`).

### Steps
1. **Initialize a VM**:
   - Navigate to the project folder and initialize a VM with a specific box.
   ```bash
   vagrant init bandit145/centos_stream9_arm
   ```
   - This creates a `Vagrantfile` you can edit.

2. **Install Apache**:
   - For CentOS-based VMs (like `bandit145/centos_stream9_arm`), use `yum` (or `dnf` for newer versions).
   ```bash
   sudo yum install httpd
   sudo systemctl enable httpd
   sudo systemctl start httpd
   ```

3. **Check IP Address**:
   - Use `ip addr show` to find the VM’s IP (e.g., `192.168.56.17` for private network or a public IP).
   ```bash
   ip addr show
   ```

4. **Disable Firewall** (for testing):
   - Firewalls may block browser access. Temporarily disable them on CentOS.
   ```bash
   sudo systemctl stop firewalld
   sudo systemctl disable firewalld
   ```
   - **Note**: For production, configure firewall rules instead (e.g., allow port 80).

5. **Access in Browser**:
   - Visit `http://<VM_IP>` (e.g., `http://192.168.56.17`) in a browser to see the default Apache page.

6. **Customize Web Content**:
   - Edit HTML files in `/var/www/html` (Apache’s default directory).
   ```bash
   sudo vim /var/www/html/index.html
   ```
   - Example content:
   ```html
   <!DOCTYPE html>
   <html>
   <body>
     <h1>Welcome to My Vagrant Web Server</h1>
   </body>
   </html>
   ```

## 5. Common Vagrant Commands

- **List Available Boxes**:
  ```bash
  vagrant box list
  ```
  - Example output:
  ```
  bandit145/centos_stream9_arm (vmware_desktop, 20240905183502)
  spox/ubuntu-arm              (vmware_desktop, 1.0.0)
  ```

- **Initialize a VM**:
  ```bash
  vagrant init <box_name>
  ```
  - Example: `vagrant init bandit145/centos_stream9_arm`

- **Start a VM**:
  ```bash
  vagrant up
  ```

- **Run Provisioning**:
  ```bash
  vagrant provision
  ```

- **Reload VM with Provisioning**:
  ```bash
  vagrant reload --provision
  ```

- **SSH into VM**:
  ```bash
  vagrant ssh
  ```

- **Stop VM**:
  ```bash
  vagrant halt
  ```

- **Destroy VM**:
  ```bash
  vagrant destroy
  ```

## 6. Troubleshooting Tips
- **Firewall Issues**: If the browser cannot access the VM, ensure the firewall is disabled or port 80 is open:
  ```bash
  sudo firewall-cmd --add-port=80/tcp --permanent
  sudo firewall-cmd --reload
  ```
- **Synced Folder Errors**: Ensure the host folder exists and the VM provider supports synced folders.
- **Provisioning Fails**: Check script syntax and permissions (e.g., ensure `/opt/scripts` is writable).
- **IP Not Accessible**: Verify the network configuration (`private_network` or `public_network`) and check `ip addr show`.

## 7. Additional Notes
- **Why Use Vagrant**: Simplifies creating reproducible development environments, especially for testing Node.js/MySQL apps.
- **VMware vs. VirtualBox**: VMware Fusion (as in your `Vagrantfile`) is faster but paid; VirtualBox is free but may have slower performance.
- **CentOS vs. Ubuntu**: Your notes mention both `bandit145/centos_stream9_arm` and `spox/ubuntu-arm`. CentOS uses `yum`/`dnf`, while Ubuntu uses `apt`.

**Example: Node.js Setup in VM**:
```ruby
config.vm.provision "shell", inline: <<-SHELL
  curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
  apt-get install -y nodejs
  node -v
SHELL
```
- Installs Node.js 18.x on an Ubuntu VM.

## Further Reading
- [Vagrant Documentation](https://www.vagrantup.com/docs)
- [Apache HTTP Server Setup](https://httpd.apache.org/docs/2.4/install.html)
- [MySQL in Vagrant](https://www.vagrantup.com/docs/provisioning/mysql)