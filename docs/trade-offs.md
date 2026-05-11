

**Image Stratergy**

Image tagging with the a correct versioning is always preferred rather that tagging the image with the :latest

:latest  i have used the latest image tagging for easier troubleshooting. 

in a production environment **Immutability** in production have to make sure same version is being deployed.


**Branching Stratergy**

I would use the Gitflow Branching stratergy, simple and elegent for secure, Auditable branching with given CICD pipeline for automatically inintiate the pipeline for Deployemtns. 

main >>>> Production branch
develop >>> Developers branch for dev/QA local environments 
            feature/issue-fixes >> devs working on this branch
Release >>> Release branch for release the deployment for each environment

Environment Flow :   DEV >> QA >> INT >> UAT >> Prod

**Infrastructure**

I chose to comment  the PersistentVolumeClaim (PVC) for Redis to ensure the stack could deploy in a standard  environment without specific storage class dependencies.

Result: 
This improved portability for the assessment. 

In a production environment, I would implement a permanant disk type along with the cloud platform as of the choice (AWS EBS or Azure Disk) to ensure  Data persistency. 



**Security Improvements**

**Docker File Optimization**

Non-root user :

Cuurently the containers are running with the high privilages because this is an assignment and due to limited tim.

in a production environment i would choose a non-root user to execute the containers to increase the scuruty and reduce the security impacts. 

**Network Isolation**

Control the traffic only via selected ports. 
In kubernetes implemete good NetworkPolicy to control the traffic to redis cache. 


**Observability**

I would monitor the endpoints and logs pod status via Promethus Grafana.

and Select good Promqueries to monitor the system.

Latency times, Error Rates in Real time. 

**Secret Management**

In a realtime production environment i qwould choose "Hashicorp vault" to store our secret key, ssh keys, If related to pipelines.

choice of cloud there are cloud services as well to use, AWS key manager, AZURE, OCI Key vault given the choice. 


