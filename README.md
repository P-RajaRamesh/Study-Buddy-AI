# 📖 Study Buddy AI
A generative AI-powered study assistant that creates multiple-choice and fill-in-the-blank questions based on user-defined topics. 
Built with LangChain and Groq LLMs, this app streamlines quiz generation and delivers an interactive learning experience via a Streamlit UI.

## 🔧 Core Workflow
- **🗣️ User Query:** Users input comma-separated topics, choose question type (MCQ or Fill-in-the-Blank), and set no. of questions.
- **🧠 LLM Processing:** GROQ-powered LLM interprets the inputs & generate question based on user inputs.
- **🐍 Pydantic:** Responses from the LLM are parsed into well-defined MCQQuestion and FillBlankQuestion objects using Pydantic schemas.
- **🖥️ Frontend Interface:** A Streamlit app provides an interactive UI for users to input topics, question type and no. of questions.
- **🐳 Deployment:** Containerized & stored on DockerHub, application is deployed to Kubernetes cluster running on Google Cloud VM, with CI/CD pipelines powered by Jenkins & ArgoCD.
- **🌐 Github:** Github servers as SCM & code version for Jenkins and ArgoCD integration.

## 📦 Installating & Running Locally
- **Clone this repo & CD Study-Buddy-AI :**
  ```
  https://github.com/P-RajaRamesh/Study-Buddy-AI.git
  ```
- **Create Virtual Environment :** ```conda create -p venv1 python==3.10```
- **Activate Virtual Environment :** ```conda activate venv1\```
- **Install Requirements :** ```pip install -e .```
- **Create ```.env``` file:** ```GROQ_API_KEY="<YOUR-GROQ-API-KEY>"```
- Finally Run Streamlit app: ```streamlit run application.py```

## 💻 Pushing code to your Github
```
git init
git add .
git commit -m "commit"
git branch -m main
git remote add origin https://github.com/P-RajaRamesh/Study-Buddy-AI.git
git push origin main
```

## ☁️ Deploying in Google CLoud VM:
### 1. Goto VM Instances and click **"Create Instance"**
  - Name: `Anime-Recommender-System`
  - Machine Type:
    - Series: `E2`
    - Preset: `Standard`
    - Memory: `16 GB RAM`
  - Boot Disk:
    - Change size to `150 GB`
    - Image: Select **Ubuntu 24.04 LTS**
  - Networking:
    - Enable HTTP and HTTPS traffic
  - **Create the Instance**
---

### 2. Connect to the VM
  - Use the **SSH** option provided to connect to the VM from the browser.
---

### 3. Install Docker
  - Search: "Install Docker on Ubuntu"
  - Open the first official Docker website (docs.docker.com) - https://docs.docker.com/engine/install/ubuntu/
  - Scroll down and copy the **first big command block** and paste into your VM terminal : 1. Set up Docker's apt repository.
     ```
      # Add Docker's official GPG key:
      sudo apt-get update
      sudo apt-get install ca-certificates curl
      sudo install -m 0755 -d /etc/apt/keyrings
      sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
      sudo chmod a+r /etc/apt/keyrings/docker.asc
      
      # Add the repository to Apt sources:
      echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
     ```
  - Then copy and paste the **second command block** : 2. Install the Docker packages.
    ```
    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
  - Then run the **third command** to test Docker: 3. You will get error, you need to use sudo before it

    ```bash
    docker run hello-world
    ```

  - **Run Docker without sudo**
    - On the same page, scroll to: **"Post-installation steps for Linux"** : https://docs.docker.com/engine/install/linux-postinstall/
    - Paste all 4 commands one by one to allow Docker without `sudo`
      1. Create the docker group: ```sudo groupadd docker```      
      2. Add your user to the docker group: ```sudo usermod -aG docker $USER``` 
         - Log out and log back in so that your group membership is re-evaluated.
      If you're running Linux in a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.
      3. You can also run the following command to activate the changes to groups: ```newgrp docker```    
      4. Verify that you can run docker commands without sudo: ```docker run hello-world```

  - **Enable Docker to start on boot**
    - On the same page, scroll down to: **"Configure Docker to start on boot"**
    - Copy and paste the command block (2 commands one-by-one):
  
      ```bash
      sudo systemctl enable docker.service
      sudo systemctl enable containerd.service
      ```

  - **Verify Docker Setup**

  ```bash
  docker version               # Check the docker version
  systemctl status docker      # You should see "active (running)"
  docker ps                    # No container should be running
  docker ps -a                 # Should show "hello-world" exited container
  ```
