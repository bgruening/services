# Join the list


To become part of the list of Jupyter services for German researchers you have to
provide a yaml-file with the information listed in the table below. 
You can check out existing yaml-files in the [data](https://github.com/NFDI-Jupyter/services/tree/main/docs/hubs/plugins/data) folder.
In the same folder there is also a [template](https://github.com/NFDI-Jupyter/services/blob/main/docs/hubs/plugins/data/template.yaml) you can base on.
To add a logo of your service provide one in the [assets](https://github.com/NFDI-Jupyter/services/tree/main/docs/hubs/plugins/assets) folder. 
When everything is ready, please add a pull request to the main branch of this [repository](https://github.com/NFDI-Jupyter/services). 
If you need further assistance or have other questions, 
do not hesitate to open an issue.
  
  
## Template
```
# Service name. If too general, should include a provider or project context.
title:
# All service providers
provider:
# Link address to the entrypoint for users
service_url:
# If the service_url is not public available, the requirements to reach it
service_url_requirement:
# Email address where to get help
support:
# Healthcheck URL
health_api_url:
# Service specific documentation for users
documentation_url:
# Roles, domain, NFDI consortia, University, etc.
target_group_open_for:
# Whether any researcher in Germany can use the service (true / false)
restricted:
# How is the login performed; Login-URL
login_process:
features: # What the service offers.
  # version of JupyterHub; classic notebook or lab view?
  version:
  # List of supported programming languages (kernels)
  programming_languages: []
  # List of supported environments (e.g., Python virtualenv) configured as kernels
  environments: []
  # Link to information about the supported environments, if not included under documentation_url
  environments_info:
  # List of pre-installed JupyterHub extensions
  extensions: []
  # List of server-proxy featured applications
  proxy_apps: []
  # Whether it is allowed to install further packages, kernels, extensions (true / false)
  install:
  # Whether there is a folder to share documents with others (true / false)
  shared_folder:
  # Whether files can survive the docker session (true / false)
  persistent_storage:
  # List of other features you find relevant
  misc: []
technicals: # Information about the technology stack used
  # The underlying system below JupyterHub, e.g., HPC, Openstack
  platform:
  # The way Jupyter is installed on the system, e.g., Docker, Kubernetes
  deployment:
  # Link to more (technical) information or a (Git) repository about the deployment or other administration tasks
  deployment_url:
  # List of required attributes, entitlements or memberships for login
  login_attributes: []
  # Where the resources (compute / storage) are located
  hardware_location:
  # List of other technicals you find relevant
  misc: []
resources: # Information about available resources on the instance
  # Default number of Jupyter servers per user
  default_server_user:
  # Maximum number of Jupyter servers per user
  max_server_user:
  # Default number of CPU for a Jupyter server
  default_cpu:
  # Maximum number of CPU for a Jupyter server
  max_cpu:
  # Guarantied total number of CPUs for the whole Jupyterhub instance
  total_cpu:
  # Scaled total number of CPUs for the whole JupyterHub instance. Normally not exclusive, depends on other factors.
  burst_total_cpu:
  # Default CPU time for a Jupyter server
  default_cpu_time:
  # Maximum CPU time for a Jupyter server
  max_cpu_time:
  # Default amount of memory for a Jupyter server
  default_memory:
  # Maximum amount of memory for a Jupyter server
  max_memory:
  # Guarantied total amount of memory for the whole Jupyterhub instance
  total_memory:
  # Scaled total amount of memory for the whole JupyterHub instance. Normally not exclusive, depends on other factors.
  burst_total_memory:
  # Default number of GPU for a Jupyter server
  default_gpu:
  # Maximum number of GPU for a Jupyter server
  max_gpu:
  # Guarantied total number of GPUs for the whole Jupyterhub instance
  total_gpu:
  # Scaled total number of GPUs for the JupyterHub instance. Normally not exclusive, depends on other factors.
  burst_total_gpu:
  # Default amount of disk space (maybe temporary) for a Jupyter server
  default_disk:
  # Maximum amount of disk space (maybe temporary) for a Jupyter server
  max_disk:
  # Default amount of disk space that survives a Jupyter session
  default_persistent_disk:
  # Maximum amount of disk space that survives a Jupyter session
  max_persistent_disk:
  # Guarantied total amount of disk space for the whole Jupyterhub instance
  total_disk:
  # Scaled total amount of disk space for the whole JupyterHub instance. Normally not exclusive, depends on other factors.
  burst_total_disk:
usage: # Statistical information about the Jupyterhub instance
  # Average number of Jupyter sessions per day
  average_daily_sessions:
```