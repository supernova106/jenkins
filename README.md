Jenkins
=========

Ansible role to bootstrap Jenkins Master or Slave machine

Development
------------

- folk the git repository
- create a pull request to master from develop branch

**Vagrant**

```
cd tests/
vagrant up
```
- go to http://127.0.0.1:8081, you should be able to see Jenkins Master
- cleanup

```
vagrant destroy -f
```

Requirements
------------

- download this cookbook to your `roles/` directory, and include `jenkins` to Jenkins VMs
- update your inventory/hosts with Ubuntu boxes
- to be installed with `nginx` ansible role as a reverse proxy and to support TLS.
- Jenkins Master also support https://docs.openstack.org/infra/jenkins-job-builder/

Role Variables
--------------

- Jenkins http port: `jenkins_http_port`, by default is 8080

**Plugins**

- list of plugins: `jenkins_plugins: []`

```
jenkins_plugins:
  - maven-plugin
  - javadoc
  - junit
  - mailer
  - token-macro
  - workflow-aggregator
  - ldap
```

**Custom files**

- custom files `jenkins_custom_files` to include to `{{jenkins_home}}/`

```
vars:
  jenkins_include_custom_files: true
  jenkins_custom_files:
    - org.jenkinsci.main.modules.sshd.SSHD.xml
```

**Upgrade**
- upgrade Jenkins Master by specifying `jenkins_version: 2.75`

**Backup**

- awscli is installed to work with S3

**Credentials**

- where to find admin password, which is generated in the first time

```
jenkins_admin_password_file: "{{ jenkins_home }}/secrets/initialAdminPassword"
```

- In order to automatically join Jenkins master, Jenkins Slave need a credential. By default username is `admin`
- specify the local password file: `jenkins_slave_password_file` or it will fetch from Jenkins master. The file is removed when Jenkins Slave is started
- if use are using **Active Directory**, create one credential for Jenkins Slave on AD, another one for Jenkins Master CLI to install plugins

```
jenkins_admin_username:
jenkins_admin_password:

jenkins_slave_username:
jenkins_slave_password:
```

**Reverse Proxy**

- by default, `jenkins_install_reverse_proxy: false` is set to false, jenkins_url is on default port `8080`
- you can easily setup nginx as reverse-proxy
- if you want to use built-in reverse-proxy (https://traefik.io/), you can set

```
jenkins_install_reverse_proxy: true
jenkins_reverse_proxy: "traefik"
```

- specify TSL certificate on the Jenkins master machine, if Jenkins is using HTTPS, the port will be automatically configured to 443

```
traefik_tls_cert_file: ""
traefik_tls_key_file: ""
```

Dependencies
------------

- Ubuntu 14.04+

Example Playbook
----------------

**bootstrap master**

```
- hosts: jenkins_master
  become: true
  vars:
    jenkins_plugins:
      - maven-plugin
      - javadoc
      - junit
      - mailer
      - token-macro
      - workflow-aggregator
      - ldap
  roles:
    - jenkins
```

- `ansible-playbook jenkins-playbook.yml --limit "jenkins_master"`

**bootstrap slave**

```
- hosts: jenkins_slave
  become: true
  vars:
    is_slave: true
  roles:
    - jenkins
```

- `ansible-playbook jenkins-playbook.yml --limit "jenkins_slave"`

License
-------

BSD

Author Information
------------------

Bean Nguyen
