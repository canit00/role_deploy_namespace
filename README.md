Role Name
=========

Ansible role to create new namespaces. Mainly written to run on OCP via Ansible <= 2.4.x

Will rewrite role for Ansible >= 2.7.x to utilize the k8s module.

Requirements
------------

Create a service account and bind it to the cluster-admin cluster role.

`
oc create sa role_addnamespace -n project_name
oc adm policy add-cluster-role-to-user cluster-admin -z role_addnamespace -n project_name 
`

Query the service account for its token.

`
oc describe sa role_addnamespace -n project_name
oc describe secret <role_addnamspace_token>
`

Now create the kube config and deploy it to the role to be deployed.

`
oc login --token <token_obtained_prior> https://master-api-url.domain.com:PORT --config /tmp/config-nonprod 
mv /tmp/config-nonprod role_add_namespace/files/env_nonprod
ansible-vault encrypt --ask-vault-pass role_add_namespace/files/env_nonprod 
`

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

This role depends on ansible_local facts and should be set prior to running role.


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

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
