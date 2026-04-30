# OpenShift Configuration Playbook

This repositories provide examples for OpenShift configuration using Ansible.

Some of the playbooks here support input variables while some will just provide static/hardcoded configurations.
You should use Ansible Automation Platform (AAP) Job Template survey or extra vars for the dynamic inputs.

## Authentication ##

In Ansible Automation Platform, we can use built-in Credential Type: OpenShift or Kubernetes API Bearer Token.
The token can be viewed from OpenShift Console. Use the 'Copy login command' page to get the token.

```yaml
endpoint: 'https://api.yourdomain:6443'
breartoken: 'pasteyourtoken'
```

Alternatively, you can create a kubeconfig custom credential type.
You will need to get your kubeconfig data string.

Input configuration
```yaml
fields:
  - id: kube_config
    type: string
    label: kubeconfig
    secret: true
    multiline: true
```

Injector configuration
```yaml
env:
  K8S_AUTH_KUBECONFIG: '{{ tower.filename.kubeconfig }}'
file:
  template.kubeconfig: '{{ kube_config }}'
```

## Execution Environment ##

The ansible collection module **redhat.openshift** is present on Red Hat's default supported EE image.

Example image 
```
registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel9:latest
```

## Playbooks List ##

| File Name | Purpose |
|--|--|
| ocp-app/deploy_service_web.yaml | Create Service for Web (TCP/8080) |
| ocp-rbac/rbac_role_static.yaml | Create 2 hardcoded RoleBinding |
| ocp-rbac/rbac_role.yaml | Create RoleBinding |
| ocp-rbac/rbac_user_group.yaml | Create User Groups from a list |
| ocp-virtualization/deploy_namespace.yaml | New Namespace Configuration |
| ocp-virtualization/deploy_vm_host_passthrough | Create a VM with host-passthrough CPU |

Example Job Template Survey for VM Deployment

![VM Launch](images/vm_launch_prompt.png)


## Workflow Example ##

Simple workflow setup:
1. Project Sync
2. Create Namespace
3. Create RoleBinding for the Namespace
4. Create VM to test deployment

![Project Setup](images/project_setup_workflow.png)

## AAP Operator Deployment ##

This is a simple AAP 2.6 Custom Resource for AAP deployment using OpenShift Operator.

```yaml
---
apiVersion: aap.ansible.com/v1alpha1
kind: AnsibleAutomationPlatform
metadata:
  name: aap
  namespace: aap
spec:
  # Platform
  image_pull_policy: IfNotPresent

  database:
    resource_requirements:
      requests:
        cpu: 200m
        memory: 512Mi
    storage_requirements:
      requests:
        storage: 100Gi

  controller:
    disabled: false

  eda:
    disabled: false

  hub:
    disabled: false
    storage_type: file
    file_storage_storage_class: rwx-file-storage-name
    file_storage_size: 100Gi
```