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

## 💻 Installating & Running Locally
- **Clone this repo & CD Study-Buddy-AI :**
  ```
  https://github.com/P-RajaRamesh/Study-Buddy-AI.git
  ```
- **Create Virtual Environment :** ```conda create -p venv1 python==3.10```
- **Activate Virtual Environment :** ```conda activate venv1\```
- **Install Requirements :** ```pip install -e .```
- **Create ```.env``` file:** ```GROQ_API_KEY="<YOUR-GROQ-API-KEY>"```
- Finally Run Streamlit app: ```streamlit run application.py```

## 🌐 Pushing code to your Github
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
  - Name: `study-buddy-ai-vm-instance`
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

### 3. 🐋 Install Docker
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

    ```
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
  
      ```
      sudo systemctl enable docker.service
      sudo systemctl enable containerd.service
      ```

  - **Verify Docker Setup**

  ```
  docker version               # Check the docker version
  systemctl status docker      # You should see "active (running)"
  docker ps                    # No container should be running
  docker ps -a                 # Should show "hello-world" exited container
  ```
---

### 4. 🧊 Install Minikube
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

### 5. </> Install kubectl
  - Search: `Install kubectl on Ubuntu` : https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/
  - Run the first command with `curl` from the official Kubernetes docs: 1. Download the latest release with the command:
    ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```
  - Go to the **Snap section** (below on the same page) : ```sudo snap install kubectl --classic```
  - Verify installation: ```kubectl version --client```
---

### 6. 🧱 Create a firewall rule in Google Cloud Console
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

### 8. 🐋 DockerHub Setup
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
    ```
    docker network ls
    ```
  - Now follow below steps to make sure Jenkins runs on **same Docker network** as Minikube.
  - Run Jenkins container:
    ```
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
  ```
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
  - Navigate to: **Manage Jenkins** (⚙️) -> **Plugins** (🧩) -> **Available plugins**
    - Install:
      - ✅ Docker
      - ✅ Docker Pipeline
      - ✅ Kubernetes

- **Restart Jenkins** in SSH
  ```
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
  ```
  docker restart jenkins
  ```

✅ Jenkins is now fully set up and ready to use!

---

### 10. 🤝 Github Integration with Jenkins
#### 🔐 Add GitHub Credentials to Jenkins
- Go to: **Manage Jenkins** (⚙️) -> **Credentials** -> **Global** -> ➕ **Add Credentials**
  - **Kind**: Username with password
  - **Username**: `P-RajaRamesh`
  - **Password**: The token you just generated at step 7.
  - **ID**: `github-token`
  - **Description**: `github-token`
  - **Create**

---

#### 🚀 Create a New Pipeline Job in Jenkins
1. Go to Jenkins Dashboard -> ➕ **New Item**
2. Enter **Name**: `Study-Buddy-AI`
3. Select **Pipeline** & click **Ok**
4. Scroll to the **Pipeline** section:
   - Select Definition: **Pipeline from SCM**
   - Choose SCM: **Git**
   - **Repository URL**: `https://github.com/P-RajaRamesh/Study-Buddy-AI.git`
   - **Credentials**: Select the `github-token`
   - **Branch**: `main`
   - CLick **Apply** & **Save**

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
- Click **Pipeline Syntax** (?) in a Jenkins Web UI under your `Study-Buddy-AI` pipeline
- Provide
  - Sample Step: **checkout: Check out from version control**
  - SCM: **Git**
  - Repository URL: `https://github.com/P-RajaRamesh/Study-Buddy-AI.git`
  - **Credentials**: Select the `github-token`
  - **Branch**: `main`
  - Click **Generate Pipeline Script**
- Now copy that syntax, goto VSCode and paste in Jenkinsfile under `Checkout Github` stage at `checkout scmGit(...`
- Save the Jenkinsfile in VSCode and push to github by following below steps:
  ```
  git add .
  git comit -m "commit Checkout Github stage"
  git push origin main
  ```
---

#### 🧪 Final Jenkins Test
- Go back to Jenkins Dashboard
- Click on your `Study-Buddy-AI` pipeline
- Click **Build Now**

If successful ✅, GitHub is now **fully integrated** with Jenkins!

---

### 11. 🔨 Build and Push Docker Image to DockerHub
#### ⚙️ Configure Docker Tool in Jenkins
1. Go to **Jenkins Dashboard** -> **Manage Jenkins** (⚙️) -> **Tools** (🔨)
2. Scroll down to **Docker Installations**
3. Click **Add Docker**
   - **Name**: `Docker`
   - ✅ Check **Install automatically**
   - Select **Download from docker.com**
