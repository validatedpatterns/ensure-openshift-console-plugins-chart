# ensure-openshift-console-plugins

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square)

A Helm chart to ensure openshift console plugins

This chart is used to activate OpenShift console plugins

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| configJob.activeDeadlineSeconds | int | `3600` |  |
| configJob.configTimeout | int | `1800` |  |
| configJob.image | string | `"quay.io/hybridcloudpatterns/imperative-container:v1"` |  |
| configJob.imagePullPolicy | string | `"Always"` |  |
| configJob.schedule | string | `"*/5 * * * *"` |  |
| ensureConsolePlugins | list | `[]` |  |
| ensurePluginsPlaybook | string | `"---\n- name: Ensure plugins for OpenShift console\n  become: false\n  connection: local\n  hosts: localhost\n  gather_facts: false\n  vars:\n    kubeconfig: \"{{ lookup('env', 'KUBECONFIG') }}\"\n    ensure_plugins: []\n  tasks:\n    - name: Retrieve current console object\n      kubernetes.core.k8s_info:\n        api_version: operator.openshift.io/v1\n        kind: Console\n      register: console_obj\n      until: console_obj.resources | length == 1\n      retries: 20\n      delay: 5\n\n    - name: Retrieve current plugins\n      ansible.builtin.set_fact:\n        current_plugins: \"{{ console_obj.resources[0].spec.plugins }}\"\n\n    - name: Calculate new plugins\n      ansible.builtin.set_fact:\n        new_plugins: \"{{ (current_plugins + ensure_plugins) | unique() }}\"\n\n    - name: Ensure the new plugin list\n      kubernetes.core.k8s:\n        definition: \"{{ console_obj.resources[0] | combine({'spec': { 'plugins': new_plugins }}) }}\"\n        state: present\n      when: new_plugins != current_plugins"` |  |
| rbac.roleBindings[0].createBinding | bool | `true` |  |
| rbac.roleBindings[0].name | string | `"manage-console-plugins"` |  |
| rbac.roleBindings[0].roleRef.kind | string | `"ClusterRole"` |  |
| rbac.roleBindings[0].roleRef.name | string | `"manage-console-plugins"` |  |
| rbac.roleBindings[0].scope.cluster | bool | `true` |  |
| rbac.roleBindings[0].subjects.apiGroup | string | `""` |  |
| rbac.roleBindings[0].subjects.kind | string | `"ServiceAccount"` |  |
| rbac.roleBindings[0].subjects.name | string | `"console-activate-sa"` |  |
| rbac.roleBindings[0].subjects.namespace | string | `"openshift-console"` |  |
| rbac.roles[0].apiGroups[0] | string | `"operator.openshift.io/v1"` |  |
| rbac.roles[0].createRole | bool | `true` |  |
| rbac.roles[0].name | string | `"manage-console-plugins"` |  |
| rbac.roles[0].resources[0] | string | `"consoles"` |  |
| rbac.roles[0].scope.cluster | bool | `true` |  |
| rbac.roles[0].verbs[0] | string | `"*"` |  |
| serviceAccountName | string | `"console-activate-sa"` |  |
| serviceAccountNamespace | string | `"openshift-console"` |  |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.14.2](https://github.com/norwoodj/helm-docs/releases/v1.14.2)