---

### 4. Install Minikube
  - Open browser and search `Install Minikube` 
  - Open the first official site (minikube.sigs.k8s.io) with `minikube start` on it: : https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download
  - Choose:
    - **OS:** Linux
    - **Architecture:** *x86*
    - Select **Binary download**
    - To install the latest minikube stable release on x86-64 Linux using binary download:
      1. Copy and run the below commands one-by-one
      ```
      curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
      sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
      ```
      2. Start your cluster: ```minikube start```
      - This uses Docker internally, which is why Docker was installed first
---

### 5. Install kubectl
  - Search: `Install kubectl on Ubuntu` : https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
  - Run the first command with `curl` from the official Kubernetes docs: 1. Download the latest release with the command:
    ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```
  - Go to the **Snap section** (below on the same page) : ```sudo snap install kubectl --classic```
  - Verify installation: ```kubectl version --client```
---

### 6. Create a firewall rule in Google Cloud Console
  - Search for firewall
  - Create a firewall rule
  - Name the firewall : `study-buddy-ai-firewall`
  - Direction of traffic : Ingress
  - Action on match : Allow
  - Targets : All instances in the network
  - Source filter : IPv4 ranges
  - Source IPv4 ranges : 0.0.0.0/0
  - Protocols and ports : Allow All
---


### 7. 🔑 Generate GitHub Personal Access Token
- Goto Github Settings -> Developer Settings -> Personal Access Token -> Generate new token (classic)
- Give the following access permissions for the token: 
  ```
  admin:org
  admin:org_hook
  admin:public_key
  admin:repo_hook
  repo
  workflow
  ```

### 8. DockerHub Setup
#### 🐳 Create DockerHub Repository
1. Go to https://hub.docker.com
2. Create a new repository `studybuddy`
3. Now my docker image repo will be *rajaramesh7410/studybuddy*
#### 🔑 Generate DockerHub Access Token
1. Go to DockerHub Home Page -> Settings -> Security -> Personal Access Token -> Generate new token
2. Give some token discription `studybuddyai` and keep the Expiration Date as **None**
3. Give access permission as *Read, Write, Delete* and click **Generate**

---

### 9. Jenkins Setup
- **Run Jenkins in Docker (DIND Mode- Docker-in-Docker)**
  - First, check existing networks:
    ```bash
    docker network ls
    ```
  - Now follow below steps to make sure Jenkins runs on **same Docker network** as Minikube.
  - Run Jenkins container:
    ```bash
    docker run -d --name jenkins \
    -p 8080:8080 \
    -p 50000:50000 \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v $(which docker):/usr/bin/docker \
    -u root \
    -e DOCKER_GID=$(getent group docker | cut -d: -f3) \
    --network minikube \
    jenkins/jenkins:lts
    ```

- **Verify Jenkins Container**
  ```bash
  docker ps                  # Jenkins container should be running
  docker logs jenkins        # Copy the admin password shown here
  ```

- **Access Jenkins Web UI**
  - Go to your VM dashboard in GCP
  - Copy the **External IP (public DNS)** and open:  `http://<EXTERNAL_IP>:8080`

- **Jenkins Setup Steps**
  - Paste the initial password from `docker logs jenkins`
  - Click **Install Suggested Plugins**
  - Create Admin User
  - Skip agent security warning (ignore for now)

- **Install Required Plugins** in Jenkins Web UI
  - Navigate to: **Manage Jenkins → Plugins**
    - Install:
      - Docker
      - Docker Pipeline
      - Kubernetes

- **Restart Jenkins** in SSH
  ```bash
  docker restart jenkins
  ```
  - Refresh Jenkins Web UI and Log in again after restart.

- **Install Python and Pip inside Jenkins Container** - **Ignore** (*not required*)
  ```bash
  docker exec -it jenkins bash
  apt update -y
  apt install -y python3
  python3 --version
  ln -s /usr/bin/python3 /usr/bin/python
  python --version
  apt install -y python3-pip
  apt install -y python3-venv
  exit
  ```

- **Restart Jenkins Again** in SSH
  ```bash
  docker restart jenkins
  ```

✅ Jenkins is now fully set up and ready to use!

---

### 10. Github Integration with Jenkins
#### 🔐 Add GitHub Credentials to Jenkins
- Go to: **Manage Jenkins → Credentials → Global → Add Credentials**
  - **Username**: `P-RajaRamesh`
  - **Password**: The token you just generated at step 7.
  - **ID**: `github-token`
  - **Description**: `github-token`