4. Click **Apply and Save**

---

#### 🔐 Add DockerHub Credentials to Jenkins
1. Go to **Manage Jenkins** (⚙️) → **Credentials**  -> **Global** -> ➕ **Add Credentials**
   - **Kind**: Username with password
   - **Username**: DockerHub username `rajaramesh7410`
   - **Password**: DockerHub token generated at step 8.
   - **ID**: `dockerhub-token`
   - **Description**: `dockerhub-token`
   - Click **Create**

---

#### 📄 Update `Jenkinsfile` in VSCode
- Un-comment the below blocks & stages in VSCode Jenkinsfile.
  ```
  environment block
  Build Docker Image
  Push Image to DockerHub
  ```
- Make sure to update your Dockerhub repo name in `Jenkinsfile` & `deployment.yaml` file:
- Change the IMAGE_TAG to `v1` in Jenkinsfile
- Update **image:** of `deployment.yaml` file with `rajaramesh7410/studybuddy:v1`
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

✅ If successful, your image will be available on DockerHub Repositories with `v1` image tag

---

### 12. 🐙 ArgoCD - Part 1
#### 🧐 Step I: Check Existing Namespaces
```
kubectl get namespace
```
---

#### 🆕 Step II: Create New Namespace for ArgoCD
```
kubectl create ns argocd
```
✅ Run `kubectl get ns` to verify the namespace is created.

---

#### 📥 Step III: Install ArgoCD
Apply the ArgoCD installation manifest from GitHub Official:
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
---

#### 🔍 Step IV: Validate ArgoCD Components
Check all resources inside the `argocd` namespace:
```
kubectl get all -n argocd
```
✅ Wait until all resources (pods, deployments, replicasets, etc.) are in **Running** or **Completed** state.  
⛔ Do **not proceed** if anything is in **Pending** or **CrashLoopBackOff** state.

---

#### 🔌 Step V: Check ArgoCD Service Type
```
kubectl get svc -n argocd
```
You’ll notice that `argocd-server` is of type **ClusterIP**, which is only accessible within the cluster.
We need to change it to **NodePort** to access the UI externally.

---

#### 🔧 Step VI: Change ClusterIP to NodePort
- Edit the service:
```
kubectl edit svc argocd-server -n argocd
```
- Find: `type: ClusterIP`
- Press `ins` key and replace with: `type: NodePort`
- Then press: `esc` key -> Enter `:wq!` to exit
- Now re-run:
```
kubectl get svc -n argocd
```
✅ You will now see `argocd-server` with a **NodePort**.

---

#### 🌐 Step VII: Access ArgoCD UI in Browser
- Run the below command to get the argocd password:
  ```
  kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
  ```
- Open a **new SSH terminal** and run port-forward command to access argocd web UI externally:
```
kubectl port-forward --address 0.0.0.0 service/argocd-server 31704:80 -n argocd
```
- Now open your browser & Enter: `http://<VM_PUBLIC_IP>:31704`
- ⚠️ You may see a privacy warning — proceed anyway...

✅ You’ll land on the ArgoCD **login page**

---

#### 🔐 Step VIII: Get ArgoCD Admin Password
- After entering ArgoCD login page enter :
- **Username**: `admin`
- **Password**: (copy from above Step VII SSH terminal)

✅ Login and you’re now inside the ArgoCD UI 🎉

---

### 13. 🐙 ArgoCD – Part 2
#### 📍 Step I: Locate Your Kubernetes Config File 
- Go to the root directory & lists all files and directories:
  ```
  cd ~
  ls -la
  ```
- You’ll see a hidden directory `.kube/` — this stores your Kubernetes configuration.
- Check the files in `.kube` & display content of the `config` file:
  ```
  ls -la .kube/
  cat .kube/config
  ```
- Copy the entire content of `.kube/config` into a Notepad++ for backup and modification.

---

#### 🔐 Step II: Convert File Paths to Base64 Encoded Strings
- The `config` file references files like:
  - `certificate-authority: /home/rajaramesh7410/.minikube/ca.crt`
  - `client-certificate: /home/rajaramesh7410/.minikube/profiles/minikube/client.crt`
  - `client-key: /home/rajaramesh7410/.minikube/profiles/minikube/client.key`
