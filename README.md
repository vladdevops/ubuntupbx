Install FreePBX 17 on Ubuntu 24.04 LTS

- with open-source dependencies (including Asterisk) installed from Ubuntu's official repositories
- using this [cloud-init.yaml](./cloud-init.yaml) installation template
- on machines where Ubuntu is already installed, or on public cloud virtual machines

---

<img alt="VoIP" width="50" src="./images/icons8-office-phone-100.png" /><img alt="FoIP" width="50" src="./images/icons8-fax-100.png" /><img alt="on" width="50" src="./images/icons8-right-50.png" /><img alt="Ubuntu Server" width="50" src="./images/icons8-server-100.png" />

## Install FreePBX and Asterisk on an existing Ubuntu machine

1. Download and edit cloud-init.yaml from this repository. Open the file in an editor (like nano) to set configurations between lines 4 and 53. Set `TOKEN` with an [Ubuntu Pro token](https://ubuntu.com/pro/dashboard) for security patches, and [Livepatch](https://ubuntu.com/security/livepatch) security automation to protect the Linux kernel.

        curl -s https://raw.githubusercontent.com/rajannpatel/ubuntupbx/refs/heads/main/cloud-init.yaml -o cloud-init-jinja.yaml
        nano cloud-init-jinja.yaml

> [!IMPORTANT]
> <img align="right" alt="Info Bubble" width="50" src="./images/icons8-info-100.png" />
> #### Free security patches on open source software for 12 years
> 1. Install all open source software dependencies of FreePBX (including Asterisk) from official Ubuntu LTS repositories
> 2. Ubuntu Pro is available for free for personal use or commercial evaluation purposes, on up to 5 Ubuntu installations.

2. Install **j2cli** to process the Jinja and create a valid YAML file

        sudo apt update
        sudo apt install j2cli
        j2 cloud-init-jinja.yaml > cloud-init.yaml

3. Manually process the cloud-init.yaml file

        sudo cloud-init clean
        sudo cloud-init single --name ubuntu_pro --file cloud-init.yaml
        sudo cloud-init single --name timezone --file cloud-init.yaml
        sudo cloud-init single --name set_hostname --file cloud-init.yaml
        sudo cloud-init single --name update_hostname --file cloud-init.yaml
        sudo cloud-init single --name users_groups --file cloud-init.yaml
        sudo cloud-init single --name write_files --file cloud-init.yaml
        sudo cloud-init single --name apt_configure --file cloud-init.yaml
        sudo cloud-init single --name package-update-upgrade-install --file cloud-init.yaml
        sudo snap install yq
        sudo bash -c 'cat cloud-init.yaml | yq -r ".runcmd[]" | while read -r cmd; do eval "$cmd"; done'

Congratulations :tada: you have successfully installed FreePBX 17 on Ubuntu!

---

<img alt="VoIP" width="50" src="./images/icons8-office-phone-100.png" /><img alt="FoIP" width="50" src="./images/icons8-fax-100.png" /><img alt="via" width="50" src="./images/icons8-right-50.png" /><img alt="Cloud" width="50" src="./images/icons8-cloud-100.png" />

## Install FreePBX and Asterisk on Ubuntu in Google Cloud

<img alt="Steps" width="50" src="./images/icons8-steps-100.png" />

- **[STEP 1](#step-1)** <br>Set up a cloud-deployment workspace, Google Cloud resources like VMs and firewalls can be provisioned and configured from here.
    - Confine the Google Cloud CLI in an Ubuntu VM or container, this VM or container becomes the cloud-deployment workspace.
- **[STEP 2](#step-2)** <br>Install and configure Google Cloud CLI in the cloud-deployment workspace.
    - The Google Cloud CLI is available as a snap package with "classic confinement", meaning it doesn't have strict confinement and has broader system access.
- **[STEP 3](#step-3)** <br>From within the cloud-deployment workspace, launch an Ubuntu VM on Google Cloud that will have:
    - FreePBX 17 and Asterisk 20.6 running on a free Ubuntu 24.04 LTS VM in Google Cloud, with Flowroute, Telnyx, and T38Fax trunks preconfigured for VoIP (voice over IP) and FoIP (fax over IP) using T.38 with T.30 ECM enabled.
    - 12 years of security patching for all open source dependencies of FreePBX, including Asterisk 20.6.
    - security patching automations enabled until the year 2034.

> [!TIP]
> <img align="right" alt="Info Lightbulb" width="50" src="./images/icons8-tip-100.png" />
> #### Free Ubuntu virtual machine on Google Cloud
> An Ubuntu virtual machine on Google Cloud can be 100% free, if run within the [always free](https://cloud.google.com/free/docs/free-cloud-features#compute) configuration and usage limits prescribed in this guide
> - $0 cost to launch
> - $0 recurring expense to run

<br><sup>PROGRESS</sup><br><sub>&emsp;&emsp; :heavy_plus_sign: &emsp; <a href="#step-1">STEP 1</a>&emsp;&emsp; :heavy_multiplication_x: &emsp; STEP 2&emsp;&emsp; :heavy_multiplication_x: &emsp;STEP 3</sub><br><br>

---

<img alt="Container or VM" width="50" src="./images/icons8-thin-client-100.png" />

## STEP 1
### Set up a cloud-deployment workspace on Windows, macOS, or Linux

Instead of installing the Google Cloud CLI software directly on your computer, use containers or VMs for process isolation and general organization or your local workspace. Multipass or LXD are available options to create a Linux environment for use as your cloud-deployment workspace.

---

<img alt="Windows" width="50" src="./images/icons8-windows-client-100.png" /><img alt="macOS" width="50" src="./images/icons8-mac-client-100.png" />

#### Step 1 for Windows and macOS users
##### Set up a cloud-deployment workspace on Windows and macOS

On Windows and macOS, [Multipass](https://multipass.run/) provides Linux VMs on demand.

1. [Install Multipass](https://multipass.run/install)

2. Launch a VM named "cloud-deployment-workspace":

        multipass launch --name cloud-deployment-workspace

3. Enter the Multipass VM as the **ubuntu** user:

        multipass shell cloud-deployment-workspace

<br><sup>PROGRESS</sup><br><sub>&emsp;&emsp; :heavy_check_mark: &emsp;STEP 1&emsp;&emsp; :heavy_plus_sign: &emsp; <a href="#step-2">STEP 2</a>&emsp;&emsp; :heavy_multiplication_x: &emsp;STEP 3</sub><br><br>

---

<img alt="Linux" width="50" src="./images/icons8-linux-server-100.png" />

#### Step 1 for Linux users
##### Set up a cloud-deployment workspace on Linux

On Linux, [LXD](https://canonical.com/lxd/) is a system container and VM manager. LXD is built on top of LXC (Linux Containers) but provides a more user-friendly and feature-rich experience. Think of LXD as the tool you use to manage LXC containers, making it easier to create, configure, and run them.

1.  [Install snapd](https://snapcraft.io/docs/installing-snapd) if your Linux doesn't already have it.


2.  [Install LXD](https://canonical.com/lxd/install)

        snap list lxd &> /dev/null && sudo snap refresh lxd --channel latest/stable || sudo snap install lxd --channel latest/stable

3.  Initialize LXD with sensible default, out-of-the-box configurations

        lxd init --auto

4.  Launch a LXD container named **cloud-deployment-workspace** and map your user account on the host machine to the default **ubuntu** user account in the container:

        lxc launch ubuntu:noble cloud-deployment-workspace -c raw.idmap="both 1000 1000"

5.  Optional Step: mount your home directory into the container as a disk named "host-home", to conveniently access your files from within the container:

        lxc config device add cloud-deployment-workspace host-home disk source=~/ path=/home/ubuntu

6.  Enter the LXD container as the **ubuntu** user:

        lxc exec cloud-deployment-workspace -- su -l ubuntu

<br><sup>PROGRESS</sup><br><sub>&emsp;&emsp; :heavy_check_mark: &emsp;STEP 1&emsp;&emsp; :heavy_plus_sign: &emsp; <a href="#step-2">STEP 2</a>&emsp;&emsp; :heavy_multiplication_x: &emsp;STEP 3</sub><br><br>

---

<img alt="Terminal" width="50" src="./images/icons8-terminal-100.png" />

## STEP 2
### Install and configure the gcloud CLI in your cloud-deployment workspace

1.  Install the [gcloud CLI](https://cloud.google.com/sdk/docs/install)

        sudo snap install google-cloud-cli --classic

2.  Authenticate with the gcloud CLI

        gcloud init

    1. Enter **Y** when prompted with *Would you like to log in (Y/n)?*
    2. Visit the authentication link which starts with `https://accounts.google.com/`
    3. Sign in with a Google account
    4. Click **Allow** to grant access to the Google Cloud SDK
    5. Click **Copy** to copy the verification code
    6. Paste the verification code into the terminal window where the `gcloud init` process is running

    Successful authentication within `gcloud init` produces the following output:

    > ```text
    > You are now logged in as [your@email.com].
    > Your current project is [None].  You can change this setting by running:
    > $ gcloud config set project PROJECT_ID
    > ```

<br><sup>PROGRESS</sup><br><sub>&emsp;&emsp; :heavy_check_mark: &emsp;STEP 1&emsp;&emsp; :heavy_check_mark: &emsp; STEP 2&emsp;&emsp; :heavy_plus_sign: &emsp; <a href="#step-3">STEP 3</a></sub><br><br>

---

<img alt="Cloud" width="50" src="./images/icons8-upload-to-cloud-100.png" />

## STEP 3
### Provision resources and deploy an Ubuntu VM on Google Cloud

These steps are performed in your cloud-deployment workspace:

1. List the projects in the Google Cloud account:
    
       gcloud projects list
    
    Output will appear in this format:
    
    > ```text
    > PROJECT_ID        NAME              PROJECT_NUMBER
    > project-id        project-name      12345678910
    > ```
    
2. Assign the `PROJECT_ID` environment variable with the Project ID from the `gcloud projects list` output:
    
       PROJECT_ID=project-id
    
3. Associate gcloud CLI to this `PROJECT_ID`:
    
       gcloud config set project $PROJECT_ID
    
    This Project ID will contain the PBX VM.
    
4. List the available cloud zones and cloud regions where VMs can be deployed:

       gcloud compute zones list

    Output will appear in this format:

    > ```text
    > NAME                       REGION                   STATUS  NEXT_MAINTENANCE  TURNDOWN_DATE
    > us-east1-b                 us-east1                 UP
    > ```

5. Only regions `us-west1`, `us-central1`, and `us-east1` in North America qualify for Google Cloud's free tier. Set the `ZONE` and `REGION` environment variables with one of the 3 free tier regions, and choose any zone in that region. The following zone and region can be used, or select another zone and region combination from the `gcloud compute zones list` output:

    ```bash
    ZONE=us-east1-b
    REGION=us-east1
    ```

6. Reserve a static IP address and label it "pbx-external-ip":
    
       gcloud compute addresses create pbx-external-ip --region=$REGION
    
7. Use curl to download the cloud-init YAML.

       curl -s https://raw.githubusercontent.com/rajannpatel/ubuntupbx/refs/heads/main/cloud-init.yaml -o cloud-init.yaml

8. Open the file in an editor (like nano) to change configurations specified between lines 4 and 53. Setting `TOKEN` with an [Ubuntu Pro token](https://ubuntu.com/pro/dashboard) is required for security updates to Asterisk, Asterisk's dependencies, and some FreePBX dependencies. [Livepatch](https://ubuntu.com/security/livepatch) will be enabled by this cloud-init.yaml file if a Pro Token is set.

    ```markdown
    # SET OUR VARIABLES
    # =================

    # Ubuntu Pro token from: https://ubuntu.com/pro/dashboard (not needed for Ubuntu Pro instances on Azure, AWS, or Google Cloud)
    {% set TOKEN = '' %}

    # SMTP credentials (sendgrid and gmail example configurations)
    # sendgrid: https://console.cloud.google.com/marketplace/product/sendgrid-app/sendgrid-email
    # {% set SMTP_HOST = 'smtp.sendgrid.net' %}
    # {% set SMTP_PORT = '587' %}
    # {% set SMTP_USERNAME = 'apikey' %}
    # substitute `YOUR-API-KEY-HERE` below, with your API KEY, https://app.sendgrid.com/settings/api_keys
    # {% set SMTP_PASSWORD = 'YOUR-API-KEY-HERE' %}

    # gmail:
    # {% set SMTP_HOST = 'smtp.gmail.com' %}
    # {% set SMTP_PORT = '587' %}
    # replace `YOUREMAIL@GMAIL.COM` with your email, get `YOUR-APP-PASSWORD` from: https://myaccount.google.com/apppasswords
    # {% set SMTP_USERNAME = 'YOUREMAIL@GMAIL.COM' %}
    # {% set SMTP_PASSWORD = 'YOUR-APP-PASSWORD' %}

    {% set SMTP_HOST = '' %}
    {% set SMTP_PORT = '' %}
    {% set SMTP_USERNAME = '' %}
    {% set SMTP_PASSWORD = '' %}

    # CRONTAB_EMAIL address for daily crontab notifications
    {% set CRONTAB_EMAIL = 'youremail@example.com' %}

    # HOSTNAME and FQDN are used by Postfix, and necessary for Sendgrid
    # HOSTNAME: subdomain of FQDN (e.g. `server` for `server.example.com`)
    # FQDN (e.g. `example.com` or `server.example.com`)
    {% set HOSTNAME = 'voip' %}
    {% set FQDN = 'voip.example.com' %}

    # OPTIONAL: PRECONFIGURE FREEPBX CORE MODULE (Trunks and Outbound Routes)
    # N11, International, and NANPA dialing with failover, across T38Fax, BulkVS, Telnyx, and Flowroute
    # ubuntupbx.core.backup.tar.gz was generated by the Backup & Restore FreePBX Module
    # this can be replaced with the address of your backup file, if you're copying configurations from another FreePBX installation
    {% set RESTORE_BACKUP = 'https://github.com/rajannpatel/ubuntupbx/raw/refs/heads/main/ubuntupbx.core.backup.tar.gz' %}

    # TIMEZONE: default value is fine
    # As represented in /usr/share/zoneinfo. An empty string ('') will result in UTC time being used.
    {% set TIMEZONE = 'America/New_York' %}

    # TIME TO REBOOT FOR SECURITY AND BUGFIX PATCHES IN XX:XX FORMAT
    {% set SECURITY_REBOOT_TIME = "04:30" %}

    # =========================
    # END OF SETTING VARIABLES
    ```

9. The following command launches a free tier e2-micro VM named "pbx". Replace `e2-micro` in this command with [another instance type](https://cloud.google.com/compute/docs/machine-resource) if the free tier isn't desired:
    
    ```bash
    gcloud compute instances create pbx \
        --zone=$ZONE \
        --machine-type=e2-micro \
        --address=pbx-external-ip \
        --tags=pbx \
        --boot-disk-size=30 \
        --image-family=ubuntu-2404-lts-amd64 \
        --image-project=ubuntu-os-cloud \
        --metadata-from-file=user-data=cloud-init.yaml
    ```

> [!NOTE]
> <img align="right" alt="Info Bubble" width="50" src="./images/icons8-information-100.png" />
> In the steps below, `--source-ranges` can be any number of globally routable IPv4 addresses written as individual IPs, or groups of IPs in slash notation, separated by commas (but no spaces). For example: `192.178.0.0/15,142.251.47.238`
>
> For convenience, some `--source-ranges` in the steps below fetch the globally routable IPv4 address of the machine where the command was run, using an Amazon AWS service. Replace `$(wget -qO- http://checkip.amazonaws.com)` with the correct IP address(es) and/or IP address ranges written in slash notation, as needed.

> [!TIP]
> <img align="right" alt="Info Lightbulb" width="50" src="./images/icons8-tip-100.png" />
> Looking up an individual IP from an ISP at [arin.net](https://arin.net) can reveal the entire CIDR block of possible IPs from that ISP, if wide ranges need to be permitted in the firewall. For example, looking up a Charter Spectrum IP [174.108.85.8](https://search.arin.net/rdap/?query=174.108.85.8) reveals a CIDR of `174.96.0.0/12`. CIDR blocks for popular ISPs serving dynamic IPs to customers in North America appear in the following table:
> | ISP  | CIDR |
> | ------------- | ------------- |
> | [Charter Spectrum](https://search.arin.net/rdap/?query=174.96.0.0)  | `174.96.0.0/12`  |
> | [Optimum Online's Altice Fiber](https://search.arin.net/rdap/?query=174.96.0.0)  | `24.184.0.0/14`  |
> | [Verizon Wireless 5G Home Internet](https://search.arin.net/rdap/?query=75.192.0.0)  | `75.192.0.0/10`  |
> | [Google Fiber](https://search.arin.net/rdap/?query=136.32.0.0)  | `136.32.0.0/11`  |


> [!CAUTION]
> <img align="right" alt="Caution Sign" width="50" src="./images/icons8-caution-100.png" />
> Allowing broad permissions to entire CIDR blocks of an ISP increases the attack surface of your FreePBX installation, monitoring SIP registrations with fail2ban and not allowing broad access to the management interface on TCP Port 80 is recommended.

10. Allow HTTP access to the FreePBX web interface from IPs specified in `--source-ranges`. Including `icmp` in `--rules` is optional, it enables the **ping** command to reach the VM from `--source-ranges` IP(s):

    ```bash
    gcloud compute firewall-rules create allow-management-http-icmp \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="$(wget -qO- http://checkip.amazonaws.com)" \
        --rules="tcp:80,icmp" \
        --description="Access FreePBX via web and ping"
    ```

11. Allow SIP registration and RTP & UDPTL media streams over the default UDP port ranges for ATAs and softphones from IPs specified in `--source-ranges`. The `$(wget -qO- http://checkip.amazonaws.com)` command assumes the machine where this command is run also shares the same Internet connection as the softphones and devices that will connect to this PBX.

    ```bash
    gcloud compute firewall-rules create allow-devices-sip-rtp-udptl \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="$(wget -qO- http://checkip.amazonaws.com)" \
        --rules="udp:5060,udp:4000-4999,udp:10000-20000" \
        --description="SIP signaling and RTP & UDPTL media for ATAs and Softphones"
    ```

12. Allow RTP and UDPTL media streams over Asterisk's configured UDP port ranges. [Flowroute](https://flowroute.com) uses direct media delivery to ensure voice data streams traverse the shortest path between the caller and callee, the `--source-ranges="0.0.0.0/0"` allows inbound traffic from anywhere in the world. [BulkVS](https://bulkvs.com), [Telnyx](https://telnyx.com) and [T38Fax](https://t38fax.com) proxy all the RTP and UDPTL media streams through their network for observability into the quality of the RTP streams.

    #### Flowroute

    ```bash
    gcloud compute firewall-rules create allow-flowroute-rtp-udptl \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="0.0.0.0/0" \
        --rules="udp:4000-4999,udp:10000-20000" \
        --description="Flowroute incoming RTP and UDPTL media streams"
    ```

    The Flowroute incoming RTP and UDPTL media streams firewall rule permits incoming UDP traffic to Asterisk's RTP and UDPTL ports from any IP address in the world. It is so permissive that the following Telnyx, T38Fax, and BulkVS specific ingress rules are redundant, but they are included below for completeness:

    #### Telnyx

    ```bash
    gcloud compute firewall-rules create allow-telnyx-rtp-udptl \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="36.255.198.128/25,50.114.136.128/25,50.114.144.0/21,64.16.226.0/24,64.16.227.0/24,64.16.228.0/24,64.16.229.0/24,64.16.230.0/24,64.16.248.0/24,64.16.249.0/24,103.115.244.128/25,185.246.41.128/25" \
        --rules="udp:4000-4999,udp:10000-20000" \
        --description="Telnyx incoming RTP and UDPTL media streams"
    ```

    #### T38Fax

    ```bash
    gcloud compute firewall-rules create allow-t38fax-rtp-udptl \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="8.20.91.0/24,130.51.64.0/22,8.34.182.0/24" \
        --rules="udp:4000-4999,udp:10000-20000" \
        --description="T38Fax incoming RTP and UDPTL media streams"
    ```

    #### BulkVS

    ```bash
    gcloud compute firewall-rules create allow-bulkvs-rtp-udptl \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="162.249.171.198,23.190.16.198,76.8.29.198" \
        --rules="udp:4000-4999,udp:10000-20000" \
        --description="BulkVS incoming RTP and UDPTL media streams"
    ```

13. Allow SIP signaling for inbound calls from Flowroute, Telnyx, T38Fax, and BulkVS when using IP authentication for those SIP trunks.

    #### Flowroute

    ```bash
    gcloud compute firewall-rules create allow-flowroute-sip \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="34.210.91.112/28,34.226.36.32/28,16.163.86.112/30,3.0.5.12/30,3.8.37.20/30,3.71.103.56/30,18.228.70.48/30" \
        --rules="udp:5060" \
        --description="Flowroute SIP Signaling"
    ```

    #### Telnyx

    ```bash
    gcloud compute firewall-rules create allow-telnyx-sip \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="192.76.120.10,64.16.250.10,185.246.41.140,185.246.41.141,103.115.244.145,103.115.244.146,192.76.120.31,64.16.250.13" \
        --rules="udp:5060" \
        --description="Telnyx SIP Signaling"
    ```

    #### T38Fax

    ```bash
    gcloud compute firewall-rules create allow-t38fax-sip \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="8.20.91.0/24,130.51.64.0/22,8.34.182.0/24" \
        --rules="udp:5060" \
        --description="T38Fax SIP Signaling"
    ```

    #### BulkVS

    ```bash
    gcloud compute firewall-rules create allow-bulkvs-sip \
        --direction=INGRESS \
        --action=ALLOW \
        --target-tags=pbx \
        --source-ranges="162.249.171.198,23.190.16.198,76.8.29.198" \
        --rules="udp:5060" \
        --description="BulkVS SIP Signaling"
    ```

14. Observe the installation progress by tailing `/var/log/cloud-init-output.log` on the VM:
    
        gcloud compute ssh pbx --zone $ZONE --command "tail -f /var/log/cloud-init-output.log"
    
15. First time gcloud CLI users will be prompted for a passphrase twice. This password can be left blank, press <kbd>Enter</kbd> twice to proceed:
    
    > ```text
    > WARNING: The private SSH key file for gcloud does not exist.
    > WARNING: The public SSH key file for gcloud does not exist.
    > WARNING: You do not have an SSH key for gcloud.
    > WARNING: SSH keygen will be executed to generate a key.
    > Generating public/private rsa key pair.
    > Enter passphrase (empty for no passphrase):
    > Enter same passphrase again:
    > ```
    
16. A reboot may be required during the cloud-init process. The following output indicates a reboot will be performed:
    
    > ```text
    > 2023-08-20 17:30:04,721 - cc_package_update_upgrade_install.py[WARNING]: Rebooting after upgrade or install per /var/run/reboot-required
    > ```
    
    If the **ubuntu-2404-lts-amd64** Ubuntu image in Google Cloud, specified in step 9 in the `--image-family` parameter, does not contain all the security patches published by Canonical in the last 24 hours, and `package_reboot_if_required: true` in cloud-init.yaml, a reboot may occur.
  
17. In the event of a reboot, re-run the tail command to continue observing the progress of the installation; otherwise skip this step:
    
        gcloud compute ssh pbx --zone $ZONE --command "tail -f /var/log/cloud-init-output.log"
    
18. Press <kbd>CTRL</kbd> + <kbd>C</kbd> to terminate the tail process when it stops producing new output, and prints a `finished at` line:
    
    > ```text
    > Cloud-init v. 24.1.3-0ubuntu3.3 finished at Thu, 20 Jun 2024 03:53:16 +0000. Datasource DataSourceGCELocal.  Up 666.00 seconds
    > ```

19. Visit the PBX external IP to finalize the configuration of FreePBX and set up your Trunks and Extensions. This command will print the hostname for your VM as a hyperlink, <kbd>CTRL</kbd> click the link to open:

        dig +short -x $(gcloud compute addresses describe pbx-external-ip --region=$REGION --format='get(address)') | sed 's/\.$//; s/^/http:\/\//'

20. Connect to the pbx VM via SSH:

        gcloud compute ssh pbx --zone $ZONE

    Upon logging in via SSH, edit the root user's crontab

        sudo crontab -e

    When prompted for a default crontab editor, **nano** (option 1) will be the most intuitive option for most users

    > ```text
    > Select an editor.  To change later, run 'select-editor'.
    > 1. /bin/nano        <---- easiest
    > 2. /usr/bin/vim.basic
    > 3. /usr/bin/vim.tiny
    > 4. /bin/ed
    > ```

    Add the following lines at the bottom of the crontab file. Replace **example-bucket-name** with the name of your storage bucket on Google Cloud Storage.

        @daily gcloud storage rsync /var/spool/asterisk/backup gs://example-bucket-name/backup --recursive
        @daily gcloud storage rsync /var/spool/asterisk/monitor gs://example-bucket-name/monitor --recursive

    Use Google Cloud Storage for FreePBX backups, and storing call recordings, if you choose to record your calls. There is no need to retain more than 1 copy of your FreePBX backups on your Ubuntu virtual machine, because the backups are retained externally in a Google Cloud Storage S3 Bucket. Delete stale backups from the S3 bucket on a schedule of your choosing by setting a "maximum age" object lifecycle policy on the S3 bucket.

> [!NOTE]
> <img align="right" alt="Info Bubble" width="50" src="./images/icons8-information-100.png" />
> - Deploying FreePBX on a single Ubuntu virtual machine (VM) in Google Cloud is an ideal solution for personal users and small to medium-sized businesses.
> - Google Cloud provides enterprise grade datacenter resources, which also include simplified backup, recovery, and rollback capabilities for virtual machines.
> - FreePBX backups can be copied from your Ubuntu virtual machine into Google's Cloud Storage. Modify line 43 of cloud-init.yaml to restore a backup on a new Ubuntu server.

    Connect to the Asterisk CLI, and observe output as you configure and use FreePBX:

        sudo su -s /bin/bash asterisk -c 'cd ~/ && asterisk -rvvvvv'

    The `exit` command will safely exit the Asterisk CLI. Running the `exit` command again will quit the SSH session. 

<br><sup>PROGRESS</sup><br><sub>&emsp;&emsp; :heavy_check_mark: &emsp;STEP 1&emsp;&emsp; :heavy_check_mark: &emsp; STEP 2&emsp;&emsp; :heavy_check_mark: &emsp;STEP 3&emsp;&emsp; :tada: &emsp;COMPLETED</sub><br><br>

---

<img alt="Delete" width="50" src="./images/icons8-delete-100.png" />

## **HOW DO I UNDO?**
### How to delete things in Google Cloud

> [!WARNING]
> <img align="right" alt="Warning Sign" width="50" src="./images/icons8-warning-100.png" />
> The following steps are destructive, and will remove everything created by following the above steps, in Google Cloud.

The following steps remove the "pbx" VM, its static IP address, and its firewall rules.

1. List all VMs in this project:

       gcloud compute instances list

2. To delete the "pbx" VM, update `ZONE` if not set already, to reflect what was specified in Step 5:

       ZONE=us-east1-b
       gcloud compute instances delete pbx --zone $ZONE

3. List all the static addresses:
    
       gcloud compute addresses list

4. To delete the address named "pbx-external-ip", update `REGION` if not set already, to reflect what was specified in Step 5:

       REGION=us-east1
       gcloud compute addresses delete pbx-external-ip --region=$REGION

5. List all firewall rules in this project:
    
       gcloud compute firewall-rules list

6. To delete the firewall rules we created earlier:

       gcloud compute firewall-rules delete allow-management-http-icmp
       gcloud compute firewall-rules delete allow-devices-sip-rtp-udptl
       gcloud compute firewall-rules delete allow-flowroute-rtp-udptl
       gcloud compute firewall-rules delete allow-telnyx-rtp-udptl
       gcloud compute firewall-rules delete allow-t38fax-rtp-udptl
       gcloud compute firewall-rules delete allow-bulkvs-rtp-udptl
       gcloud compute firewall-rules delete allow-flowroute-sip
       gcloud compute firewall-rules delete allow-telnyx-sip
       gcloud compute firewall-rules delete allow-t38fax-sip
       gcloud compute firewall-rules delete allow-bulkvs-sip

<br><br><br><br>
<a href="https://icons8.com"><img alt="icon credits" align="right" src="./images/icons.png"></a>