---

#### 🚀 Create a New Pipeline Job in Jenkins
1. Go to Jenkins Dashboard → **New Item**
2. Enter **Name**: `Study-Buddy-AI`
3. Select **Pipeline**
4. Scroll to the **Pipeline** section:
   - Select **Pipeline from SCM**
   - Choose **Git**
   - **Repository URL**: `https://github.com/P-RajaRamesh/Study-Buddy-AI.git`
   - **Credentials**: Select the `github-token` credential
   - **Branch**: `main`

---

#### 📄 Update `Jenkinsfile` in VSCode
- In Jenkinsfile, comment out the below blocks & stages:
  ```
  environment block
  Update Deployment YAML with New Tag
  Commit Updated YAML
  Install Kubectl & ArgoCD CLI Setup
  Apply Kubernetes & Sync App with ArgoCD
  ```
- Open **Pipeline Syntax** in a Jenkins Web UI under your `Study-Buddy-AI` pipeline
- Provide Pipeline for for SCM, Git, Repo URL, Branch as main and click generate pipeline syntax
- Now copy that syntax, goto VSCode and paste in Jenkinsfile under `Checkout Github` stage at `checkout scmGit(...`
- Save the Jenkinsfile in VSCode and push to github by following below steps:
  ```
  git add .
  git comit -m "commit Checkout Github stage"
  git push origin main
  ```
---

#### ✅ Final Jenkins Test
- Go back to Jenkins Dashboard
- Click on your `Study-Buddy-AI` pipeline
- Click **Build Now**

If successful ✅, GitHub is now **fully integrated** with Jenkins!

---

### 11. Build and Push Docker Image to DockerHub
#### ⚙️ Configure Docker Tool in Jenkins
1. Go to **Jenkins Dashboard → Manage Jenkins → Tools**
2. Scroll down to **Docker Installations**
3. Click **Add Docker**
   - **Name**: `Docker`
   - ✅ Check **Install automatically**
   - Select **Install from docker.com**
4. Click **Apply and Save**

---

#### 🔐 Add DockerHub Credentials to Jenkins
1. Go to **Jenkins → Manage Jenkins → Credentials → Global → Add Credentials**
   - **Username**: DockerHub username `rajaramesh7410`
   - **Password**: DockerHub token generated at step 8.
   - **ID**: `dockerhub-token`
   - **Description**: `dockerhub-token`

---

#### 📄 Update `Jenkinsfile` in VSCode
- Un-comment the below blocks & stages in VSCode Jenkinsfile.
  ```
  environment block
  Build Docker Image
  Push Image to DockerHub
  ```
- Save the Jenkinsfile in VSCode and push to github by following below steps:
  ```
  git add .
  git comit -m "commit Docker Build & Push Image to DockerHub"
  git push origin main
  ```
---

#### 🚀 Trigger Jenkins Pipeline
1. Go to Jenkins Dashboard
2. Click on your pipeline (`Study-Buddy-AI`)
3. Click **Build Now**

✅ If successful, your image will be available on DockerHub Repositories

---

### 12. Install and Configure ArgoCD - Part 1
#### 🧐 Step 1: Check Existing Namespaces
```bash
kubectl get namespace
```
---

#### 🆕 Step 2: Create New Namespace for ArgoCD
```bash
kubectl create ns argocd
```
✅ Run `kubectl get ns` to verify the namespace is created.

---

#### 📥 Step 3: Install ArgoCD
Apply the ArgoCD installation manifest from GitHub Official:
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
---

#### 🔍 Step 4: Validate ArgoCD Components
Check all resources inside the `argocd` namespace:
```bash
kubectl get all -n argocd
```
✅ Wait until all resources (pods, deployments, replicasets, etc.) are in **Running** or **Completed** state.  
⛔ Do **not proceed** if anything is in **Pending** or **CrashLoopBackOff** state.

---

#### 🔌 Step 5: Check ArgoCD Service Type
```bash
kubectl get svc -n argocd
```
You’ll notice that `argocd-server` is of type **ClusterIP**, which is only accessible within the cluster.
We need to change it to **NodePort** to access the UI externally.

---