- Edit the file at 3 places:
  - `certificate-authority` -> `certificate-authority-data`
  - `client-certificate` -> `client-certificate-data`
  - `client-key` -> `client-key-data`
- Now we’ll **inline** the actual base64 content instead of using file paths.
- 🔁 For Each of These 3 Files, Run:
```
cat /home/rajaramesh7410/.minikube/ca.crt | base64 -w 0; echo
cat /home/rajaramesh7410/.minikube/profiles/minikube/client.crt | base64 -w 0; echo
cat /home/rajaramesh7410/.minikube/profiles/minikube/client.key | base64 -w 0; echo
```
- Copy each base64 string from above each commands & replace instead of paths at corresponding :
  - `certificate-authority-data` 
  - `client-certificate-data` 
  - `client-key-data` 
  fields in your config file.

---

#### 📝 Step III: Saving Edited Kubeconfig File
- Save this new file as `kubeconfig` (no `.txt` extension) in your **Downloads** folder.
- Now open Git Bash and run:
```bash
cd ~/Downloads
vi kubeconfig
```
- Paste the full edited config content.
- Save by pressing `esc` key, then type `:wq!` and hit Enter.

---

#### 🔒 Step IV: Add `kubeconfig` as Secret File in Jenkins
- Go to **Jenkins Dashboard** -> **Manage Jenkins** (⚙️) -> **Credentials**
- Select: **Global** -> ➕ **Add Credentials**
- Choose: **Kind: Secret file**
- Upload your edited `kubeconfig` file
- Set:
  - **ID**: `kubeconfig`
  - **Description**: `kubeconfig`
- Click **Create**
  
✅ At this point, your Jenkins instance is fully connected to your Kubernetes cluster using a secure kubeconfig setup.

---

#### 📄 Step IV: Update `Jenkinsfile` in VSCode
- Un-comment the below stages in VSCode Jenkinsfile.
  ```
  Install Kubectl & ArgoCD CLI Setup
  Apply Kubernetes & Sync App with ArgoCD
  ```
- In SSH, run the below command to get kubernetes cluster serverUrl: `https://192.168.49.2:8443`
   ```
   kubectl cluster-info
   ```
- Now update that serverUrl in Jenkinsfile at stage: `Apply Kubernetes & Sync App with ArgoCD`
- Copy & paste your ArgoCD server IP & port at same stage in Jenkinsfile at **argocd login** `<VM_EXTERNAL_IP>:31704`
- Save the Jenkinsfile in VSCode and push to github by following below steps:
  ```
  git add .
  git comit -m "commit: Install Kubectl & ArgoCD CLI. Apply Kubernetes & Sync App with ArgoCD."
  git push origin main
  ```
---

### 14. 🐙 ArgoCd - Part 3
#### ❗Important: Inject environment secret in Kubernetes Cluster
  ```
  kubectl create secret generic groq-api-secret \
    --from-literal=GROQ_API_KEY="<YOUR-GROQ-API-KEY>" \
    -n argocd
  ```
---

#### Jenkinsfile stage: `Install Kubectl & ArgoCD CLI Setup` can be removed if followed below steps: **Ignore** (*not required*)
 - **Install Kubectl & ArgoCD CLI**
  ```bash
  docker exec -it jenkins bash
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  chmod +x kubectl
  mv kubectl /usr/local/bin/kubectl
  curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
  chmod +x /usr/local/bin/argocd
  exit
  ```
- **Restart Jenkins Again** in SSH
  ```
  docker restart jenkins
  ```
👨🏻‍💻 You need to login the Jenkins web UI again.

---

#### 🤝 Connect GitHub Repository to ArgoCD
1. Open **ArgoCD UI** -> Go to **Settings** (⚙️) -> **Repositories** -> **Connect Repo** via HTTPS.
2. Fill in details:
   * **Type:** git
   * **Name:** anything you want (`study-buddy-ai`)
   * **Project:** default
   * **Repo URL:** `https://github.com/P-RajaRamesh/Study-Buddy-AI.git`
   * **Username & Password:** Provide GitHub username and token (optional but recommended)
3. Click **Connect**. <br/>

✅ You should see a success message confirming the GitHub repo is connected to ArgoCD.

---

