**Day 2 Tasks Learnings**

ACM (Red Hat Advanced Cluster Management for Kubernetes)
Check Cluster Health / Create Cluster
Option 1:
-	Login to ACM Web Console
-	Infrastructure > Clusters (To see all managed clusters)
-	U can create clusters here
-	Check for individual status indicators (Ready, Unknown, Failed or Creating)
Option 2:
-	Verify MultiClusterHub status 
oc get multiclusterhub -n open-cluster-management
-	Verify MultiClusterEngine status
oc get multiclusterengine -n open-cluster-management
-	Check ACM Pod health:
oc get pods -n open-cluster-management
Edit/Apply Policies in ACM
Policies = Governance rules (Security, Config, Compliance)
-	Governance > Policies
-	Create Policy or select and edit your policy
-	Fill in name, namespace, policy type
-	Define the policy YAML
-	Select Placement (Choose which clusters to apply to)

RHEL
Check Password Expiry: chage -l $USER
Check NTP: timedatectl status
Check DNS: ?
Check Networking: ?
Check RHEL repo config: ?
OS Patching: sudo dnf update -y (Snapshots / Healthchecks before and after patching)
Change Password: passwd $USER
Add/Remove User: useradd / userdel $USER 
Extend Disk (disk partitioning): ?
Add Disk: ?

ODF (Red Hat OpenShift Data Foundation) 

ACS (Red Hat Advanced Cluster Security)

OCP (OpenShift Container Platform)
Cluster Health Check (Cluster Operators)
-	OpenShift Console
-	Operators > Cluster Operators
-	Check columns (Available, Progressing, Degraded)
-	Healthy = Available
-	Oc get co
 
Check CSR (Certificate Signing Requests)
Purpose: CSRs generated when new nodes join cluster, certificates need approval
CLI: oc get csr, oca dm certificate approve <csr-name>
UI: Compute > Nodes (Local-cluster)


Check MachineConfigPool (MCP) Health
MCP: A group of nodes that share the same config are updated together
Machine Config: Defines the configuration
UI: Compute > MachineConfigPools (Update status, Degraded = False)
CLI: oc get mcp, oc describe mcp worker (more detailed)
 