#### 🔧 Step 6: Change ClusterIP to NodePort
- Edit the service:
```bash
kubectl edit svc argocd-server -n argocd
```
- Find: `type: ClusterIP`
- Press `ins` key and replace with: `type: NodePort`
- Then press: `esc` key -> Enter `:wq!` to exit
- Now re-run:
```bash
kubectl get svc -n argocd
```
✅ You will now see `argocd-server` with a **NodePort**.

---

#### 🌐 Step 7: Access ArgoCD UI in Browser
- Run the below command to get the argocd password:
  ```
  kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  ```
- Open a **new SSH terminal** and run port-forward command to access argocd web UI externally:
```bash
kubectl port-forward --address 0.0.0.0 service/argocd-server 31704:80 -n argocd
```
- Now open your browser & Enter: `http://<VM_PUBLIC_IP>:31704`
- You may see a privacy warning—proceed anyway

✅ You’ll land on the ArgoCD **login page**

---

#### 🔐 Step 8: Get ArgoCD Admin Password
- After entering ArgoCD login page enter :
- **Username**: `admin`
- **Password**: (copy from above SSH terminal)

✅ Login and you’re now inside the ArgoCD UI 🎉

---

### 13. Install and Configure ArgoCD – Part 2
#### ⚙️ Step 1: Locate Your Kubernetes Config File ( Already given this file in Course Materials Download from there )



Go to the root directory:

```bash
cd ~
ls -la
```

You’ll see a hidden directory `.kube/` — this stores your Kubernetes configuration.

Check the content:

```bash
ls -la .kube/
cat .kube/config
```

Copy the entire content of `.kube/config` into a Notepad for backup and modification.

---

#### 🔐 Step 2: Convert File Paths to Base64 Encoded Strings

The `config` file references files like:

- `/home/gyrogodnon/.minikube/ca.crt`
- `/home/gyrogodnon/.minikube/profiles/minikube/client.crt`
- `/home/gyrogodnon/.minikube/profiles/minikube/client.key`

We’ll **inline** the actual base64 content instead of using file paths.

##### 🔁 For Each of These 3 Files, Run:

```bash
cat /home/gyrogodnon/.minikube/ca.crt | base64 -w 0; echo
cat /home/gyrogodnon/.minikube/profiles/minikube/client.crt | base64 -w 0; echo
cat /home/gyrogodnon/.minikube/profiles/minikube/client.key | base64 -w 0; echo
```

Copy each base64 string and replace the corresponding `certificate-authority-data`, `client-certificate-data`, and `client-key-data` fields in your config file.

---

#### 📝 Step 3: Save Edited Kubeconfig File

- Save this new file as `kubeconfig` (no `.txt` extension) in your **Downloads** folder.

Now open Git Bash and run:

```bash
cd ~/Downloads
vi config
```

Paste the full edited config content.

Save it:

- Press `Esc`, then type `:wq!` and hit Enter.

---

#### 🔒 Step 4: Add kubeconfig as Secret File in Jenkins

- Go to **Jenkins Dashboard → Manage Jenkins → Credentials**
- Select: **Global → Add Credentials**
- Choose: **Kind: Secret file**
- Upload your edited `config` file
- Set:
  - **ID**: `kubeconfig`
  - **Description**: `kubeconfig`

Click Save ✅

---

#### ☁️ Step 5: Set Up Kubernetes Cluster Access in Jenkins Pipeline

1. Go to Jenkins Dashboard → Pipelines → Open your `GitOps` pipeline
2. Click **Configure**
3. Scroll down to **Pipeline section**
4. Click **Pipeline Syntax** → Opens in a new tab
5. Select:
   - **Sample Step**: `kubernetes deploy`
   - **Kubeconfig**: select `kubeconfig` credential
   - **Server URL**: Get from this command:
     ```bash
     kubectl cluster-info
     ```
     (e.g., `https://192.168.49.2:8443`)
6. Generate the script

Copy the generated script and paste/save it — you’ll use it in your Jenkinsfile in the next stage.

---

✅ At this point, your Jenkins instance is fully connected to your Kubernetes cluster using a secure kubeconfig setup.

---

# 9. Install and Configure ArgoCd - Part 3


### Step 1: Install `kubectl` and ArgoCD CLI on Docker Container

- Open **VS Code** and navigate to your **Jenkinsfile**.
- Copy-paste the code snippet you have for installing **ArgoCD** and **kubectl**.
- This snippet will be used in the pipeline.

---

### Step 2: Apply Kubernetes & Sync App with ArgoCD Stage

- Inside the pipeline stage, create a script block.
- Paste the copied installation commands inside the script.
- Replace the placeholder IP address with your **own ArgoCD server IP**.
  