#### 🆕 Create a New Application in ArgoCD
* Go to **Applications** -> Click **New App** (➕).
* Fill in the form:
  * **Name:** `study`
  * **Project:** default
  * **Sync Policy:** Automatic
  * ✔ **Tick**
    - ✅ Enable Auto-Sync
    - ✅ Prune Resources
    - ✅ Self Heal
  * Leave other settings as default.
  * **Repository URL:** select your connected repo.
  * **Revision:** `main` (branch)
  * **Path:** `manifests`
  * **Cluster URL:** select from dropdown.
  * **Namespace:** `argocd`
* Click **Create**. <br/>
  
✅ You should see the application status as **Synced** and **Healthy**.

---

#### 📝 Modify the Image Tag & Build new image
- Change the IMAGE_TAG to `v2` in Jenkinsfile
- Update **image:** of `deployment.yaml` file with `rajaramesh7410/studybuddy:v2`
- Save the Jenkinsfile in VSCode and push to github by following below steps:
  ```
  git add .
  git comit -m "commit: v2 image tag"
  git push origin main
  ```
- Go back to Jenkins Dashboard
- Click on your `Study-Buddy-AI` pipeline
- Click **Build Now**  (you can **ignore** if dont want to biuld the image again.)   <br/>

✅ If successful, your image will be available on DockerHub Repositories with `v2` image tag. <br/>
🐙 Argocd will deploy the `manifests` files in Kubernetes cluster (even not clicked on **Build Now**).

---

#### Verify ArgoCD Application and Logs
* Open **ArgoCD UI**.
* Click the `study` application workflow.
* View logs for each pod to verify deployment.

---

#### 📖 Access Your Application
* On your VM instance terminal, run:
  ```
  kubectl get deploy -n argocd
  ```
* You should see your (`llmops-app`) deployment.
* Check pods running:
  ```
  kubectl get pods -n argocd
  ```
---

#### 🔌 Allow External Access
* Run the following command in SSH to create a tunnel:
  ```
  minikube tunnel
  ```
* Open another SSH terminal and run port-forwarding:
  ```
  kubectl port-forward svc/llmops-service -n argocd --address 0.0.0.0 9090:80
  ```
---

#### 📖 Access the Application from Browser
* Copy your VM’s external IP address.
* Open browser and go to:
  ```
  http://<VM_EXTERNAL_IP>:9090
  ```
✅ You should see your (`Study Buddy AI`) application running successfully!

---

#### 🪝 Add Webhook in GitHub Repository
1. Go to your **GitHub repo** -> **Settings** -> **Webhooks** -> **Add webhook**.
2. Fill in the details:
   - **Payload URL:** 
     `http://<YOUR-VM-EXTERNAL-IP>:8080/github-webhook/`  
     *(Replace with your Jenkins URL)*
   - **Content type:** `application/json`
   - **Secret:** *(Not necessary, leave blank)*
   - ◉ **Enable SSL verification** Enable if using HTTPS
3. Under **Which events would you like to trigger this webhook?**  
   - Select ◉ **Just the push event**  
     (This means the pipeline triggers on every push)
4. Click **Add webhook**.

---

#### ⚙️ Configure Jenkins web UI to Receive Webhook
1. Open **Jenkins** web UI -> Go to your **Pipeline** job -> Click **Configure**.
2. Click ⏲ **Triggers**.
3. Tick ✅ **GitHub hook trigger for GITScm polling**.
4. Click **Apply** and **Save**. <br/>

✅ Your webhook trigger is now configured.

---

#### 🧪 Test the Webhook Trigger
1. Open **VS Code**.
2. Make a slight change in you application streamlit UI.
3. Change the IMAGE_TAG to `v3` in Jenkinsfile
4. Update **image:** field of `deployment.yaml` file with `rajaramesh7410/studybuddy:v3`
5. Commit and **push** the code to GitHub.
6. Go to Jenkins Dashboard.
7. You should see your Jenkins pipeline **automatically triggered** and start running. <br/>

✅ If successful, your image will be available on DockerHub Repositories with `v3` image tag. <br/>
🐙 Argocd will re-deploy the `manifests` files in Kubernetes cluster.

---

### 15. 🎯 Final Outcome
- Now copy VM External IP in Google Cloud & append :9090 and checkout your Application... `http://<VM_EXTERNAL_IP>:9090`
- Whenever you push anything to github repo of your application Jenkins will automatically trigger **Build Now**
- New image will be created in Dockerhub repo and changes will be reflected in UI once argocd re-deploys `manifests` files.
- You can also rollback to previous image by changing the image tag which you have already created.

✅ This completes the full pipeline successfully and automatically!

---
