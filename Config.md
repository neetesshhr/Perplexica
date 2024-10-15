
# Perplexica Implementation on my.domain.com

## Project Overview
The goal is to implement **Perplexica** on `https://ai.rijalnitesh.com`, using Hetzner (Ubuntu VPS) for hosting and Cloudflare for Ubuntu management.

---

## Approach

The project can be divided into five major stages:

1. **Domain and DNS Management**
2. **Cloudflare API Integration**
3. **CI/CD with GitHub Actions**
4. **Server Provisioning with Ansible**
5. **API Key Management**

---

## Detailed Steps

### Step 1: Domain and DNS Setup
- **Action:**
  My domain was initially registered with Namecheap. To facilitate DNS management on Cloudflare, I signed up for a Cloudflare account and added the domain to Cloudflare’s dashboard.
  
- **Rationale:**
  Cloudflare offers efficient DNS management, enhanced security (SSL), and performance optimization features.

- **Execution:**
  Updated the nameservers in Namecheap to point to Cloudflare’s nameservers, allowing Cloudflare to manage DNS.

**Advantages:**
  - Easy DNS management and added security features.
  - SSL and caching provided by Cloudflare.

**Disadvantages:**
  - DNS propagation time can vary.
  - Requires familiarity with DNS setup.

---

### Step 2: Cloudflare API Integration
- **Action:**
  Created an API token on Cloudflare with appropriate permissions (DNS edit access) and securely stored it as a GitHub secret.

- **Rationale:**
  Automating DNS management via API allows programmatic updates without manual intervention.

- **Execution:**
  Used GitHub Actions to create a workflow that updates A records using Cloudflare’s API, with the token and zone ID securely stored in GitHub secrets.

**Advantages:**
  - Automates the DNS record updates and reduces manual errors.
  
**Disadvantages:**
  - API integration requires additional setup and knowledge of APIs.

---

### Step 3: GitHub Actions for CI/CD
- **Action:**
  Configured GitHub Actions with a self-hosted runner on Amazon EC2 or VPS, automating tasks like DNS management and application deployment.

- **Rationale:**
  Automating deployment reduces errors and provides continuous integration for updates.

- **Execution:**
  Stored secrets in GitHub and used GitHub variables for dynamic passing of information during workflow execution.

**Advantages:**
  - Streamlined deployment process.
  - CI/CD ensures fast and efficient deployments.

**Disadvantages:**
  - Requires setup of a self-hosted runner and knowledge of GitHub Actions.

---

### Step 4: Ansible Playbook for Server Provisioning
- **Action:**
  Created an Ansible playbook to install Docker, Docker Compose, and Nginx, and set up Nginx to forward traffic from port 80 to Docker.

- **Rationale:**
  Automating server provisioning ensures consistency and eliminates manual effort.

- **Execution:**
  The playbook installs and configures the necessary services, ensuring the domain is routed to the application running inside the Docker container.

**Advantages:**
  - Automated server provisioning ensures consistency.
  - Ansible is flexible and can handle complex infrastructure.

**Disadvantages:**
  - Requires prior knowledge of Ansible.
  - Debugging playbooks can take time if errors arise.

---

### Step 5: API Key Management
- **Action:**
  Created an OpenAPI key and stored it securely in GitHub secrets. This key is passed to the Ansible playbook for dynamic injection into the deployment configuration.

- **Rationale:**
  Securing API keys is crucial for protecting sensitive information during deployment.

- **Execution:**
  The API key is passed securely within the Ansible playbook, ensuring it’s not exposed during deployment.

**Advantages:**
  - API keys are securely managed.
  - Prevents unauthorized access.

**Disadvantages:**
  - Managing secrets adds complexity to the workflow.

---

## Resources Required

1. **Cloudflare API Token**: For DNS management.
2. **Zone ID**: For the Cloudflare DNS zone.
3. **AWS EC2 Access**: SSH access to the Ubuntu VPS.
4. **OpenAPI Key**: Required for Perplexica deployment.
5. **Docker and Docker Compose Configuration**: For deploying Perplexica on the VPS.

---

## Conclusion

This approach leverages automation through GitHub Actions and Ansible for a seamless deployment pipeline. Cloudflare provides security and performance optimizations, while Docker ensures consistent application deployment.

**Pros:**
- Automated, efficient setup process with minimal manual intervention.
- Enhanced security through SSL, DNS management, and secure API key handling.

**Cons:**
- Requires knowledge of multiple tools (Cloudflare, GitHub Actions, Docker, Ansible).
- Debugging issues may require familiarity with these tools.