Force Drain Node
Empty the node safely before maintenance
-	Node maintenance
-	Node stuck
-	Before reboot
Oc adm drain <node-name> --ignore-daemonsets –delete-emptydir-data –force
Mark Unschedulable: oca adm cordon <node-name>
Reboot a Node
-	SSH into Node > sudo reboot
-	Oc delete node <node-name> (Because the MachineSet recreates node automatically)
Red Hat OpenShift Administration I
Redhat OpenShift Container Platform
-	Complete platform to run applications in clusters of servers
-	Based on existing technologies (containers and k8s)
-	Containers: Package applications and dependencies to be executed on any system with a container runtime
-	Kubernetes: Provides many features to build clusters of servers that run containerized applications
Containers
-	Namespaces: Isolate processes and resources
-	cgroups: Manage resource usage (CPU, memory)
-	Defined by Open Container Initiative
-	Ensures portability across platforms
Kubernetes
-	Platform for deploying, managing and scaling containerized applications
-	Runs applications across a cluster of nodes (servers)
-	Automatically handles failures
-	Uses declarative model
Namespaces
-	Logical separation of resources
-	Help organize large environments
-	Enable access control (permissions)
Accessing the OpenShift Web Console
-	oc login -u <USERNAME> -p <PASSWORD> <API_ENDPOINT>:<PORT>
-	oc whoami --show-console
Kubectl CLI
cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.31/rpm/repodata/repomd.xml.key
EOF
Install: sudo yum install -y kubectl
OpenShift CLI
Download from the openshift web console: Help > Command Line tools
Oc command
Username and Password Authentication
Oc login -u <USERNAME> <API-URL>
Oc login --token=<TOKEN> --server=<CLUSTER-URL>
(Go to https://oauth-openshift.apps.cluster-url/oauth/token/request and display token)
Oc login --web <API-URL>


Manage Resources at the CLI
Create proj: oc new-project <PROJ_NAME>
Cluster Info: oc cluster-info
Api-versions: oc api-versions
Get cluster operator: oc get clusteroperator
Get commands: oc get <RESOURCE_TYPE> <RESOURCE_NAME> -o (yaml/json)
Oc get all: oc get all (Summary of most impt resources in a project)
Describe: oc describe (Drill down from get summary)
Explain: oc explain
Create: oc create
Status: oc status
Delete: oc delete
Assess health of OpenShift Cluster
Operators: Automate cluster management tasks, Handle health checks, updates and maintain desired state
2 Types: Cluster Operators (CVO-managed)/OLM Operators (Optional add ons)
Check Resource Usage
Cluster Wide: oc adm top pods -A --sum
Namespace-specific: oc adm top pods -n <NAMESPACE>
Per-Container: oc adm top pods <POD> -n <ns> --containers
Events (High-level logs)
View events: oc get events -A (All namespaces)
Sort by time: oc get events --sort-by .metadata.creationTimestamp
Alerts (Prometheus + Alertmanager)
View monitoring components: oc get all -n openshift-monitoring
Get alerts: 
oc exec -n openshift-monitoring Prometheus-k8s-0 -- curl …
oc exec -n openshift-monitoring alertmanager-main-0 -- curl …
Check Node Health
Cluster-info: oc cluster-info
List nodes: oc get nodes
LAB:
Cluster Version: oc version
Supported API versions: oc api-versions
Identify fields for <OBJECT>:  oc explain <OBJECT> (Contains FIELD)
Retrieve conditions status of the etcd-master01 pod in the openshift-etcd namespace by using  jq: oc get pods etcd-master01 -n openshift-etcd -o json | jq .status.conditions

Create Pods
Oc run <NAME> --image <IMAGE>
Options:
-	--command -- <CMD>  run custom command
-	-it  interactive shell
-	--restart  Always / OnFailure / Never
-	--rm  delete pod after exit
-	--env  set env variables
Run Interactive Containers
Oc run -it <NAME> --image <IMAGE> --command --/bin/bash
Pod Restart Policies
-	Always  restart always (default)
-	OnFailure  restart on error
-	Never  Do not restart
OpenShift Security (UID/GID)
-	OpenShift assigns random non-root UID
-	Based on proj: oc describe project <PROJECT>
-	Ensure containers non root, each proj have own UID range
Execute Commands in Pods
oc exec <POD> -- <COMMAND>
oc exec <POD> -c <CONTAINER> -- <COMMAND>
oc exec <POD> -it – bash
Attach to Running Container
Oc attach <pod> -it
User: Developer
Pod: ubi9-user (UBI9 base container image)
Image: registry.ocp4.example.com:8443/ubi9/ubi
Restart policy: Never
Execute: whoami and id
Oc run -it <POD> --restart  <POLICY> --image <IMAGE> -- /bin/bash -c “whoami && id”
 
<REGISTRY>/<REPOSITORY>:<TAG>@<DIGEST>
Base Images (UBI)
-	Red Hat provides Universal Base Images
-	Secure, supported, RHEL-based
-	Standard: Full features
-	Init: multi-process
-	Minimal: smaller (microdnf)
-	Micro: smallest (no package manager)
Inspect Images (Tools)
-	Skopeo (Inspect, copy, delete images)
-	Skopeo inspect docker://image
-	Skopeo list-tags docker://image
-	Skopea copy docker://src docker://dest

Oc image info <image>
-	Metadata, Layers, Environment Variables
Skopeo login <registry> # Authentication
oc debug pod/<POD>

Key Concepts:
Containers are:
-	Immutable (Should not be changed permanently)
-	Ephemeral (Temporary)
-	Only modify running containers for troubleshooting
-	Proper fix = update image  Redeploy

Big Picture: K8s Networking
-	K8s creates virtual network across all nodes (machines) in a cluster
-	Every pod can talk to every other pod by default
OVN-K8s
-	Default networking system in openshift 4.x
-	Creates single virtual network across all nodes
-	Manages routing, IP assignment and traffic automatically
Pod networking basics
-	Each pod gets unique IP, shared by all containers inside pod
-	Containers in each pod use local host to communicate
-	Container <> Container (Inside pod communication)
-	Pod <> Pod
-	Pod <> Service
-	External <> Service
Useful Commands
-	Oc expose deployment/myapp (Create service from deployment)
-	Oc get svc -o wide (view services)
-	Oc get endpoints (view endpoints, actual pod IPs)
-	Oc describe deployment myapp (view deployment labels)
-	Oc expose service <NAME> (Creates route / external access)
Big Picture: Pods, Services and Ips
-	Connect pods  Services  External users
-	Scale Apps
-	Expose apps outside the cluster (Route & Ingress)
Pods, Services, Ips
-	Pods temporary  Ips change
-	Services provide a stable IP + name
-	Service distributes traffic across pods
-	Comms inside cluster uses OVN-K8s Network
Services exists Because:
-	Pods ephemeral
-	Services keeps a fixed entry point
Services Types
-	Cluster IP (Default): Internal only, pod <> pod communication
-	LoadBalancer: Creates external IP, Uses cloud provider LB, can be expensive
-	NodePort: Opens port on every node, External traffic hits node  Forward to pods
-	ExternalIP (Legacy): External IP  Node  Pods
-	ExternalName: Maps to external DNS name
Exposing Apps Externally
-	Route (OpenShift specific and preferred): Assigns a public hostname, managed by openshift ingress controller (HA prox), supports TLS, Advanced routing and Blue-green deployments
-	Ingress: Similar to Route, supports HTTP(S) + TLS, less feature-rich in openshift
Oc scale --replicas=5 deployment/app
Big Picture (Ephemeral vs Persistent Storage)
Ephemeral: Data lives only while pod is alive, Deleted when pod is deleted
Persistent Storage: Data survives pod restarts, Can be reused across pods
Core Components
Persistent Volume: Actual storage resource in cluster, Created by admin (Hard drive in data center)
Persistent Volume Claim: Request for storage by developer (Request form for 10GB storage)
Storage Class: Types of storage (Fast/Slow/NFS/SSD)
FLOW: Dev create PVC  K8s finds or create PV  PVC is bound to PV  Pod Mounts PVC
Static vs Dynamic Provisioning
-	Admin pre-creates PVs (Rooms already built u pick 1)
-	PV created on demand via Storage Class (Hotel builds a room when u request it)
Volume Types (Important Ones)
-	configMap: Config files (Setting file)
-	emptyDir: Temp storage (Scratch file)
-	hostPath: Node disk (Borrow someone’s laptop)
-	NFS: Shared Storage (Google drive)
-	iSCSI: Block storage (External hard disk)
Access Modes
-	RWO: One node many writes (One computer uses USB)
-	RWX: Many can write (Shared google docs)
-	ROX: Many read only (view-only file)
-	RWOP: One pod only (Personal Notebook)
Volume Modes
-	Filesystem (default): Normal files
-	Block: Raw disk (faster, advanced use)
Using PVC in Deployment
Oc set volumes deployment/app --add --type pvc --claim-name mypvc --mount-path /data
-	PVC created
-	Volume attached to pod
-	Mounted at /data
Lifecycle
-	Available: PV ready
-	Bound: Connected to PVC
-	In Use: Actively used
-	Released: PVC deleted
-	Reclaimed: Storage handled
Reclaim Policy:
-	Retain: Keep Data
-	Delete: Delete Everything




IMPORTANT COMMANDS:
IMAGE STREAM: os create is [NAME]
TAG: os tag [IMAGE] [NAME]:[ver]
UPDATE IMAGE-TABLE: oc set image-lookup mysql8
SECRETS: oc create secret generic [NAME] --from-literal=() --from-literal=()
PVC: oc set volume deployment/mysql8 --add  --name=mysql-storage  --type=pvc --claim-name=mysql-pvc --claim-size=2Gi --claim-class=lvms-vg1 (storage class)  --mount-path=/var/lib/mysql (working directory)
WATCH IMAGE STREAM: oc set triggers deployment/mysql8 --from-image=mysql8:1 --containers=mysql8
SET NEW IMAGE: oc set image mysql80=[]
SET PROBE: oc set probe deployment/[NAME] --readiness/liveness [--/--get-url]
SET RESOURCES: oc set resources deployment/quotesdb --requests cpu=200m,memory=256Mi --limits cpu=500m,memory=1Gi
HELM
helm install [APP_NAME] [REPO]/etherpad -f values.yaml --version 0.0.6
REPO: helm search repo –versions
Hint to create yaml files: helm show values do280-repo/etherpad --version 0.0.6
Create htpasswd, use -B option to force bcrypt:
htpasswd -c -B -b ~/DO280/labs/auth-providers/htpasswd new_admin redhat
-c = create (Only for first time)
-B = use Bcrypt
-b = use batch mode so u can type uname and pword

Create an auth-review secret by using the ~/DO280/labs/auth-review/tmp_users file.
[student@workstation ~]$ oc create secret generic auth-review \
    --from-file htpasswd=/home/student/DO280/labs/auth-review/tmp_users \
    -n openshift-config
Export the existing OAuth resource to ~/DO280/labs/auth-review/oauth.yaml.

[student@workstation ~]$ oc get oauth cluster \
   -o yaml > ~/DO280/labs/auth-review/oauth.yaml
 
Goal: Modify tmp_users file to DELETE analyst UPDATE tester, leader ADD new_admin, new_developer using password L@bR3v!ew
-	htpasswd -D ~/DO280/labs/auth-review/tmp_users analyst
-	htpasswd -B -d ~/DO280/labs/auth-review/tmp_users [NAME] [PASSWORD] (Can be looped)
Goal: Log in as cluster admin, upload the tmp_users file into secret named auth-review and apply it to the cluster’s OAuth settings
-	oc create secret generic [NAME] –from-file=htpasswd=[tmp_users file] -n openshift-config
-	oc edit oauth cluster
-	- htpasswd:
-	    fileData:
-	      name: auth-review
-	  mappingMethod: claim
-	  name: htpasswd
-	  type: HTPasswd



Goal: Make new_admin a full cluster administrator and verify both new_admin and new_developer can login
-	Oc adm policy add-cluster-role-to-user [cluster_admin] new_admin
Once u login as new_admin u can then remove permissions, etc prevent normal users from creating projects on their own
-	Login to new_admin
-	Oc adm policy remove-cluster-role-from-group self-provisioner system:authenticated:oauth
Goal: Create managers group, add leader, give project creation rights, have leader create auth-review project
-	Oc adm groups new managers
-	Oc adm groups add-users managers leader
-	Oc adm policy add-cluster-role-to-group self-provisioner managers
-	Create new project as intended

Generate Private Key
-	Openssl genrsa -out training.key 4096
Generate Certificate signing request
-	Openssl req -new -key training.key -out training.csr -subj "/C=US/ST=North Carolina/L=Raleigh/O=Red Hat/CN=todo-https.apps.ocp4.example.com"
Signing cert with Lab CA
-	openssl x509 -req -in training.csr -passin file:passphrase.txt -CA training-CA.pem -CAkey training-CA.key -CAcreateserial -out training.crt ...
Mount cert via openshift secret
-	oc create secret tls todo-certs --cert certs/training.crt --key certs/training.key

Server Pod – Client Pod communication
Automate TLS (oc annotate)
-	oc annotate service server service.beta.openshift.io/serving-cert-secret-name=server-secret
Explain: Signals openshift internal CA to generate a valid TLS certificate and Key/Pair and save into secret called server-secret
Patching server deployment
Applied YAML patch taking certs out and map them as files inside Nginx container at /etc/pki/nginx
Injecting the Trust Bundle into Client
-	oc annotate configmap ca-bundle service.beta.openshift.io/inject-cabundle=true
Explain: When no-ca-bundle tried to reach via curl, fails because client did not trust
End-to-End
Mounted ConfigMap into client deployment at /etc/pki/ca-trust/extracted/pem. Client OS now possessed cert required to validate the signatures 
LoadBalancer
-	oc expose deployment [NAME] --name=[NAME]-loadbalancer 
--type=LoadBalancer
Example (Cluster role binding):
-	Allow only provisioners group to create project
-	oc edit clusterrolebinding self-provisioners
-	subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: provisioners
Create Project Template Definitions
-	oc adm create-bootstrap-project-template -o yaml > ~/DO280/labs/selfservice-review/template.yaml
-	oc create -f ~/DO280/labs/selfservice-review/template.yaml -n openshift-config
-	oc edit projects.config.openshift.io cluster
-	Edit under spec projectRequestTemplate.name.project-request
Check Quotas and Limit Ranges
-	Oc describe quota,limitrange




Cluster Operators: Built in system operators that build and run OpenShift platform
-	Manage baseline features [web console, network routing, auth, login]
-	Cluster Version Operator controls them
-	Only updates when u upgrade the entire cluster version
Add-on Operators:  Supplemental tools that cluster admins can add on
-	Bring new capabilities to cluster like, automated db as a service, monitoring tools or pipelines
-	Operator Lifecycle Manager controls them
-	Updates independently whenever admin needs to
Get operators list (Package manifest):
Oc get packagemanifest
Oc get csv [NAME] -o jsonpath={.spec.install.spec.deployments} | jq
Assign permissions (Service Accounts)
Oc get deploy gitlab -o yaml | oc adm policy scc-subject-review -f –
Oc create sa gitlab-sa
Apply anyuid to gitlab-sa:
Oc adm policy add-scc-to-user anyuid -z gitlab-sa
Assign:
oc set serviceaccount deployment/gitlab gitlab-sa

Add permissions:
-	Oc adm policy add-scc-to-user priviledged -z {SERVICE_ACCOUNT}
-	Oc adm policy add-cluster-role-to-user cluster-admin -z {SERVICE_ACCOUNT}
View permissions:
-	Oc adm policy scc-subject-review -f {yaml file}
Set permissions:
-	oc set sa deployment payroll-api payroll-sa



OpenShift Updates
-	Over-the-Air (OTA) Updates: OpenShift uses a smartphone style updates system, they communicate with Red Hat servers to automatically find security patches, bug fixes and new features
-	Skip-Level Upgrades: System figures out safest path for cluster. Allows minor patches skip (4.18.1  4.18.3)
-	Telemetry Data: Sends anonymized health and status reports back to Red Hat via component called Telemeter (Notifies if ur cluster is safe to upgrade or a known bug may block you)
-	No Rollbacks: One way street, cannot rollback to older version hence need to contact Red Hat support
Update Channel
When you tell OpenShift to look for updates, you must place it into a specific "Channel" based on how stable you need your environment to be:
-	Candidate Channel (candidate-4.x): The bleeding edge. It has the absolute newest code for testing, but Red Hat does not officially support it. Do not use this in production.
-	Fast Channel (fast-4.x): Released as soon as Red Hat declares a version generally available. Fully supported, stable, and ideal for Development and QA environments.
-	Stable Channel (stable-4.x): The gold standard for Production. Red Hat watches clusters on the Fast channel first. If no bugs are found in the wild after a short delay, that version is promoted to the Stable channel.
-	Extended Update Support Channel (eus-4.x): Available only on even-numbered releases (like 4.16, 4.18). It allows enterprise production environments to stay on a single major release longer with extended support.
How to Perform Updates
You must pause the clusters auto self-healing monitors so it does not accidentally reboot nodes in the middle of upgrading
-	Oc annotate machinehealthcheck -n openshift-machine-api machine-api-termination-handler cluster.x-k8s.io/paused[="" OR -] (Pause/Unpause)
 
IAM roles:
Create a Group: oc adm groups new <GROUP_NAME>
Add Users to Group: oc adm groups add-users <GROUP_NAME> <USERNAME>
Scope Permissions Cluster-Wide: oc adm policy add-cluster-role-to-group <ROLE> <GROUP>
Scope Permissions to Specific Namespace: oc adm policy add-cluster-role-to-group <ROLE> <GROUP> -n <NAMESPACE>
Manage Project Self-Provisioning:
Openshift allows any authenticated user to create new projects by default. 
Restrict Project Creation: oc annotate clusterrolebinding/self-provisioners --overwrite rbac.authorization.kubernetes.io/autoupdate="false"
Modify the Binding: oc edit clusterrolebinding self-provisioners
How to create Project Templates:
Get the template: oc adm create-bootstrap-project-template -o yaml > {FILE}
Get ur limit range: oc get limitrange max-memory -n template-test -o yaml
Edit global cluster proj config: oc edit projects.config.openshift.io cluster
 
Apply to openshift-config namespace: oc apply -f template.yaml -n openshift-config


Example of how to set constraints:
Project Constraints protect the cluster from the entire namespace misbehaving or over-allocating resources. (Big picture)
Default Constraints protect the namespace from a single unconfigured workload eating up all the project's allocated room.
Namespace constraints:                                                                                                                                       
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-resources
  namespace: my-namespace
spec:
  hard:
    requests.cpu: "3.5"
    requests.memory: 3Gi
    limits.cpu: "4"
    limits.memory: 4Gi
Default Resource Specification:
apiVersion: v1
kind: LimitRange
metadata:
  name: support-limits
  namespace: workshop-support
spec:
  limits:
  - type: Container
    default:
      cpu: 300m
      memory: 400Mi
    defaultRequest:
      cpu: 100m
      memory: 250Mi

Step 1: Prepare the Namespace, Groups, and Quotas
First, log in as admin to handle the cluster-level namespace and role setup.
# Log in as admin
oc login -u admin -p redhatocp https://api.ocp4.example.com:6443

# 1. Create the namespace (not a project!)
oc create namespace workshop-support

# 2. Add the mandatory label
oc label namespace workshop-support category=support

# 3. Grant the admin role to the workshop-support group
oc adm policy add-cluster-role-to-group admin workshop-support

# 4. Create the Resource Quota (named exactly workshop-support)
oc create quota workshop-support \
  --hard=requests.cpu=3500m,requests.memory=3Gi,limits.cpu=4,limits.memory=4Gi \
  -n workshop-support

Next, create the LimitRange inside the namespace. Create a file named limitrange.yaml:




YAML
apiVersion: v1
kind: LimitRange
metadata:
  name: workshop-support
  namespace: workshop-support
spec:
  limits:
  - default:
      cpu: 300m
      memory: 400Mi
    defaultRequest:
      cpu: 100m
      memory: 250Mi
    type: Container

Apply it:
oc apply -f limitrange.yaml

Step 2: Deploy the Project Cleaner Application
# 1. Create the Service Account
oc create sa project-cleaner-sa -n workshop-support

# 2. Create the ClusterRole allowing get, list, and delete on namespaces
oc create clusterrole project-cleaner \
  --verb=get,list,delete \
  --resource=namespaces

# 3. Bind the ClusterRole to your Service Account
oc adm policy add-cluster-role-to-user project-cleaner \
  system:serviceaccount:workshop-support:project-cleaner-sa
Now, edit the CronJob file at ~/DO0019L/labs/compreview-apps/project-cleaner/cron-job.yaml. Ensure the schedule is set to run every minute, and map the correct serviceAccountName:
YAML
apiVersion: batch/v1
kind: CronJob
metadata:
  name: project-cleaner
  namespace: workshop-support
spec:
  schedule: "*/1 * * * *"  # Run every minute
  jobTemplate:
    spec:
      template:
        spec:
          serviceAccountName: project-cleaner-sa  # Add the service account
          containers:
          ...
Apply the CronJob:
oc apply -f ~/DO0019L/labs/compreview-apps/project-cleaner/cron-job.yaml






Step 3: Set up TLS and Deploy the Beeper DB/API
First, deploy the database layer:
oc apply -f ~/DO0019L/labs/compreview-apps/beeper-api/beeper-db.yaml -n workshop-support

Create the TLS secret using the pre-generated keys in your lab environment:
oc create secret tls beeper-api-tls \
  --cert=/home/student/DO0019L/labs/compreview-apps/beeper-api/certs/beeper-api.pem \
  --key=/home/student/DO0019L/labs/compreview-apps/beeper-api/certs/beeper-api.key \
  -n workshop-support
Open ~/DO0019L/labs/compreview-apps/beeper-api/deployment.yaml. You need to edit the container spec to mount the secret, set the environment variables, and change the probes to HTTPS. Your manifest updates should look like this:
spec:
  containers:
  - name: beeper-api
    image: ...
    env:
    - name: CERT_PATH
      value: "/etc/pki/beeper-api/tls.crt"
    - name: CERT_KEY_PATH
      value: "/etc/pki/beeper-api/tls.key"
    - name: TLS_ENABLED
      value: "true"
    ports:
    - containerPort: 8080
    startupProbe:
      httpGet:
        path: /api/beeps
        port: 8080
        scheme: HTTPS  # Change from HTTP to HTTPS
    readinessProbe:
      httpGet:
        path: /api/beeps
        port: 8080
        scheme: HTTPS  # Change from HTTP to HTTPS
    livenessProbe:
      httpGet:
        path: /api/beeps
        port: 8080
        scheme: HTTPS  # Change from HTTP to HTTPS
    volumeMounts:
    - name: tls-certs
      mountPath: "/etc/pki/beeper-api"
      readOnly: true
  volumes:
  - name: tls-certs
    secret:
      secretName: beeper-api-tls
Apply the deployment manifest:
oc apply -f ~/DO0019L/labs/compreview-apps/beeper-api/deployment.yaml -n workshop-support



Step 4: Networking and Edge Routing
Expose the API internally via a ClusterIP service on port 443 targeting container port 8080:
oc expose deployment beeper-api \
  --name=beeper-api \
  --port=443 \
  --target-port=8080 \
  -n workshop-support

Expose the service to the outside world with a secure passthrough route:
oc create route passthrough beeper-api-route \
  --service=beeper-api \
  --hostname=beeper-api.apps.ocp4.example.com \
  -n workshop-support
Step 5: Enforce Network Policies
Apply the provided configuration files to secure the pod traffic:
# Apply DB ingress restrictions
oc apply -f ~/DO0019L/labs/compreview-apps/beeper-api/networkpolicy-beeper-db.yaml -n workshop-support

# Apply API ingress restrictions (allowing traffic only via routers)
oc apply -f ~/DO0019L/labs/compreview-apps/beeper-api/networkpolicy-beeper-api.yaml -n workshop-support

HELM
Helm repo add classroom <LINK>
Helm repo list
Helm search repo classroom

helm install etherpad-dev classroom/etherpad \
  --version 0.0.7 \
  --set replicaCount=1 \
  --set image.registry=registry.ocp4.example.com:8443 \
  --set image.name=etherpad \
  --set image.tag=1.8.18 \
  --set route.hostname=etherpad-dev.apps.ocp4.example.com

RESET ROOT PASSWORD:
-	reboot: Restarts the machine to interrupt the boot process
-	e: Interrupt GRUB menu (modify kernal boot parameters)
-	Find linux line and add rd.break at the end
-	example: linux ... ro crashkernel=auto rd.break
-	Tells system to interrupt boot before mounting the real root filesystem, drops into minimal emergency shell
-	Ctrl + X or F10: Boots system using temp modified parameters
-	Will see (switch_root:/#)
-	mount -o remount,rw /sysroot 
-	By default system mounts root (/sysroot) as read only, password change needs write access (rw = read-write)
-	chroot /sysroot
-	currently in temp environment, chroot switches root to actual system
-	passwd (Change password)
-	touch /.autorelabel
-	exit, exit, reboot

RHSA Tips:
-	wc -l results.txt | tee -a findings.txt (Count num of lines in results.txt, append to findings.txt and print out input)
Types of users:
Super user: Uname = root, UID = 0 (Full system access)
System user: processes that provide supporting services
Regular user: limited access to system usually
Types of groups:
Primary Group: all user has 1 primary group, this group owns files that the user creates
Supplementary groups: etc more than 1 group
View current logged in user: id
View another user: id [USER]
View who owns file: ls -l [FILE]
View who owns folder: ls -ld [FOLDER]
View process info w who owns: ps awwu
View info about user: cat /etc/passwd [USER:x:UID:GID:comment:HomeDIR:Default shell at login] #/sbin/nologin shell to disallow interactive logins
View groups: cat /etc/group
Switch User: su – user # if without it switches to root by default
Create Group: groupadd -g 10000 [GROUP] (-g = GID, -n = name, -r = system groups)
Check group status: tail /etc/group (/etc/passwd to check user)
Change group memberships: usermod -g [NEW_GROUP] [OLD_GROUP]
Add list of users to supplementary group: groupmod -aU [USER] [GROUP]
Add user to 1 or more existing groups w/o removing them from current pri/sec groups: usermod -aG [GROUP] [USER]
Temporarily change ur Primary group: newgrp [GROUP]
Allow usage of sudo to group: echo "%group ALL=(ALL) ALL" > /etc/sudoers.d/group
Lock and expire account (Using non admin): sudo usermod -L -e 1 operaror
Unlock account and remove expiration date: sudo usermod -U -e ‘’ operator1
Set maximum age of password to be x days: chage -M x operator1
List user details: chage -l operator1
Force password change on login: chage -d 0 operator1 (Force it to expire)
Change Account Expiry: chage -E [DATE] operator1
File to change: /etc/login.defs (Likely only for new users)
The umask setting grants all user and group permissions, and prevents others from having any permissions on new files or directories.
Special Permissions:
Setuid (User ID pass-through): when file is executed runs privileges of file owner u+s
Setgid (Group ID Collaboration): File runs with the privileges of file group, any new file/folder created inherits directory groups owner rather than creator’s default group g+s
Sticky Bit: Restricts deletion rights on a directory (only root or own the file then can delete) o+t
Umask: Subtractive filter
Persistently configure the user file creation mask (~/.bashrc file) in the shell for the dev1 and dev2 users so that group members can collaborate, but other users cannot access their data. (umask 007)
su - dbadmin1
[dbadmin1@serverb ~]$ echo "umask 007" >> .bashrc
[dbadmin1@serverb ~]$ source ~/.bashrc
RPM
RPM: rpm -q -p rhcsa-script-1.0.0-1.noarch.rpm
View package info: -i
List package info: -l
View Script: --scripts
Rpm2cpio and cpio:
# List files in pkg
rpm2cpio rhcsa-script-1.0.0-1.noarch.rpm | cpio -tv
----------------------------------------------------------------------------------
# Extract files to /home/student dir
rpm2cpio rhcsa-script-1.0.0-1.noarch.rpm | cpio -idv
----------------------------------------------------------------------------------
Install PACKAGE: sudo rpm -ivh [PACKAGE]
View PACKAGE installed correctly: rpm -q [PACKAGE]
DNF (Update, for example yum)
dnf list [NAME-PATTERN]: List installed and available packages by name.
dnf group list: List installed and available groups.
dnf search KEYWORD:	Search for a package by keyword.
dnf info PACKAGENAME: Show details of a package.
dnf install PACKAGENAME: Install a package.
dnf group install GROUPNAME: Install a package group.
dnf upgrade: Upgrade all packages.
dnf remove PACKAGENAME: Remove a package.
dnf history: Display transaction history.
dnf config-manager --add-repo [REPO]: Add custom package repository
REPO: /etc/yum.repos.d/{REPO}
dnf repolist all: list all repos available
dnf config-manager --disable rht, errata: Disable rht and errata
rpm -qa | grep flatpack: rpm command to list all installed pkg name that match flatpack package name
flatpack remote-modify --disable rhel: Disable rhel
curl [LINK]: View flatpakrepo file
flatpak remote-add --no-gpg-verify --user [REMOTE_REPO_NAME] [LINK]: Add custom repo in user installation mode
flatpak remotes: List currently enabled repositories
flatpack remote-ls myrepo: List available applications and runtimes in ‘myrepo’ remote repository
flatpack list: verify applications successfully installed
FILE SYSTEMS and BLOCK DEVICES
Default local file system: XFS for RHEL
Alternatives: ext4 also supported for managing local files
Removable Media Support: RHEL 10 onwards exFAT file system is natively supported
Mount: Attaches a storage device’s file system to empty directory in Linux directory tree, making files accessible
Mount Point: Directory where the file system is attached. Once mounted ls views contents
Windows vs Linux: Linux integrates all devices into a single tree structure
Block Device: /dev directory provides low-level system access to storage hardware (must be partitioned and formatted before mounting)
Storage Device Naming Patterns:  SATA/SAS/USB/virtio-scsi: /dev/sda,/dev/sdb,/dev/sdc
Virtio-blk (KVM/VM Virtual Disks): /dev/vda,/dev/vdb/
NVMe SSDs:  /dev/nvme0,/dev/nvme1
SD/MMC/eMMC Cards: /dev/mmcblk0,/dev/mmcblk1
Display file system with mount points: df
Display disk usage report for /home: du -h /home
Identify Block device: lsblk -fp (Full Path)
List all open files and processes that are accessing the file system: lsof /mnt/data
Mount UUID=”” [Location to mount]
Umount from the ~ directory
(updatedb first) Locate files:  locate [file]
Case-incensitive: -i
Limit search results: -n [NUM]
Find files in real time: find [DIRECTORY] -name [FILENAME]
Case-insensitive: -iname
Find based on file name: find / -iname review5-path
Find based on user/group: -user/-group
Find based on UID/GID: -uid/-gid
Find based on permissions: -perm 764 -ls or -perm u=rwx g=rw o=r
Find based on Size: -size +-[SIZE]
Find based on Modification Time: -mmin +-[TIME_IN_MINUTES]
Find based on Regular files: -type f -links +1 (More than 1 hard link)
Find based on Directories: -type d
Find based on Soft Link: -type l
Find based on Block Devices: -type b
PROCESSES 
top, ps aux (Find stat column)
ps aux: Displays all processes
ps lax: Provides more details
ps j: Find processes and session information as well as jobs
&: Start command in background for example (sleep 10000 &)
fg %[JOBNUMBER]: Bring a background job to the foreground/ starting a suspended process
SIGNAL
SIGHUB: 1              SIGSTOP: 19
SIGINT: 2 (Ctrl + C)   SIGTSTP: 20 (Ctrl + Z)
SIGQUIT: 3 (Ctrl + \)  SIGCONT: 18
SIGKILL: 9
SIGTERM: 15
Kill [PID], kill -9 6564 or kill -SIGKILL 6564 or kill -SIGTERM %[JOB_NO]
Specify background jobs with a percent sign: kill %1
•	pgrep: Searches and lists PIDs based on criteria (e.g., pgrep -u bob lists all of Bob's PIDs).
•	pkill: Sends a signal to processes matching those criteria (e.g., pkill -u bob kills all of Bob's processes).
o	pkill -SIGCONT [PROCESS]
•	Can target specific terminals (pkill -t tty3) or parent processes (pkill -P 8391).
•	killall [name]: Signals multiple processes at once based on their exact command string name (e.g., killall job).
•	Pstree: Displays a visual tree diagram of parent and child process relationships.
Tracking and Logging Out Users (Administrative Tasks)
•	w vs who: 
o	* who gives a basic list of logged-in users.
o	w gives a detailed breakdown, including what terminal they are on (TTY), how long they've been idle, and how much CPU they are using (JCPU/PCPU).
•	Terminal Types: 
o	* ttyN means a physical system console.
o	pts/N means a virtual/remote login session (like SSH or a GUI terminal window).
•	Logging out a user: Run pkill -t [TTY] as root (or via sudo) to target and close out an unresponsive or unauthorized user's entire terminal session.
MONITORING PROCESS ACTIVITY
Interpret Load Average Values: uptime (View current load average)
Determine num of CPU on a system: lscpu
Display dynamic view of system processes: top (T and M single keystrokes to toggle the CPU and memory header lines)
S : Process state can be one of the following states:
•	D : Uninterruptible sleep
•	R : Running or runnable
•	S : Sleeping
•	T : Stopped or traced
•	Z : Zombie
System Daemon: Manages the startup process for Linux
Service Units
-	A system unit defines objects that the system knows how to manage
-	Units: Represented by config files named unit files, has info about system services, listening sockets and other relevant objects for the system init system
-	Unit has a name and unit type (enables grouping)
-	systemd daemon uses units to manage diff objects
-	SERVICE UNITS: .service extension and represent system services. You can use it to start frequent used daemons like webserver
-	SOCKET UNITS: .socket extension, represent interprocess communication. Can use to delay start of a service at boot at boot time
-	PATH UNITS: .path extension and delay activation of service until condition
Explpre current system’s current state: systemctl lab: systemctl list-units –type=service
Explore all service units regardless of activation states: --all
Filter options by values: --state=[LOAD/ACTIVE/SUB] fields
LOADED AND ACTIVE: systemctl
Display units that systemd service attempts to parse/load into memory: systemctl list-unit-files --type=service
Viewing Service States: systemctl status [NAME].[TYPE]
LOADED: Service loaded into memory
ACTIVE: Service unit running?
DOCS: Where to find more info
Main PID: Main process ID of service
Status: More info about the service
Process: More info about related processes
CGroup: More info about related control groups
Verify status of a service: systemctl status [NAME].service
Start service:  systemctl start [NAME]
Stop service: systemctl stop [NAME].service
Restart service: systemctl restart [NAME].service
Reload service: systemctl reload [NAME].service
Check both: systemctl reload-or-restart [NAME].service
List Dependencies for service: systemctl list-dependencies [NAME].service
List Reverse Dependencies for a service: systemctl list-dependencies –reverse [NAME].service
Mask Service: systemctl mask [NAME].service
Unmask Service: systemctl umask [NAME].service
Enable Service to Start at Boot:  systemctl enable --now sshd.service
Disable Service to Start at Boot: systemctl disable --now sshd.service
Verify if service is enabled or disabled: systemctl is-enabled sshd.service
NETWORKING
Identifying Network Devices: ip link show
Displaying IP: ip addr show ens3
Display current IP and netmask for all interfaces: ip -br addr
Display Performance: ip -s link show ens3
Verify connectivity: ping -c3 [IP_ADDR] / ping6 
Display Routing Table: ip route / ip -6 route
Tracing Traffic Routes:  tracepath access.redhat.com / tracepath6
Alternative method: mtr -r -c 5 access.redhat.com (report mode -r / must define num of pkts -c 5)
Display socket statistics: ss -tan
View host routing table: ip route
Display status of all network devices: nmcli dev status
Display all connections: nmcli con show (--active)[Show active connections]
Add network connection: nmcli con add ifname type ethernet
Managing Network Connections: nmcli -p con show [NAME] / nmcli con up [NAME]
Disconnects network device and closes connection: nmcli dev dis [NAME]
Select & display specific fields: nmcli -f [KEY],[KEY],… con show [NAME]
Modify connection settings: nmcli con mod [NAME] [KEY] [VALUE] [KEY] [VALUE]
Can add (+) or remove (-) from connection settings if nth is added then replaces
After updating must reload: nmcli con reload (ALL) or add [NAME] for specific
Deleting a Network Connection: nmcli con del [NAME]
View permission: nmcli gen permissions
Example:
Create the static-addr static connection for the ens4 interface with the 172.24.250.40 address and /24 network prefix.
Nmcli con add con-name static-addr type ethernet ifname ens4 ipv4.method manual ipv4.addresses 172.24.250.40/24 
 
EDIT NETWORK CONFIG FILES
-	NetworkManager connection profiles are stored in the /etc/NetworkManager/system-connections/ directory. 
-	Files = *.nmconnection
-	After manual edits, must use nmcli con reload + nmcli con up
NetworkManager organizes conf file in the following directory:
-	/etc/NetworkManager/system-connections/ (Persistent con profiles)
-	/run/NetworkManager/system-connections/ (Temp profiles, deleted upon sys reboot)
-	/usr/lib/NetworkManager/system-connections (Preconfig, immutable connection profiles)
Identify interface name and curr config: nmcli -f connection.interface-name,ipv4.method con show ‘Wired connection 1’
HOSTNAME
-	FQDN (Fully qualified domain hostname): Uniquely identifies each server in a network
-	Hostname command temporarily change systems FQDN
-	etc hostname web1.example.com, hostname  web1.example.com
-	Persistent one must be specified in /etc/hostname file
-	U can use hostnamectl hostname instead
-	Verify be checking cat /etc/hostname
-	Check /etc/hosts file, if no matching entry need to query DNS name server to lookup hostname
-	/etc/resolv.conf file controls how DNS query is performed
-	Nmcli con mod ‘Wired Connection 1’ ipv4.dns 172.25.250.220
-	Test DNS server connectivity: host [HOSTNAME]/[IP_ADDR]
-	More complex version:  dig [HOSTNAME]
-	Both do not consider hostname entries in the /etc/hosts file
-	Try getent hosts [HOSTNAME] that checks /etc/hosts file before resorting to DNS server lookup
MANAGING SSH HOST KEYS
-	Prevent impersonation attacks
-	SSH creates secure communication channels through Public Key encryption
-	SSH connect: Server first sends copy of its public key to client
-	Server’s key is important because client uses key to authenticate server’s identity
-	Client checks for record of servers public key in /etc/ssh/ssh_known_hosts or ~/.ssh/known_hosts
-	For security the SSH clients default setting is to require manual confirmation before new host key is accepted and stored
-	Customize how SSH client handles host keys by editing either /etc/ssh/ssh_config to configure system-wide SSH client settings or ~/.ssh/config to configure user-specific SSH client settings
-	StrictHostKeyChecking parameter 
o	(yes = most secure must match known_hosts, new hosts must manually add) 
o	(accept-new = adds host key to known_hosts file without prompting, but if host key change, ssh fails to connect) 
o	(no = least secure) 
o	(ask = default and recommended, ssh prompts you for confirmation, alerts u if host key changed)
o	Example: ssh -o StrictHostKeyChecking=accept-new hostb
-	Verify SSH Host Key Fingerprint: Short, cryptographic hash of the server’s public key (like a fingerprint)
o	Example: ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key.pub, there u can check with ssh hostb if the fingerprint matches
-	Regenerate SSH Host Keys on Servers: Host key files in /etc/ssh/ directory
o	Example: ls -l /etc/ssh/ssh_host_*
o	Delete existing key pairs: rm -f /etc/ssh/ssh_host_*
o	Restart SSH daemon: systemctl restart sshd.service
-	Managing known host files on clients: SSH clients who previously connected but then u regenerated host keys
o	Since the host key is still stored in its known_host file
o	Clients will receive warning
o	SSH clients keep a list of known systems and corresponding host keys in either /etc/ssh/ssh_known_hosts file or ~/.ssh/known_hosts file
o	First folder, system wide file the system admin must create it and add trusted host keys to it. (Pre populate this file)
o	Latter folder, this is a user-specific file in home dir in each user, if ssh client does not find remote system’s public key in the system-wide known hosts file, then looks in user file
o	Remove key entry for hostb: ssh-keygen -R hostb
o	If offending key in system-wide known host file admin needs to do it: ssh-keygen -R hostb -f /etc/ssh/ssh_known_hosts
-	Populating known host files
o	Collect public host keys of remote servers: ssh-keyscan
o	Manually populate known_hosts files, which is useful for scripting automation or setting up system-wide ssh_known_hosts file
o	Retrieve and save public key: ssh-keyscan hostb >> ~/.ssh/known_hosts
o	By default the command saves all key pairs that the server presented, verify: ssh-keygen -l -f ~/.ssh/known_hosts
o	Add new host to system-wide known host file: ssh-keyscan -q -t ed25519 hostb >> /etc/ssh/ssh_known_hosts
o	Scan a range of Ips to collect RSA host keys: ssh-keyscan -q -t rsa 172.25.250.0/24 >> /etc/ssh/ssh_known_hosts
-	Generate ssh keys: ssh-keygen -f [Secure folder]
-	Send public key of keypair to machine: ssh-copy-id -i [IDENTITY_FOLDER] [MACHINE]
-	For easier access u can use ssh agent
o	Start process in bg: eval $(ssh-agent)
o	Add the private key in mem cache: ssh-add ~/.ssh/protected-key
o	Login via same (~/.ssh/protected-key) folder path
-	Don’t allow normal user to login to root: From root edit /etc/ssh/sshd_config and comment ‘prohibit-password’ in PermitRootLogin field then systemctl restart sshd.service

RH134
Setting Variables/Aliases
-	HISTFILE, HISTFILESIZE, HISTFILEFORMAT
-	Etc. HISTTIMEFORMAT=“%F %T ”
-	Etc. PS1=“bash\$ ” or PS1=“[\u@\h \w]\$”
-	Etc. export EDITOR=vim/nano (Assign any variable that is defined in shell as env variable)
-	Etc. export PATH=${PATH}:/home/user/sbin
-	Alias hello=’echo “Hello, this is a long string.”’
-	Unset/unexport/unalias variable: unset [NAME]
-	Unexport variable:
-	Unexport variable without unsetting it: export -n PS1
Loops/If:
for VARIABLE in LIST; do
    COMMAND VARIABLE
Done
- echo $? (Check if the script returns a 0/1 exit code)
Conditional syntax (-gt, -lt, -eq, -ne, -f, -d, -r): Greater than, less than, equal, not equal, is file?, Directory exists?, current user has read perm?
-	Etc: [[ 1 -eq 1]]; echo $?
-	for VARIABLE in LIST; do
-	    COMMAND VARIABLE
-	Done
-	
-	while <CONDITION>; do
-	    <STATEMENT>
-	    ...
-	    <STATEMENT>
-	done
-	
-	until <CONDITION>; do
-	    <STATEMENT>
-	    ...
-	    <STATEMENT>
-	Done
-	
-	if <CONDITION>; then
-	    <STATEMENT>
-	    ...
-	    <STATEMENT>
-	elif <CONDITION>; then
-	    <STATEMENT>
-	    ...
-	    <STATEMENT>
-	else
-	    <STATEMENT>
-	    ...
-	    <STATEMENT>
-	fi
-	
REGEX
^: Match beginning, for example ^cat match with category
$: Match ending, for example cat$ match with concat
Exact search would be ^[WORD]$
[]: Matches exactly 1 character in brackets
*: Matches 0 or more characters
- Could also mean match the character before it
- To get the Regex equivalent use .*
.: Matches exactly 1 of any character
{<NUMBER>}: use .{2} for example
GREP
-i: use provided regex with no case sensitivity
-v: display only lines that do not contain matches to regex
-r: search for data that matches regex recursively in a group of files
-A: display no of lines after regex match
-B: display no of lines before regex match
-e: if multiple -e used, can use multi regex (used with logical OR)
-E: use extended instead of basic regex when parsing regex
Examples:
Remove comment lines: grep -v '^[#;]'
Table 2.1. Basic and Extended Regular Expression Syntax
Basic syntax	Extended syntax	Description
.	The period (.) matches any single character.
?	The preceding item is optional and is matched at most once.
*	The preceding item is matched zero or more times.
+	The preceding item is matched one or more times.
\{n\}	{n}	The preceding item is matched exactly n times.
\{n,\}	{n,}	The preceding item is matched n or more times.
\{,m\}	{,m}	The preceding item is matched at most m times.
\{n,m\}	{n,m}	The preceding item is matched at least n times, but not more than m times.
[:alnum:]	Alphanumeric characters: [:alpha:] and [:digit:]; in the 'C' locale and ASCII character encoding, this expression is the same as [0-9A-Za-z].
[:alpha:]	Alphabetic characters: [:lower:] and [:upper:]; in the 'C' locale and ASCII character encoding, this expression is the same as [A-Za-z].
[:blank:]	Blank characters: space and tab.
[:cntrl:]	Control characters. In ASCII, these characters have octal codes 000 through 037, and 177 (DEL).
[:digit:]	Digits: 0 1 2 3 4 5 6 7 8 9.
[:graph:]	Graphical characters: [:alnum:] and [:punct:].
[:lower:]	Lowercase letters; in the 'C' locale and ASCII character encoding: a b c d e f g h i j k l m n o p q r s t u v w x y z.
[:print:]	Printable characters: [:alnum:], [:punct:], and space.
[:punct:]	Punctuation characters; in the 'C' locale and ASCII character encoding: ! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ' { | } ~.
[:space:]	Space characters: in the 'C' locale, it is tab, newline, vertical tab, form feed, carriage return, and space.
[:upper:]	Uppercase letters: in the 'C' locale and ASCII character encoding, it is: A B C D E F G H I J K L M N O P Q R S T U V W X Y Z.
[:xdigit:]	Hexadecimal digits: 0 1 2 3 4 5 6 7 8 9 A B C D E F a b c d e f.
\b	Match the empty string at the start or end of a word.
\B	Match the empty string provided that it is not at start or end of a word.
\<	Match the empty string at the start of a word.
\>	Match the empty string at the end of a word.
\w	Match the word constituent. Synonym for [_[:alnum:]]
\W	Match the non-word constituent. Synonym for [^_[:alnum:]]
\s	Match white space. Synonym for [[:space:]]
\S	Match non-white space. Synonym for [^[:space:]]

Run one time jobs in future
At: Schedules job to run once at a future date/time
Queue: Users can queue jobs into different queues labelled a-z A-Z (Higher-Lettered queues have higher priority)
TIMESPEC, command reads tasks to be performed from std input (type of pipe into it), press Ctrl+D on empty line to finish
At now +5min, 15:59, teatime, noon +4 days
View Pending Jobs: atq, at -l (List jobs scheduled)
Inspect Job Content: Use at -c JOBNUMBER
Remove Jobs: Use atrm JOBNUMBER
CRON JOBS (Recurring user jobs)
List jobs for current user: crontab -l
Remove all jobs for current user: crontab -r
Edit jobs for current user: crontab -e (Invokes vim by default) USE THIS TO Implement a cron job
Remove all jobs, replace them with jobs read from file, uses stdin input when no files specified: crontab [FILENAME]
Privileged user can use crontab -u to manage jobs for another user
Format: [Min] [H] [D] [M] [DAY OF WEEK] [Command]
Etc: 11th day of every mth and every Friday at 12:15 (24hr)
15 12 11 * Fri command
Send email Chime at every 5th minute past every hour from 9 – 16 on Fri in July:
*/5 9-16 * Jul 5
List all timer united loaded in memory: systemctl list-units -t timer
List all installed timer unit files: Systemctl list-unit-files -t timer
Start/stop/enable/disable/status/(reload daemon to ensure system loads changes) system timer units: 
systemctl start/stop/enable/disable/status/daemon-reload (--now) logrotate.timer
MANAGE TEMPORARY FILES
System-tmpfiles
-	Purpose: Provides structured way to create/delete/secure temporary directories/files
-	Mechanism: At boot, the systemd-tmpfiles-setup svc read conf files from /usr/lib/tmpfiles.d/, /run/tmpfiles.d/ and /etc/tmpfiles.d/
Clean Temp files (Automatically)
-	Systemd-tmpfiles-clean.timer unit triggers system-tmpfiles-clean.service, which runs systemd-tmpfiles –clean
-	You can modify timer settings (OnUnitActiveSec)
-	Run systemctl daemon-reload to apply changes
Clean Temp files (Manually)
-	Run systemd-tmpfiles –clean
-	Purges files that have not been accessed, changed or modified within the 
maximum age defined in conf files
Overrides
-	/usr/lib/tmpfiles.d/ < /run/tmpfiles.d/ < /etc/tmpfiles.d/
-	Do not edit files in /usr/lib/tmpfiles.d, these are managed by RPM packages, instead copy file to /etc/tmpfiles.d
-	Syntax: Conf files use: Type, Path, Mode, UID, GID, Age, Argument
Useful commands
View Timer Conf: systemctl cat systemd-tmpfiles-clean.timer
Check Timer Status: systemctl status system-tmpfiles-clean.timer
Testing: Apply conf from single file at a time using systemd-tmpfiles

Cron Jobs
Best practice: Schedule recurring system jobs using system accounts via files in /etc/cron.d/ rather than editing /etc/crontab. Prevents package updates from overwriting configs
Syntax Diff: System crontabs include extra user-name before command field
*/2: Indicates interval ie every 2 minutes
Directories etc: /etc/cron.hourly, .daily, .weekly, .monthly store exe shell scripts (Needs x permission [chmod +x])
ANACRON (Non-constant systems)
Purpose: Ensures perioding jobs are executed even when system off/asleep when task originally scheduled
Tracking: Last execution times tracked via timestamps in /var/spool/anacron
Configuration (/etc/anacrontab): uses 4 fields: Period in days, Delay in minutes, Job identifier and Command
Variables: RANDOM_DELAY, START_HOURS_RANGE: Dictates allowed hours where jobs can run

HOW DOES CRON AND ANACRON COOPERATE
-	Every hour at :01, Cron runs script /etc/cron.hourly
-	Inside dir, script named 0anacron triggers the anacron -s command
-	Anacron checks /etc/anacrontab to see if any jobs missed, if any runs after specified delays

LOGGING ARCHETECTURE
RHEL logs system events for auditing and troubleshooting using a standard syslog protocol managed by two primary services:
-	Systemd-journald: collects event messages from system kernal, output from early stages of the boot process, standard output, daemon outputs and syslog events. Standardizes indexes and writes these logs into a system journal. (Volatile, does not persist across reboots)
-	Rsyslog (Persistent): uses imjournal module to read syslog messages directly from the system journal as they arrive. It sorts these messages by prog type and priority > /var/log directory
/var/log/messages: The primary log file for most general system messages (excluding security, mail, cron, and pure debugging events).
/var/log/secure: Stores security-related events and authentication logs (e.g., logins, sudo attempts).
/var/log/maillog: Contains logs related to the system mail server.
/var/log/cron: Records logs regarding scheduled tasks and job executions.
/var/log/boot.log: Captures console and system startup diagnostic messages. It is populated by the Plymouth splash screen application and any boot-related messages sent to the local7 syslog facility.
 
 
Rsyslog service decides what to do with a system message based on Rules configured in /etc/rsyslog.conf or /etc/rsyslog.d/
Each rule has 2 parts:
-	Filter: type of prog (authpriv, cron, mail) and priority (severity level, e.g., info, warning, crit).
o	*: Matches all facilities or priorities
o	Severity Levels: Specifying priority logs that level and anything higher/more severe
o	Exclusions: tells the system not to log a specific facility to that file
-	Action: Dictates where the message goes
Old logs kept for about 4 weeks before oldest ones permanently deleted to save disk space
Log Entry: [TIMESTAMP] [HOST] [PROGRAM [PID]]: [MESSAGE]
Real-Time Monitoring: (tail -f) lets u watch a log file update live in terminal
Manual log generation (logger) force a test message into logging system using logger command. Etc: logger -p local7.notice “Test”
 /etc/rsyslog.d/debug.conf configuration file. Here you can log messages to a specific folder for instance the debug logs to /var/log/messages-debug.
*.debug /var/log/messages-debug, then systemctl restart rsyslog

Journalctl:
-n <NUM>: last <NUM> log entries (10 by default)
-f: last 10 lines of system journal and continues
-p <debug|info|notice|warning|err|crit|alert|emerg>: Filter logs
-u <systemd unit>: for example logs for sshd.service
--since <DAY>: “{YEAR}-{MTH}-{DAY} {HR}:{MIN}”
--until <DAY>: etc “2025-06-04 10:00” or “-1 hour”
Additional:
_COMM: command name
_EXE: path to the executable file for the process
_PID: PID of the process
_UID: UID of the user that runs the process
_SYSTEMD_UNIT: system unit that started the process
Etc: journalctl _SYSTEMD_UNIT=sshd.service _PID=2188
-b <NUM>: retrieves entries from the <NUM> system boot only (Current for default)
--list-boots: list system boot events

By default, Red Hat Enterprise Linux stores the system journal files in /run/log/journal and is cleared after reboot.
Conf systemd-journald service to keep persistently
-	Mkdir /var/log/journal
-	Journalctl –flush (Flush current journal to storage)
-	Ls /var/log/journal
-	Ls /var/log/journal/<HEX> (.journal binary files stored here)
-	Systemctl restart systemd-journald

System Journal rotation:
-	System does not allow journal to get larger than 10% of file system
-	Or leave less than 15% of the file system free
-	Can modify runtime and persistent journals in /etc/systemd/journald.conf
-	Journalctl | grep -E ‘Runtime Journal|System Journal’

Journal Conf files:
Edit the /etc/systemd/journald.conf or add conf files with a .conf suffix to the /etc/system/journald.conf.d directory

Conf Auto Journal Rotation:
Set in /etc/systemd/journald.conf,
Prefix System for persistent, Runtime for volatile journals in /var/log/journal

SystemMaxUse and RuntimeMaxUse
The maximum amount of file system space that the journal can use. This value is 10% of the total file system space by default, and is capped at 4 GB.
SystemMaxFileSize and RuntimeMaxFileSize
The maximum file size of a journal before it rotates. This value is 1/8 of the SystemMaxUse/RuntimeMaxUse value by default, capped at 128 M, usually supports keeping seven rotated journal files as history. If journal compact mode is enabled (the default), this value is capped at 4 GB.
SystemKeepFree and RuntimeKeepFree
The minimum free file system space that must remain before archived journals are dropped. At least 15% of the total file system space is always kept free by default. This value is capped at 4 GB.
Restart the systemd-journald service: systemctl restart systemd-journald

NTP-ADMINISTER LOCAL CLOCKS AND TIMEZONES
Overview of current time-related system settings: timedatectl
List database of time zones: timedatectl list-timezones
See all avail timezones: tzselect
Set timezone to America/Phoenix: timedatectl set-timezone America/Phoenix
Manually set time: timedatectl set-time "YYYY-MM-DD hh:mm:ss" 
(If error “Automatic time synchronization enabled”, disable first)
Enable/Disable NTP synchronization: timedatectl set-ntp true/false
Chronyd service synchronizes local system clock with network NTP servers. If network drops, it tracks clock drift using /var/lin/chrony/drift
Stratum Hierarchy: Time sources graded by accuracy and distance from atomic/GPS reference clock (Stratum 0,1,2…)
Best practice: Configure 3 or more time sources
-	Pool directive: Points to dns name that rotates through multiple dynamic public servers
-	Server directive: Specifies static, single NTP server by IP/Hostname (Isolated network)
-	Iburst option: recommended, forces 4-8 rapid time checks upon startup
Any changes to /etc/chrony.conf require a systemctl restart chronyd
Monitor status: chronyc sources -v
^: server mode -> Source is an external NTP server
*: Current best -> Pri server actively syncing system clock
+: Contributing -> Valid, healthy backup server contributing to sync
-: Usable -> Avail but not used (better alternatives)
X/~: Error/Unstable -> Server data is faulty or too variable to be trusted

SELINUX
View SElinux labels, add -Z to standard tools
View process contexts: ps axZ or ps -ZC httpd
View file/dir contexts: ls -Z /var/www
Manage contexts during creation/cp: mkdir -Z / cp-Z
3 Operational Modes: Enforcing (Actively blocks actions), Permissive (does not block but logs what would have been blocked), Disabled (no logs and discouraged) this is not supported in RHEL 10
Check current mode: getenforce
Change mode temporarily (runtime): setenforce 0 (permissive), 1 (enforcing)
Change mode permanently: Edit config file (/etc/selinux/config) (Best to sys reboot)
-	SELINUX=enforcing|permissive
-	SELIUXTYPE=targeted
Kernal Boot parameters: enforcing=0(permissive)|1(enforcing)
Selinux=0(disable)|1(enable)
-Z: command displays SELinux context of a file/dir
Temporary
Chcon: sets file context of virtual directory to httpd_sys_content_t type 
(chcon -t httpd_sys_content_t /virtual)
Restorecon: command resets the context to the default value of default_t
Permanent
Semanage fcontext -a -t <type> ‘regex’  restorecon -Rv /path
Adds a permanent rule to SELinux database then applies it
(/.*)?: Pirate Regex, when adding policy for directory this matches directory itself and all files inside recursively
Cheatsheet:
List all default policies: semanage fcontext -l
List local/custom policies: semanage fcontext -l -C
Add permanent rule for a custom directory: semanage fcontext -a -t httpd_sys_content_t ‘/virtual(/.*)?’
Restore/Apply default db context to files (recursive+verbose): restorecon -Rv /path/to/dir
View file context of directory: ls -Zd /dir
List all available Booleans along with status: getsebool -a
Note: Setting Booleans with setsebool command without the -P option is temp
List httpd_enable_homedirs bool: semanage boolean -l | grep httpd_enable_homedirs (Perm , Temp)
Enable httpd_enable_homedirs: setsebool httpd_enable_homedirs on (-P) for permanent
List only persistent Booleans with customized settings vs original: semanage Boolean -l -c
Troubleshooting SELinux Denials
-	SELinux denies by default, if no rule explicitly allowing it it is blocks and a denial is logged
-	Everytime SELinux blocks something Access Vector Cache message is logged
Workflow:
-	Issue occurs
-	Notice the failure (curl or service fails)
-	Find alert in system logs
-	Use sealert to let system analyze log and translate to plain English
-	Apply fix (restorecon or adjust permissions)
Cheatsheet:
View raw security log directly: tail /var/log/audit/audit.log
Search for recent SELinux denials (AVCs): ausearch -m AVC -ts recent
Find automated summary and critical UUID: tail /var/log/messages
Analyze specific log event using uID: sealert -l <UID>
Scan entire audit log file and generate human-readable report: sealert -a /var/log/audit/audit.log
Reset file back to default system policy label: restorecon -v /var/www/html/mypage
Reset entire directory recursively: restorecon -Rv /var/www/html
RHEL web console: SELinux in left menu -> View errors under Access control errors -> Expand to read and apply solution to let system auto fix label 
TAR Files
Create an archive file: -c or --create
List the contents of an archive: -t or --list
Extract an archive: -x or –extract
Show files archived/extracted during opr: -v or --verbose
Follow this option with the archive file name to create/open: -f or --file
Preserve the original file permissions when extracting: -p or --preserve-permission
Enable extended attribute support and store extended file attributes: --xattrs
Enable SELinux context support and store SELinux file contexts: --selinux
Select algo:
Use archive suffix to determine algo to use: -a or --auto-compress
Use gzip compression algo (.tar.gz): -z or --gzip 
Use bzip compression algo (.tar.bz2): -j or --bzip2
Use xz compression algo (.tar.xz): -J or --xz 
Create archive: tar -cf [ARCHIVE_NAME] [FILE_1] [FILE_2] …
The archive tar file will contain all the files (User needs read permission)
List archive contents: tar -tf /root/etc.tar
Extract archive contents:
-	mkdir /root/etcbackup
-	tar -tf /root/etc.tar
-	(Go in folder etcbackup) tar -xf /root/etc.tar
-	Tar -xpf /home/user/myscripts.tar
Create/Extract Compressed Archive:
-	Tar –[c|x][z|j|f]f [TAR_FILE] [FOLDER]
View uncompressed size of gz: gzip -l file.tar.gz
View uncompressed size of xz: xz -l file.tar.xz

Transfer files with SFTP
When u run sftp provide remote user and host: sftp remoteuser@remotehost
Print current working dir on remote host: pwd
Print current working dir on local host: lpwd
U should be in the directory u want file to be copied to: put [file] / put -r [folder]
Single line: sftp [USER]@[HOST]:[FILEPATH]
Etc: How to copy local /etc/dnf/dnf.conf and /etc/hosts files to remote host’s home dir
-	Scp /etc/dnf/dnf.conf /etc/hosts remoteuser@remotehost:/home/remoteuser
Other direction:
-	Scp remoteuser@remotehost:/etc/hostname /home/user
SYNCHRONIZE CONTENT BETWEEN SYSTEMS
-	Rsync: Copy files efficiently and securely between local and remote
-	Initial directory synchronization takes similar time copying it and subsequent copies only the differences hence accelerates updates substantially
-	Rsync -n: dry run to simulate what happens
-	-v or --verbose: provides more detailed output (troubleshooting/live viewing)
-	-a or --archive: archive mode, enables recursive copying and turns on other options to preserve more characteristics of the files
o	-r, --recursive: synchronizes whole dir tree recursively
o	-l, --links: synchronizes symbolic links
o	-p, --perms: preserves permissions
o	-t, --times: preserves timestamps
o	-g, --group: preserves group ownership
o	-o, --owner: preserves owner of files
o	-D, --devices: preserves device files
-	Hard links is not included in archive mode since it takes much longer time
-	Rsync -H can be used to preserve hard links
-	Example: rsync -av /var/log hosta:/tmp (Always from/to)
TUNING
-	Get best performance out of hardware by fine tuning
-	TuneD is a powerful tool, automatically optimizes system performance and power efficiency by applying predefined tuning profiles
-	Runs continuously in bg as a daemon. Can be applied statically or dynamically
-	For dynamic, system needs a ‘tuned’ daemon to monitor
-	Monitor plugins analyse system and gather real time data (CPU load/disk i/o, network traffic
-	Tuning plugins use this for dynamic tuning, etc the net plugin can dynamically change interface speed based on instance usage
-	Turn on by changing the ‘dynamic_tuning’ to 1 in /etc/tuned/tuned-main.conf
-	Vm.swappiness dictates how aggressively the kernal moves processes from physical memory to swap space (sysctl vm.swappiness
Tuned-adm (To manage TuneD apps and profiles)
Identify current active tuning profile: tuned-adm active
Verify if current system settings match the active profile: tuned-adm verify
Lists all avail tuning profiles (includes built in and custom created): tuned-adm list
Info about specific tuning profile: tuned-adm profile_info <PROFILE_NAME>
Switch to diff profile that better matches system tuning req: tuned-adm profile virtual-guest
Tuning profile based on various system characteristics: tuned-adm recommend
Revert settings that current profile applied: switch to diff profile or deactivate the tuned daemon: tuned-adm off
Limited access or Turn on admin access > click overview
Enable web console
1.	Dnf install cockpit
2.	Systemctl enable --now cockpit.socket
3.	Firewall-cmd –add-service=cockpit --permanent (If using need add)
4.	Firewall-cmd --reload
Workflow for tuned
-	Check if tuned package installed: dnf list tuned
-	Check if enabled/active: systemctl status/is-enabled/is-active tuned
-	List avail tuned profiles: tuned-adm list
-	Review conf file for current profile
-	Verify current system matches profile: tuned-adm verify
-	Verify vm.dirty_ratio and vm.swapiness: sysctl [A|B]

Core scheduling concepts
Nproc: How many processors/CPU cores
Pre-emptive multitasking: 
Pre-emptive multitasking: OS uses time slicing to share CPU cores among many competing processes. The kernel’s process scheduler forces running processes to pause so others can get a turn, the interruption is called pre-emption (Stops single process from locking up the system)
Scheduling policies: 
SCHED_NORMAL/SCHED_OTHER: Default policy used for standard, non-real time apps
SCHED_FIFO/SCHED_RR: Real-time Policies, used for time critical tasks that take precedence over normal apps
EEVDF Scheduler
-	Calculates a virtual deadline for each task based on priority and how much CPU time it is owed and the task with earliest deadline runs next
-	Provides better responsiveness and lower latency compared to CFS
Nice Values
EEVDF scheduler handles things automatically, users can influence a process’s share of CPU time using Nice Values
-	Range: -20 (Highers priority/Least nice) to +19 (Lowest priority/Most nice) Default = 0
-	Lower nice value gives a process more weight, moving its virtual deadline earlier so it runs sooner and more often
-	Regular users can only increase not lower nor touch other users
-	Root users can decrease values to increase priority
-	Nice Values only affect standard SCHED_NORMAL not push priority above real time tasks
View Priorities
TOP (Dynamic)
-	Provides live constantly updating look at the system
-	NI: Explicit Nice Value of process
-	PR: Kernel’s calculated internal priority
o	PR = 20 + nice_value
o	Real time processes will display -ve number or string rt
PS (static snapshot)
-	Ps -o pid, priority, nice, cls, pcpu, comm -C <command>
-	Ps -eo … --sort=-nice (sort nice by low  High)
-	CLS: TS stands for time-sharing, while FF or RR indicates real time
Managing Priorities in the CLI
Start process with specific priority
-	By default new commands inherit nice value of 0
-	Change at launch: nice sha1sum /dev/zero & (Starts sha1sum with default nice adjustment of +10)
-	Nice -n 15 sha1sum /dev/zero & (sha1sum = nice value of +15)
Change running process (renice)
-	Renice -n 19 <PID> (Change nice value of PID to 19)
-	Can also use top, press R and enter PID to type new nice value

Disk Partitioning Schemes
-	Disk partitioning divides a physical hard drive into logical storage spaces, admin can limit app space, isolate the os from user data and optimize performance
Feature	Master Boot Record	GUID Partition Table
Firmware Standard	Legacy Bios	Modern UEFI
Max Partitions	4 Pri (up to 15 using Extended/Logical)	128 Partitions

Max Disk Size	2 TiB	8 ZiB
Redundancy	None (1 partition table)	Backup table at the end of the disk
Data Integrity	NA	Checksums to detect table errors

Managing Partitions with parted
RHEL uses parted to manage MBR and GPT disks
-	Inspect Disks: parted /dev/sda print
-	Labelling New Disks (mklabel): Before partitioning disk u must write disk label
o	MBR: parted /dev/sdb mklabel msdos
o	GPT: parted /dev/sdb mklabel gpt
o	Creating new label wipes existing partition table
-	Create Partitions: To maximize disk performance, a starting sector which is a multiple of 2048 is recommended for proper block alignment
o	MBR: parted /dev/sdb mkpart primary xfs 2048s 1000MB
o	GPT: parted /dev/sdb mkpart userdata xfs 2048s 1000MB
o	Availability: After partitioning run udevadm settle to force system to wait till new /dev/sdb1 device created and ready
-	Delete Partitions: parted /dev/sdb rm 1 (Removes partition 1)
Formatting & Mounting File Systems
-	Create file system: 
o	XFS: mkfs.xfs /dev/sdb1
o	Ext4: mkfs.ext4 /dev/sdb1
-	Mounting Manually:
o	Mount /dev/sdb1 /mnt
o	To verify active mounts: mount | grep sdb1
Persistent Mounting:
-	Manual mounting disappear when system reboots
-	Make it perm by adding entry to /etc/fstab config file
/etc/fstab
-	Device Id: Red Hat recommends UUID retrieved via lsblk --fs because traditional device names like /dev/sdb1 can be dynamically changed during reboots
-	Mount Point: target dir (/dbdata)
-	File System: xfs/ext4
-	Mount Options: Comma-separated behaviors
-	Dump Support: Used by legacy backup utility
-	Fsck Order: System boot file check order. Set to 0 for XFS. Set to 1 for root file system on ext4, and 2 for secondary ext4 file systems
Error in /etc/fstab can prevent system from booting
Test config before rebooting: findmnt –verify
Unmount device and run: mount -a
Whenever modify /etc/fstab: systemctl daemon-reload

Use-case:
U are given a hard drive and plug it into the server
-	Add new drive to the system, format it
-	Permanently mount to a directory called /archive
-	Reboots it should still reconnect
Labelling
-	Parted /dev/sdb mklabel msdos
-	MBR label to the new disk
Create partition boundary
-	Parted /dev/sdb (Interactive mode)
-	Dividing raw disk to usable chunks
-	Primary: tells system that it is a standard bootable partition type
-	Xfs: type of filesystem
-	2048s: Modern standard
-	1001MB: 1GB partition space
Let kernel catch up: udevadm settle
Formatting: mkfs.xfs /dev/sdb1
-	Partition is like an empty land
-	Running this create the internal directory structures, tracking tables and datablocks (Drawing lines and folders on empty plot) 
Persistence:
-	Lsblk --fs /dev/sdb (get UUID)
-	UUID=<UUID> /archive xfs defaults 0 0
-	Systemctl daemon-reload
-	Mount /archive (Tests config)
-	Mount | grep /archive (last confirmation)

SWAP SPACE
-	Area on a hard drive
-	Controlled by kernel memory management subsystem
-	Acts as an extension to physical RAM
-	RAM + SWAP space = Virtual Memory
-	When RAM utilization hits limit, kernel hunts for inactive/idle memory pages and writes them to swap space on disk
-	Frees up physical RAM blocks for active applications
-	Swap space is a safety net against crashes, not perm fix
Sizing:
-	Systems with <= 2GB RAM: Allocate 2x amount of RAM
-	Systems with 2 – 8GB RAM: Allocate equal to amount of RAM
-	Systems with 8 - 64GB RAM: Allocating at least 4GB is recommended
-	If hibernation (save RAM state to disk before shutting down, swap space must be larger than physical RAM size
Create/Format Swap Space:
-	Create dedicated partition and Initialize with unique signature
-	Create Partition: parted /dev/sdb mkpart swap1 linux-swap 1001MB 1257MB
-	Same as normal partition but filesystem type is linux-swap
-	Then udevadm settle
-	Format Swap space: mkswap /dev/sdb2
Activate and Deactivate Swap:
-	Turn on Swap: swapon /dev/sdb2
-	Check Active Swap: free/swapon --show 
-	Turn off Swap: swapoff /dev/sdb2
Persistent Activation and Priorities:
Perma mount via /etc/fstab: UUID=<UUID> swap swap (defaults or pri=<NUM>) 0 0 
LVM
-	Logical Volume Manager places an abstract software layer over physical hard drives
-	U can combine multi physical disks into single large storage pools
-	U can expand or shrink volumes dynamically without unmounting filesystems or stopping apps
-	OS sees logical drive, hiding the number of physical disks backing it
3 core architectures
-	Physical Volume: Raw materials, disks initialized for LVM, LVM breaks them into tiny data blocks called physical extents (4MiB each usually)
-	Volume Groups: The bucket. Dump one or more PVs into a VG to create a pool of PE (Like an enormous virtual hard drive
-	Logical Volumes: Rooms. Slice pieces of VG pool to create usable block storages. LV is made of Logical Extents (LE) that map directly to Pes in the pool
Raw Partition  PV  VG  LV  Format & Mount
1: Hardware prep
Create standard disk partitions: parted /dev/sdb set 1 lvm on (lvm toggle on)
Let the machine update: udevadm settle
2: Initialize Physical Volumes
Label raw partitions: pvcreate /dev/sdb1 /dev/sdb2
3: Create Volume Groups
Pool physical components under 1 group: vgcreate vg0 /dev/sdb1 /dev/sdb2
4: Slice out a logical volume
Carve out slice from virtual pool(-L:bytes)(-l:num of extents): lvcreate -n lv01 -L 300M vg01 or -l 32 (32 extents x 4 MiB = 128 MiB)
5: Format and Mount
New logical device lives under /dev/vgname/lvname:
-	Mkfs -t xfs /dev/vg01/lv01
-	Mkdir /mnt/data
-	Systemctl daemon-reload
-	Mount /mnt/data/
List (Physical|Group|Logical) by adding: pvdisplay|vgdisplay|lvdisplay
Parted /dev/sdb unit mib print (u can list partitions using MiB as display unit)
Extend Logical Volume
U can increase logical volume without experiencing downtime. Add free physical extents to the LV from the VG, to extend LV capacity and to expand file system. Use vgdisplay to confirm that the VG has enough free space for the LV extension
Extend LV: lvextend -L +500M /dev/vg01/lv01
# lvextend -l option expects the number of PEs as the argument
# lvextend -L option expects sizes in bytes, mebibytes, gigibytes, or similar
Expand XFS file system: xfs_growfs /mnt/data
# Command is done after lvextend
Expand file system to occupy the new extended LV: resize2fs /dev/vg01/lv01

Although you can resize an ext4 file system up or down, you can resize an XFS file system only up.
Pvcreate /dev/vdd
Vgextend VG /dev/vdd
Lvextend -r -l +100%FREE /URL (Get from df -hT /FOLDERNAME)

Extend Swap Space Logical Volumes
Deactivate swap space on the LV: swapoff -v /dev/vg01/swap
Extend the LV: lvextend -L +300M /dev/vg01/swap
Format LV as swap space: mkswap /dev/vg01/swap
Activate swap space on the LV: swapon /dev/vg01/swap

Extend VG: vgextend [NAME] [Mount point]

REPLACE PV in VG:
Prepare physical device and create PV: pvcreate /dev/sdb3
Add PV to VG: vgextend vg01 /dev/sdb3
Reduce VG storage: pvmove -A y /dev/sdb1
Remove PV from VG: vgreduce vg01 /dev/sdb1
Prepare File System: umount /mnt/data
Remove Logical Volume: lvremove /dev/vg01/lv01 (rm lv that is no longer needed, umount first)
Remove Volume Group: vgremove vg01
Remove the Physical Volumes: pvremove /dev/sdb2 dev/sdb3

What is a Boot Loader
When a computer turns on it goes from powered down state to working
-	Step 1: Motherboard firmware (BIOS vs UEFI): Motherboard firmware initializes basic hardware. BIOS (Old), UEFI (New) Sole job is to locate and execute the boot loader on storage device
-	Step 2: Bootloader (GRUB2) RHEL 10 defaults to GRUB2, loads essential file system modules, presets user boot menu and passes execution control over to linux kernel
o	UEFI: GRUB2 relies on files inside the EFI System Partition (mounted at /boot/efi)
o	BIOS: GRUB2 embeds itself inside Master Boot Record space at beginning of drive
Temporary vs Persistent changes
U can customize how kernel boots by changing parameters
Temp:
-	When GRUB2 menu appears on screen during startup, press R key to open interactive text editor
-	Changes made here apply only to that boot instance
-	Once reboot again, modifications disappear
Persistent:
-	Use grubby (cli tool to update GRUB2 config)
-	Permanent, changes persist across all reboots/kernel upgrades
GRUBBY
Inspect Boot Entries: grubby --info 1
Change Default Boot Kernel: grubby –set-default-index 0
Modify Kernel Command-Line Args: grubby --update-kernel /boot/vmlinuz-6.12.0-55.9.1.el10_0.x86 --args=“rhgb quiet” (hide wall of text scrolling)
Remove Args: grubby --update-kernel /boot/vmlinuz-6.12.0-55.9.1.el10_0.x86_64 --remove-args="rhgb quiet"
RHEL 10 Initialization Flow
Once GRUB2 hands over control, OS system boots in a multi-stage process:
1.	Load kernel & initramfs: GRUB2 loads the linux kernel and the initramfs (Initial RAM Filesystem) image into memory
o	Initramfs: Temp, mini-root filesystem containing essential drivers, scripts and mini systemd instance needed to wake system storage hardware (can inspect using lsinitrd)
2.	Executing PID 1: Kernel activates hardware drivers and runs /sbin/init (symbolic link to systemd) inside the initramfs as Process ID 1
3.	Mounting real root: Systemd processes initrd.target, finding actual hard drive root filesystem via /etc/fstab and mount it temporarily to /sysroot
4.	Pivot: Kernel shifts its focus from temporary initramfs in RAM to real root directory in /sysroot. Systemd re-executes itself from physical disk and takes full control of the final boot process
5.	Reach target: Systemd loads default target and starts all background services
Choose store layout (system Targets)
Target = Operating state or layout for store depending on what is needed that day
Graphical.target: Full service display
Multi-user.target: Express Counter
Rescue.target: Maintenance Mode
Emergency.target: Disaster Mode

Check current layout: systemctl get-default
Change layout tmr: systemctl set-default multi-user.target (Text only)
Change layout now: systemctl isolate multi-user.target (switches without restarting)

What happens if broken config file locks u out of server, u have to pause GRUB2 Countdown (any key)
-	Press E to edit manager blueprint
-	Goto line starting with Linux and write system.unit=rescue.target at the very end
-	Ctrl + X
Corrupted File System
-	System unit attempts to repair the filesystem. If the problem cannot be resolved automatically, the system opens emergency shell
Non-existent storage device, UUID or Mount point
Systemd unit times out waiting for storage device to become available, if device doesn’t respond, then system opens emergency shell
Checking and repairing FS corruption
Check and repair issues (XFS): xfs_repair block-device
(ext4): fsck.ext4 -p block-device
Repair file-system issues at Boot
-	Emergency shell appears
-	Find which file system is mounted by systemd unit: mount
-	It might be in ro (read-only)
-	Remount root file system with rw: mount -o remount,rw /
-	Try to mount all filesystems listed on /etc/fstab: mount --all
-	If /mnt/mount does not exist create it: mkdir ….
o	Systemctl daemon-reload
o	Mount –all
Recover SUDO user
Using rescue media
1.	Reboot system
2.	Select boot.iso image to boot from
3.	Troubleshooting -> Rescue a RHEL system -> Continue
4.	Chroot /mnt/sysroot
5.	Passwd root (give new passwd)
6.	Ensure all unlabelled files relabelled: touch /.autorelabel
7.	Exit exit 
Without rescue media
1.	Reboot system
2.	Interrupt boot-loader with ESC
3.	Boot with init=/bin/bash
a.	Move cursor to kernel entry to boot
b.	E
c.	Find linux line
d.	Remove console= options
e.	Move to end: end key / ctrl + e
f.	Add string init=/bin/bash (at the end)
g.	Ctrl + X
4.	Change / file system to rw: mount -o remount,rw /
5.	Passwd
6.	Touch /.autorelabel
7.	Exec /sbin/init

Managing Network Security
Core Firewall Architecture
-	The Engine (nftables): In the background, the kernel uses a framework called netfilter to handle traffic. In rhel 10 nftables creates and executes the firewall rules replacing iptables
-	The Manager (firewalld): use firewalld service. Friendlt dynamic front-end manager that translates easy commands into active network rules on the fly
Zones:
-	Trusted: complete trust, allows all incoming traffic
-	Public: Default setting for new networks. Untrusted. Blocks everything except specific essential services like SSH
-	Home/Internal: High trust. Assumes u are on safe local network, opens up file-sharing and printing client ports
-	Drop/Block: Absolute lockdown. Rejects all incoming connections
Predefined Services
U don’t have to memorize port numbers, u can use predefined services. Opening a service automatically maps and opens all the ports it needs
-	Ssh -> Open 22/tcp
-	Cockpit -> Open 9090/tcp
-	Ipp-client -> Open 631/udp
Can view using firewalld-cmd --get-services
Runtime vs Perma
Understand where that change is saving:
-	Runtime config: Applies to the active system now, if reboots, the changes wiped
-	Perma (--permanent): Saves rules to disk so they survive reboots. Perma rules do not take into effect immediately until u reload
Etc: 
firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --reload

Cheat Sheet
Check current default zone: firewall-cmd --get-default-zone
Change the default zone: firewall-cmd --set-default-zone=dmz
See everything allowed in a zone: firewall-cmd --list-all --zone=public
Show zones currently handling traffic: firwall-cmd --get-active-zones 
Route a specific network to a zone: firewall-cmd --permanent --zone=internal --add-source=192.168.0.0/24
Open a service permanently: firewall-cmd --permanent --zone=public --add-service=mysql
Open a custom port permanently: firewall-cmd --permanent --zone=public --add-port=8080/tcp
Apply permanent changes live: firewall-cmd --reload
Save currently live rules to permanent: firewall-cmd --runtime-to-permanent
Filter port number: grep gopher /etc/services
List current port label assignments: semanage port -l
Filter SELinux port label by using service name: semanage port -l | grep ftp
Filter by port number: semanage port -l | grep -w 70
Label a new port with existing port context label
Semanage port -a adds new port label, -t denotes type, -p denotes the protocol:
Semanage port -a -t port_label -p tcp|udp PORTNUMBER
View changes to policy: semanage port -l -C
Delete/Modify port label: semanage port -d/m -t gopher-port-t -p tcp 71
Verify changes by using semaanage: semanage port -l | grep http
Example: httpd
Try to start up the httpd: systemctl enable --now httpd
Check if SELinux blocking httpd service: sealert -a /var/log/audit/audit.log

NFS clients can mount exported dir:
-	Manually mount the exported directory using mount
-	Persistently mount the directory at boot using /etc/fstab
-	Automount the directory statically on demand using autofs service
Following errors may occur when editing the /etc/fstab file:
-	Storage timeouts may increase if network issues occur
-	Timeouts might increase when NFS Filesystems are not in use
-	Boot time issues may occur if network is down/unreachable
Automounting using nfs-utils
Install the nfs-utils package: dnf install nfs-utils
NFSv3: Remote Procedure Call(RPC) Protocol, requires fileserver that supports NFSv3 connections to run rpcbind service. (client connects to the service using 111 port on NFS server)
-	Query avail exports on NFSv3 server: showmount –exports server
NFSv4: Introduces an export tree that contains paths for service’s exported directories. To view all exported dir, mount root(/) of server’s export tree
-	Mount server:/export mountpoint
-	Ls mountpoint
(The export tree provides browsable paths for all exported dir, but not mount any of them)

Manually Mount Exported NFS Directories
-	Identify NFS export to mount
-	Create local mount point if not exist
-	Reccomended to use /mnt for temporary purposes
Create mount point: mkdir mountpoint
Mount NFS export to access contents: mount -t nfs -o rw,sync server:/export mountpoint

Persistently Mount exported NFS Directories
Add in /etc/fstab: server:/export mountpoint nfs rw,sync 0 0
Obtain NFS Server and mount options from /etc/fstab: mount mountpoint
Unmount NFS export: umount mountpoint
List open file names/processes that is preventing u from umounting exported directory: lsof mountpount


Mount NFS Exports with Automounter
Automounter (autofs) is a service that automatically mounts file systems and NFS exports on demand and automatically umounts them when mounted resources are no longer in use
Adv:
-	Uses equivalent resources to filesystems mounted at boot
-	File system only uses resources when a program is reading/writing open files
-	Idle systems and unmounted use almost no resources
-	By umounting filesystem each time no longer in use, it is protected from unexpected corruption when open
-	/etc/fstab mount storage permanently at boot time, autofs mounts storage on demand
-	If NFS server config includes redundant servers/paths, automounter can get fastest connection each time a new file system is requested
Direct/Indriect Maps
Direct Maps: 
-	Used when u have a specific, absolute path on ur system that u want to map directly to a network share
-	Target directory remains visible even when unmounted
-	Master map always use special indicator /-
# Master file (/etc/auto.master.d/direct.autofs)
/-  /etc/auto.direct
-------------------------------------------------
/-: Tells autofs the entry in next map file will have absolute custom path
/etc/auto.direct: Points to the tracking sheet where u have listed custom paths

# Map file (/etc/auto.direct)
/mnt/docs   -rw,sync   hosta:/shares/docs
--------------------------------------------------
/mnt/docs: This is the exact permanent folder on machine where files will appear
Hosta:/shares/docs: The network server and path where data actually lives

Indirect Maps:
-	Used when u want to group multiple network shares inside a parent directory
-	Subdirectories completely hidden and do not exist on disk unless someone types their name, autofs creates them on the fly and destroys them when unmounted

# Master file (/etc/auto.master.d/indirect.autofs)
/shares  /etc/auto.indirect
-	/shares defines the hallway, gives autofs to control subfolder creation
# Map file (/etc/auto.indirect)
work   -rw,sync   hosta:/shares/work
-	Work is relative name, final path (/shares/work)

# inside /etc/auto.indirect
* -rw,sync   hosta:/shares/&
If user runs cd /shares/bob, the * catches ‘bob’, replaces the & with ‘bob’ and runs off to mount hosta:/shares/bob

How to Activate the Service
Once master maps inside /etc/auto.master.d/ and sub-map files are written, start the engine:
-	Sudo systemctl enable --now autofs
Can do inside /etc/fstab
Serverb:/shares/public /public nfs rw,sync,x-systemd.automount 0 0

Kickstart file (ks.cfg) RHEL installation automation
Section A: (General settings)
-	url: Where installer should download the os packages
-	autopart/clearpart: Auto wipe hard drive and lay down a fresh layout
-	network: Sets up network card config and sets machine hostname
-	timezone/lang
Section B: (Package List)
-	starts with %packages and ends with %end (defines what software should be installed
-	@^graphical-server-environment: The environment group (‘Server with GUI’)
-	@: Installs a standard package group (database tools)
-	Vim-enhanced: Installs a single package by name
-	-hyperv*: Minus sign (-) explicitly excludes a package from being installed to keep the OS clean
Section C: (Custom Automation Scripts)
-	%pre: Runs before the installation begins
-	%post: Runs after the installation finishes. This is widely used by admins to pull down configuration agents, add custom message banners, update security policies before user logs in

3	Steps kickstart workflow
1.	Write file (anaconda-ks.cfg)
a.	Every time manually install RHEL, installer saves choice in a template file at /root/anaconda-ks.cfg (use it as a blueprint)
b.	Use command ksvalidator /path/to/file.cfg to test for syntax mistakes before deploying
2.	Publish the file
a.	Put the ks.cfg answer file somewhere target server can reach during boot. Etc. local web server (http://myserver/ks.cfg)
3.	Boot and Trigger
a.	Boot the target machine using standard RHEL installation media
b.	At the GRUB selection Menu, press C or E to edit kernel arguments
c.	Append inst.ks=<FILE>
d.	Press Ctrl + X to boot
You can also access the PXE boot loader install RHEL 10 by using Tab and adding
inst.ks=http://servera.lab.example.com/kickstart.cfg (Link)

PODMAN
Version: podman -v
Login: podman login <REGISTRY_URL>
Inspect Configured Container Registries: podman info
Podman primarily uses 2 files to store registry configs:
-	/etc/containers/registries.conf file (registry configs)
-	${XDG_RUNTIME_DIR}/containers/auth.json stores credential information (directory specific to current user)
Run container: podman run registry.redhat.io/ubi10 echo ‘Hello World!’
-	Detached container: -d or –detatch (Runs in the background)
-	Container name: --name
List running containers: podman ps (ID,IMAGE_NAME,CMD,TIME,STATUS,EXPOSED PORTS,CONTAINER_NAME)
List all containers (running and stopped): podman ps -a 
Restart container: podman restart nginx
Stop container: podman stop <CONTAINER_ID> [--all] (stop all)
Force stop container: podman kill httpd
Remove stopped container: podman rm <CONTAINER_ID> (force: --force)
Automatically remove container when exits: podman run –rm registry.redhat.io/ubi10 echo ‘Hello World!’
Expose container ports: podman run -p 8080:8080 <Image>
Search for images in a registry: podman search registry.redhat.io/rhel10
Search for UBI: podman search registry.redhat.io/ubi
Fetch container images: podman pull (After pulling image is stored locally)
Get local images: podman images
Inspect image in json: podman image inspect rhel-bootc:latest
Name of container image: “{{.Config.Labels.name}}
Date/time: --format “{{.Created}}”
Description of container image: “{{.Config.Labels.description}}”
Inspect remote image:
-	Pull image first: podman image pull <REPO><IMAGE>
-	Inspect: podman image inspect <IMAGE>
Tag images: podman tag <IMAGE_ID> <CONTAINER_NAME>
Create an Image from a containerfile: 
-	Start with base image: podman image pull registry.redhat.io/ubi/ubi
-	Create actual container image: podman build -t my-httpd -f Containerfile
-	List images in storage: podman image list
-	Run image: podman run my-httpd
Share images thru a registry:
-	Push image to registry: podman image push my-httpd registry.redhat.io/my-httpd
Remove image, untagged and unreferenced parent images from local: podman rmi <ID>
Removes all images from local storage that are without tags and that are not referenced by other images (dangling images): podman image prune

A bootable container image must include the kernel, a boot loader and other operating system components (usually not needed for applications containers)

No need to log in to 50 different servers to run updates:
-	Build (Define OS in Containerfile Build via Podman)
-	Deploy (Push to central registry, Convert to disk format)
-	Manage (Servers pull image layers Transactional auto-updates)
Write standard Containerfile

Deploy, Push completed bootable image to central container repo:
-	Install it on baremetal hardware / VMs, Anaconda installer reads the image via Kickstart automation
-	For Cloud, bootc-image-builder converts that image into standard virtual hard drive formats (.qcow2,.ami,.vmdk)
Manage:
Once server running, root (/) fs is immutable (locked/read-only). Don’t touch the server, update the Containerfile, rebuild it, push to registry

Benefits:
-	Atomic upgrades/Rollbacks: Updates are transactional, system either updates completely or not at all, if update breaks, use bootc to rollback bootloader to point back to the previous working container image layer
-	Zero Infrastructure Drift: root fs locked, hence quick fixes cant be made ruining consistency, hence all servers deployed remains identical
-	Version Control

Application Container vs Bootable Container
Biggest trap when writing a Containerfile in Image Mode is using instructions meant for lightweight apps instead of a full machine
-	In an Application Container: Tell container what process to launch when wake up like ENTRYPOINT, CMD (etc Run the apache web server now) and EXPOSE 80 to tell podman which network port to route into it
-	In a Bootable Container: You do not use ENTRYPOINT, CMD, EXPOSE. This image is destined to become a server
-	Systemd is the master process, Instead of a single script, Containerfile uses standard system commands to enable services and punch holes through the native firewall
Instruction	Traditional Application	RHEL Image Mode
How to run a service	ENTRYPOINT “”	RUN systemctl enable httpd
How to open network ports	Expose 80	RUN Firewall-cmd --add-service=https
Env var	ENV MY_VAR=value	Ignored at boot
User Conf	USER apache	Ignored

COPY: Strictly cp files or dir from local host machine into image exactly as they are. If you copy a compressed .tar.gz archive, it lands in the image as a compressed file
ADD: More powerful and dynamic, it can look at a network URL and download files directly into image layer during build. If u specify local compressed archive, ADD will automatically unpack it into directories on the fly

Test locally
Use podman run,
When u boot a rhel-bootc image inside Podman, RHEL kernel inside image remains completely asleep, systemd wakes up as process ID 1
Log inside it using: podman exec -l ps -ef
U will see
-	Systemd-journald (Handling logs)
-	NetworkManager (Running the network)
-	Sshd (Waiting for connection requests)
-	Httpd (Actively serving webpages)
HOW TO DEPLOY bootc onto live systems
Bare-Metal Deployment: The kickstart shift
-	To install image based OS directly onto physical hardware or vm, use Anaconda Kickstart Workflow
-	Traditional Kickstart: Uses %packages section to pull individual software parts from a repo (.rpm files)
-	Image Mode Kickstart: Completely deletes %packages block and replaces it with ostreecontainer command pointing to ur container registry destination
# Traditional Package Mode
%packages
@^minimal-environment
%end

# New Image Mode Equivalent
ostreecontainer --url=registry.lab.example.com:5000/student/webserver-bootc
-	Base rhel-bootc image has no default user profiles or passwords backed in, you must still add rootpw and user configs in Kickstart file (if not u will be locked out of server upon first boot)

CLOUD & VIRTUALIZATION DEPLOYMENT: bootc-image-builder
If deploying to cloud or VM managers (VMware/KVM), u cant install using kickstart
-	Turn ur container image into a virtual hard drive file
-	Bootc-image-builder does this conversion
Supported output types
-	Qcow2, vmdk, ami, gcd, vhd


Creating a disk image (.qcow2)
Define Users (config.toml)

[[customizations.user]]
name = "user1"
password = "password1"
groups = ["wheel"] # Gives administrator privileges

Trigger the podman builder container
sudo podman run \
  --rm -it --privileged \
  --security-opt label=type:unconfined_t \
  -v ./config.toml:/config.toml \
  -v ./output:/output \
  registry.redhat.io/rhel10/bootc-image-builder \
  --type qcow2 \
  registry.lab.example.com/user/bootc-httpd:latest
Physical virtual disk file will sit in output/qcow2/disk.qcow2
Booting the VM on KVM
Cp disk image into default Linux Virtualization Library path and run a standard deployment wrapper
# 1. Copy disk to local hypervisor storage pool
sudo cp output/qcow2/disk.qcow2 /var/lib/libvirt/images/

# 2. Define and assemble the virtual machine
sudo virt-install \
  --name bootc-webserver \
  --memory 4096 \
  --import --disk /var/lib/libvirt/images/disk.qcow2 \
  --graphics none \
  --osinfo rhel10.0 \
  --noautoconsole --noreboot

# 3. Fire it up!
sudo virsh start bootc-webserver

Verify current system state: bootc status
Upgrade the system (Image mode):
-	Edit the Containerfile in bootc image project directory
-	Rebuild the container image
-	Push the new container image to the registry with same name tag
-	Update the image on the system using the bootc command
-	Reboot the system
Automated updates: bootc-fetch-apply-updates system service and timer unit files check for updates regularly
Systemctl status bootc-fetch.apply-updates.timer
Manual Upgrade: bootc upgrade  Reboot
Rollback: bootc rollback (Next boot)

(/): Image mode uses a technology called composefs combined with OSTree 
-	OSTree: Git for hard drive, snapshots of os inside (/ostree/repo)
-	Composefs: takes snapshots and mounts it as root (read-only)
-	Nobody can run rm -rf
(/etc): System configs live here, to change network settings/passwords
-	Writable
-	When u upgrade OS to new container image ver, runs a 3-way merge
-	If you modified a config file, custom version is safely preserved and carried over to the new OS layout
-	If u never touched a file, new OS image contains updated/more secure default version of that file, it gets updated automatically
-	BG process merge quietly while system shutting down, right before it boots to new version
(/var): shared storage
Dynamic runtime data lives here (logs,user home directories, db, website files)
-	Shared Zone: /etc splits into new copy during upgrade but /var directory shared across all OS deployments on that machine
-	Upgrade Rule: Files inside /var are copied over once during first day machine is built. During future OS upgrades, the image update engine ignores /var



Setting up Partitions
Parted /dev/sdb mklabel msdos
Parted /dev/sdb mkpart primary 1MiB 2049MiB (Primary partition start from X to Y named /dev/sdb1)
Parted /dev/sdb set 1 lvm on
Pvcreate /dev/sdb1 (Initializes newly created partition as a PV)
Vgcreate extra_storage /dev/sdb1 (Creates VG named extra_storage  Adds PV into it)
Lvcreate -L 1GiB -n vol_home extra_storage
Mkdir /user-homes
Mkfs -t xfs /dev/extra_storage/vol_home

Generate SSH Key pair: ssh-keygen
Copy public key to other machine: ssh-copy-id -i <FILEPATH> <USER:MACHINE>
Edit selinux files: /etc/selinux/config
Check selinux enforcement: getenforce

Configure the autofs service to mount the servera.lab.example.com:/user-homes/production5 NFS export on the /localhome/production5 directory on the serverb machine.
echo "/- /etc/auto.production5" > /etc/auto.master.d/production5.autofs
echo “/localhome/production5 -rw servera.lab.example.com:/user-homes/production5” > /etc/auto.production5
systemctl restart autofs

ln <FROM> <TO>

Update the parameters in the gwen parameters
