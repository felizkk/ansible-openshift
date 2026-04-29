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

## Workflow Example ##

TBD