```groovy
sh '''
argocd login 34.72.5.170:31704 --username admin --password $(kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d) --insecure
'''
````

> **Note:** Change `34.72.5.170:31704` to your ArgoCD server IP and port.

---

### Step 3: Connect GitHub Repository to ArgoCD

1. Open **ArgoCD UI** → Go to **Settings** → **Repositories** → **Connect Repo** via HTTPS.
2. Fill in details:

   * **Type:** git
   * **Name:** anything you want
   * **Project:** default
   * **Repo URL:** `https://github.com/data-guru0/GitOPS-testing.git`
   * **Username & Password:** Provide GitHub username and token (optional but recommended)
3. Click **Connect**.
4. You should see a success message confirming the GitHub repo is connected to ArgoCD.

---

### Important:

```groovy
kubectl create secret generic groq-api-secret \
  --from-literal=GROQ_API_KEY="" \
  -n argocd
```

### Step 4: Create a New Application in ArgoCD

* Go to **Applications** → Click **New App**.
* Fill in the form:

  * **Name:** Gitops (or any name you prefer)
  * **Project:** default
  * **Sync Policy:** Automatic
  * Tick **Sync Pipeline Resources** and **Self Heal**.
  * Leave other settings as default.
  * **Repository URL:** select your connected repo.
  * **Revision:** `main` (branch)
  * **Path:** `manifests`
  * **Cluster URL:** select from dropdown.
  * **Namespace:** `argocd`
* Click **Create**.
* You should see the application status as **Synced** and **Healthy**.

---

### Step 5: Modify Jenkinsfile to Sync ArgoCD Application

* In **VS Code**, open your `Jenkinsfile`.
* In the last stage, add the command to sync the ArgoCD app:

```groovy
sh 'argocd app sync gitopsapp'
```

> Replace `gitopsapp` with the actual name of your ArgoCD application.

* Push the changes to GitHub.
* Go to Jenkins and build the pipeline.
* On success, you will see a success message.

---

### Step 6: Verify ArgoCD Application and Logs

* Open **ArgoCD UI**.
* Check the application workflow.
* View logs for each pod to verify deployment.

---

### Step 7: Access Your Application

* On your VM instance terminal, run:

```bash
kubectl get deploy -n argocd
```

* You should see your `mlops-app` deployment.
* Check pods:

```bash
kubectl get pods -n argocd
```

* You should see your pods running.

---

### Step 8: Allow External Access

* Run the following command to create a tunnel:

```bash
minikube tunnel
```

* Open another SSH terminal and run port-forwarding:

```bash
kubectl port-forward svc/my-service -n argocd --address 0.0.0.0 9090:80
```

---

### Step 9: Access the Application from Browser

* Copy your VM’s external IP address.
* Open browser and go to:

```
http://<VM_EXTERNAL_IP>:9090
```

* You should see your `mlops-app` running successfully!


# 10. Setup Webhooks

---

### Step 1: Add Webhook in GitHub Repository

1. Go to your **GitHub repo** → **Settings** → **Webhooks** → **Add webhook**.
2. Fill in the details:
   - **Payload URL:**  
     `http://34.72.5.170:8080/github-webhook/`  
     *(Replace with your Jenkins URL)*
   - **Content type:** `application/json`
   - **Secret:** *(Not necessary, leave blank)*
   - **Enable SSL verification:** Enable if using HTTPS
3. Under **Which events would you like to trigger this webhook?**  
   - Tick **Just the push event**  
     (This means the pipeline triggers on every push)
4. Click **Add webhook**.

---

### Step 2: Configure Jenkins to Receive Webhook

1. Open **Jenkins** → Go to your **Pipeline** job → Click **Configure**.
2. Scroll down to **Build Triggers**.
3. Tick **GitHub hook trigger for GITScm polling**.
4. Click **Apply** and **Save**.
5. Your webhook trigger is now configured.

---

### Step 3: Test the Webhook Trigger

1. Open **VS Code**.
2. Make a slight change in the `Jenkinsfile` (e.g., add or modify an `echo` statement for demonstration).
3. Commit and **push** the code to GitHub.
4. Go to Jenkins Dashboard.
5. You should see your Jenkins pipeline **automatically triggered** and start running.

---

### Final Outcome

- Jenkins will automatically trigger ArgoCD sync as part of the pipeline.
- This completes the full GitOps pipeline successfully and automatically!

---



















