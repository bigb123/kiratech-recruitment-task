# kiratech-recruitment-task
Ansible, gcp and Docker swarm setup for Kiratech recruitment task.

The backend infrastructure built on Google Cloud Platform with Ansible, Docker Swarm and 
Github Actions as a CI/CD pipeline.

The setup creates two CentOS instances for Docker Swarm Nodes, one load balancer for them 
and one CentOS instance for the Docker Swarm Master. The setup uses Ansible Playbooks to 
provision infrastructure on the cloud and configure each instance. 

# The Cloud

Cloud infrastructure configuration can be found in `gce-instances.yml` and `gce-lb.yml` 
Ansible playbooks.

It is required that the public ssh key will be added to the metadata of the GCP Compute 
Engine Metadata to the 'SSH Keys' section. The username has to be "ansible-service-account".

# The Docker backend

Docker is being configured with roles in `roles/` directory.

# The CI/CD

Linting, building and deployment is being handled by Github Actions. For proper
operation the following Github secrets are required to be populated prior to 
execution:

- GCP_CREDENTIALS_FILE - json credentials file with GCP Service Account access
- GCP_SSH_KEY_FOR_ANSIBLE - private ssh key to let Ansible log in to CentOS 
    instances via ssh. The public key that pairs with this key has to be added
    to the Metadata 'SSH Keys' section in GCP Compute Engine.

# The problem with updating GCP infrastructure

While it's possible to create the GCP infrastructure, configure Docker Swarm and
prepare all the backend there's a problem with infrastructure update.

The `gcp_compute_backend_service` in `gce-lb.yml` file requires fingerprint of the
backend from the GCP service. While mit is possible to deliver this parameter
via GCP REST Api, it looks like it's not implemented on the Ansible side and 
the execution of the Backend update returns GCP error that fingerprint of
this resource is missing. 

The workaround is to comment out everything that is in the "create a backend service" 
section and that depends on it (everything below this module).

# The inspiration of the Ansible usage

The inspiration how to use Ansible with Google Cloud comes from the Google 
demo project https://github.com/GoogleCloudPlatform/compute-video-demo-ansible. 
