Role Deploy Namespace
=========

Ansible role to create new namespaces. Mainly written to run on OCP via Ansible <= 2.4.x

Will rewrite role for Ansible >= 2.7.x to utilize the k8s module.

Plus, the oc command line tool is neat.

Requirements
------------

Create a service account and bind it to the cluster-admin cluster role.

`
oc create sa role_depnamespace -n project_name
`

`
oc adm policy add-cluster-role-to-user cluster-admin -z role_depnamespace -n project_name 
`

Query the service account for its token.

`
oc describe sa role_depnamespace -n project_name
`

`
oc describe secret <role_depnamespace_token>
`

Now create the kube config and deploy it to the role to be deployed.

`
oc login --token <token_obtained_prior> https://master-api-url.domain.com:PORT --config /tmp/config-nonprod 
`

`
mv /tmp/config-nonprod role_deploy_namespace/files/env_nonprod
`

`
ansible-vault encrypt --ask-vault-pass role_deploy_namespace/files/env_nonprod 
`

Role Variables
--------------

N/A

Dependencies
------------

This role depends on ansible_local facts and should be set prior to running role. [Example.](https://github.com/canit00/openshift/blob/master/2018/ansible/playbooks/pb_set_cluster_facts.yaml)

    $ cat /etc/ansible/facts.d/cluster.fact
    # BEGIN ANSIBLE MANAGED BLOCK
    [env]
    is=nonprod
    node=master
    # END ANSIBLE MANAGED BLOCK

Example Playbook
----------------

To deploy the playbook run the following:
```
ansible-playbook -v -l <hostname> pb_deploy_namespace.yaml --ask-vault-pass
```

If you only want to set or modify an existing limits skip entering a project description when prompt.
```
ansible-playbook -v -l <hostname> pb_deploy_namespace.yaml --ask-vault-pass -t limit
```
    ---
    # Ansible role to create new namespaces in OpenShift
    # Right now known to work on Ansible version 2.4.x
    # Example: ansible-playbook -v -l <hostname> pb_deploy_namespace.yaml --ask-vault-pass
    # To only set limits (skip entering a project description): ansible-playbook -v pb_deploy_namespace.yaml --ask-vault-pass -t limit
    - hosts: all
      gather_facts: yes
    
      vars_prompt:
        - name: project_name
          prompt: "Enter project name; e.g: omms-pe-stage"
          private: no
    
        - name: project_description
          prompt: "Enter project description; e.g: OMMS Stage Project"
          private: no
    
        - name: mem
          prompt: "Enter # of memory in Gigs; e.g: 2"
          private: no
    
        - name: cpu
          prompt: "Enter # of CPUs; e.g: 4"
          private: no
     
      roles:
      - { role: role_deploy_namespace }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