If you have any further questions or need more details, feel free to ask.
## Workflow Daigram 
![FlowDiagram](https://github.com/user-attachments/assets/01c3c5e6-c36c-43bb-9529-e8d07be9f9d6)


# Full tutorial of my approach

## Server setup with github actions

- Here I launched the Ubuntu VPS (AWS EC2) using by open ssh port to my ip only and opening 80 and 443 to all of the others.
- Then I configured a Github runner and started runner and background service
- For that I went to repository **settings** under *code and automations* I clicked on **Actions** then click on **New Self hosted runner** choose **Linux** Ran the following command
```bash
# Create a folder
$ mkdir actions-runner && cd actions-runner
# Download the latest runner package
$ curl -o actions-runner-linux-x64-2.319.1.tar.gz -L https://github.com/actions/runner/releases/download/v2.319.1/actions-runner-linux-x64-2.319.1.tar.gz
# Optional: Validate the hash
$ echo "3f6efb7488a183e291fc2c62876e14c9ee732864173734facc85a1bfb1744464  actions-runner-linux-x64-2.319.1.tar.gz" | shasum -a 256 -c
# Extract the installer
$ tar xzf ./actions-runner-linux-x64-2.319.1.tar.gz
```

- Then configure as it showed it sonfigure section
```bash
./config.sh --url https://github.com/username/Perplexica --token mytoken
```
- Then run as backgroud service
```bash
sudo ./svc.sh install
sudo ./svc.sh start
```

## Namecheap and Cloudflare integration

- Sign up to cloudflare go to cloudflare click on **add domain** put your domain name that you have registered already.
- Now grab the nameservers for the cloudflare after that go to namecheap dashboard click on manage dns and Under Nameservers click on the custom dns then paste the nameservers provided there.
- Wait for 5-10 mins to propagate the dns.

## Cloudflare zoneid and api token and github actions

- Click on your domain then scrll down until you find the zone id copy it and store it secure place

1. Generate a Cloudflare API Token
    - Go to your Cloudflare dashboard.
    - Navigate to My Profile > API Tokens.
    - Click Create Token and select the Edit zone DNS template.
    - Select the zone (your domain) you want to manage.
    - Save the generated token securely.
2. Store API Token and Zone ID in GitHub Secrets
    - Go to your GitHub repository.
    - Navigate to Settings > Secrets and variables > Actions > New repository secret.
    - Add two secrets:
    - `CLOUDFLARE_API_TOKEN`: Your Cloudflare API token.
    - `CLOUDFLARE_ZONE_ID`: The Cloudflare Zone ID for your domain.

## Open AI API Key and Github Actions

- To generate an OpenAI API key token, follow these steps:

1. Create an OpenAI Account:

    - Go to the OpenAI website.
    - Click on the "Sign Up" button to create a new account or "Log In" if you already have one.
    Access the API Section:

    Once logged in, navigate to the API section. This is typically found in the dashboard or by visiting the API Documentation.
    - Generate API Key:

        - In the API section, look for a button or link that says "API Keys" or "Create API Key."
        Click on this option, and you will be prompted to create a new key.
        Follow any additional instructions, and your new API key will be generated.


2. Store API Token a GitHub Secrets
    - Go to your GitHub repository.
    - Navigate to Settings > Secrets and variables > Actions > New repository secret.
    - Add two secrets:
    - `OPENAPI_KEY`: Your OPENAI API token.

## Ansible playbook 

**Since we are using github runner we will run ansible-playbook as localhost**

- Create a ansible folder then create a yaml file called `main.yaml`

```yaml
- hosts: localhost
  connection: local
  become: true
  roles:
    - role: docker
    - role: nginx

```


- After that create a ansible roles for that create a  folder  `roles` cd into `./ansible/roles` run the following commands to create `docker` and `nginx` role

```bash
ansible-galaxy init docker
ansible-galaxy init nginx
```

- After edit a task.yaml file for docker which located in `./ansible/roles/docker/tasks/main.yml` in this file we are installing docker and docker compose after that we are configuring docker to be run on our server then we are loading our config.toml from the templates directory and also we are running docker-compose to spin up the machine
```yaml
# tasks file for docker
- name: Update Cache
  apt:
    update_cache: yes

- name: Update Docker and docker-compose
  apt:
    name: 
      - docker.io
      - docker-compose-v2
    state: latest
    update_cache: yes

- name: Start docker
  service:
    name: docker
    state: started
    enabled: yes

- name: Add user to docker group
  user:
    name: ubuntu
    groups: docker
    append: yes

- name: Install latest Docker SDK for Python
  apt:
    name: python3-docker
    state: latest
    update_cache: yes

- name: Clone a GitHub repository
  git:
    repo: https://github.com/ItzCrazyKns/Perplexica.git
    dest: /home/ubuntu/Perplexica
    clone: yes
    update: yes
  become: true

- name: Copy config.toml
  template:
    src: config.toml.j2
    dest: /home/ubuntu/Perplexica/config.toml

- name: Run Docker Compose
  command: docker compose up --build -d
  args:
    chdir: /home/ubuntu/Perplexica
  become: true
```

- After that we will create a dynamic `config.toml.j2` file in `./ansible/roles/docker/templates` directory so that we can load our secrets dynamically and securely from github actions
```jinja
[GENERAL]
PORT = 3001 # Port to run the server on
SIMILARITY_MEASURE = "cosine" # "cosine" or "dot"

[API_KEYS]
OPENAI = "{{ openapi_key }}" # OpenAI API key - sk-1234567890abcdef1234567890abcdef
GROQ = "{{ groq_key }}" # Groq API key - gsk_1234567890abcdef1234567890abcdef
ANTHROPIC = "{{ anthropic_key }}" # Anthropic API key - sk-ant-1234567890abcdef1234567890abcdef

[API_ENDPOINTS]
SEARXNG = "http://localhost:32768" # SearxNG API URL
OLLAMA = "" # Ollama API URL - http://host.docker.internal:11434
``` 

- After we will edit the variables files to load the contents from ansible-playbook command `./ansible/roles/docker/vars/main.yml`
```yaml
---
# vars file for nginx
domain_name: "mydomain.example.com"
email_user: "example@example.com"
```

- Now let's configure nginx for that edit `./ansible/roles/nginx/tasks/main.yml` here we are installing nginx and configuring nginx and also load nginx configuration to forward port 3000 to 80
```yaml
---

- name: Update the apt package index
  apt:
   update_cache: yes

- name: Install Nginx and certbot
  apt:
    name: 
      - nginx
      - certbot
      - python3-certbot-nginx
    state: present

- name: Remove nginx default configuration
  file:
    path: /etc/nginx/sites-enabled/default
    state: absent

- name: Copy Nginx configuration
  template:
    src: ai.conf.j2
    dest: /etc/nginx/sites-available/ai.conf

- name: Enable Nginx configuration for Our App
  file:
    src: /etc/nginx/sites-available/ai.conf
    dest: /etc/nginx/sites-enabled/ai.conf
    state: link
  become: true

- name: Test Nginx configuration
  command: nginx -t
  become: true

- name: Restart Nginx
  service:
    name: nginx
    state: restarted
  become: true

```

- Now let's create the `ai.conf.j2` file in `./ansible/roles/nginx/templates` that will forward proxy and load domain dynamically from github actions
```jinja
server {
    listen 80;
    server_name {{ domain_name }}; 

    location / {
        proxy_pass http://localhost:3000;  # Forward requests to Grafana
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
   
}

``` 

- Now let's create a vars file that will create a domain name a load from the github actions on ansible playbook command `./ansible/roles/nginx/vars/main.yml`

```yaml
---
# vars file for nginx
domain_name: "mydomain.example.com"
```

## Creating Github actions variables and workflow file for ci 

We need to create a varaible for that 
- Go to your GitHub repository.
- Navigate to Settings > Secrets and variables > Actions > New repository Variable.
    - Add following varaibles
        - `RECORD_NAME`: Record name i.e `ai.rijalnitesh.com`
        - `PUBLIC_IP`: Public ip of your server

- Let's create a github actions create a folder called `workflows` in `.github` folder then create a file called `main.yaml`  so in this file we are create `A` record in cloudflare using the API loading the api and secrets and installing ansible and running ansible playbook.

```yaml
name: Add A Record to Cloudflare

on:
  workflow_dispatch:
  push:
    branches:
      - master  # or any branch you want this workflow to run on

jobs:
  update-dns:
    runs-on: self-hosted
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Add A Record to Cloudflare DNS
        env:
          CLOUDFLARE_API_TOKEN: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          CLOUDFLARE_ZONE_ID: ${{ secrets.CLOUDFLARE_ZONE_ID }}
          RECORD_NAME: ${{ vars.RECORD_NAME }}  # Change this to your desired record
          RECORD_TYPE: "A"
          RECORD_CONTENT: ${{ vars.PUBLIC_IP }}  # Replace with the IP address
        run: |
          curl -X POST "https://api.cloudflare.com/client/v4/zones/$CLOUDFLARE_ZONE_ID/dns_records" \
          -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" \
          -H "Content-Type: application/json" \
          --data '{
            "type": "'"$RECORD_TYPE"'",
            "name": "'"$RECORD_NAME"'",
            "content": "'"$RECORD_CONTENT"'",
            "ttl": 120,
            "proxied": true
          }'
          sleep 30


  deploy_appilcation:
    runs-on: self-hosted
    steps:
      - name: Install  necessary package
        run: |
          sudo apt update
          sudo apt install -y ansible
      
      - name: Run Ansible playbook
        env:
          RECORD_NAME: ${{ vars.RECORD_NAME }}
          OPENAPI_KEY: ${{ secrets.OPENAPI_KEY }}
        working-directory: ansible
        run: |
          ansible-playbook -e "domain_name=$RECORD_NAME openapi_key=$OPENAPI_KEY" main.yaml 

```

Now we can access ai.rijalnitesh.com
