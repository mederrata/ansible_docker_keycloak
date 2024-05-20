Anisble Docker Keycloak
=========

This role deploys a dockered keyclock behind a locally installed nginx reverse proxy. For presistent state, please enable postgres.

Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here. For instance, if the role uses the EC2 module, it may be a good idea to mention in this section that the boto package is required.


* Open Ports
	- 8080 (TCP HTTP Keycloak) https://hub.docker.com/r/jboss/keycloak
	- 8443 (TCP HTTPS Keycloak)
	- 9990 (TCP HTTP Mangement Keycloak)
	- 9993 (TCP HTTPS Mangement Keycloak)
	- 80 Certbot (TCP HTTP Certbot) https://certbot.eff.org/docs/using.html
	- 443 Certbot (TCP HTTPS Certbot) 
	

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.


| Status Key | Description |
|----------|-------------|
| required | Playbook must override default |
| unused | Variable does nothing |
| optional | Default Acceptable |



| Role Variable | Default | Status |
|--------------|---------|-------|
| `keycloak_domain_name` | "example.com" | required |
| `keycloak_registry_name` | "quay.io/keycloak/keycloak" | optional |
| `keycloak_docker_image` | "{{ keycloak_registry_name }}:{{  keycloak_version }}" | optional |
| `keycloak_https_bind_port` |  8442 | unused |
| `keycloak_http_bind_port` | 8080 | unused |
| `keycloak_version` | 15.1.0 | optional |
| `keycloak_admin` | admin | optional |
| `keycloak_admin_password` | '' | required |
| `keycloak_realm_name` | '' | unused |
| `keycloak_container_extra_arguments` | [] | optional |
| `keycloak_systemd_path` | "/etc/systemd/system" | optional |
| `keycloak_realm_export_json` | "" | unused  |
| `keycloak_install_directory` | "/keycloak" | optional |
| `keycloak_docker_network` | keycloak-network | optional |
| `keycloak_user_username` | keycloak | optional |
| `keycloak_user_uid` | 995 | optional |
| `keycloak_user_gid` | 995 | optional |
| `keycloak_postgres_enabled` | false | optional |
| `keycloak_postgres_base_path` | "{{ keycloak_install_directory }}/postgres" | optional |
| `keycloak_postgres_data_path` | "{{ keycloak_postgres_base_path }}/data" | optional  |
| `keycloak_postgres_docker_image` | "postgres:14.1-alpine" | optional |
| `keycloak_postgres_container_postgres_bind_port` | 5432 | optional |
| `keycloak_postgres_container_extra_arguments` | [] | optional |
| `keycloak_postgres_connection_username` | "" | required |
| `keycloak_postgres_connection_password` | "" | required |
| `letsencrypt_certbot_enabled` | false | optional |
| `keycloak_postgres_db_name` | "" | optional |
| `letsencrypt_certbot_admin_email` | "example@example.com" | optional  |
| `letsencrypt_certbot_create_if_missing` | true | unused |
| `letsencrypt_certbot_create_standalone_stop_services` | [] | unused |
| `nginx_default_config_directory` | /etc/nginx/conf.d/ | unused |

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

 * hispanico.nginx-revproxy 
    - Install: `ansible-galaxy install hispanico.nginx_revproxy`
    - Links:
    	+ Github: https://github.com/hispanico/ansible-nginx-revproxy
    	+ Ansible Galaxy: https://galaxy.ansible.com/hispanico/nginx-revproxy
 * ansible-docker-keycloak
    - Install: `ansible-galaxy install git+https://github.com/hungrymonkey/ansible-docker-keycloak.git`

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

### Files
- playbook.yml

	```
	- hosts: keycloak_server
	  become: true
	  pre_tasks:
	    - name: update apt cache
	      apt: update_cache=true cache_valid_time=600
	      changed_when: false
	      when: ansible_os_family == 'Debian'
	  roles:
	    - ansible-docker-keycloak
	  vars:
	    keycloak_admin_password: keycloakadminpassword
	    letsencrypt_certbot_admin_email: email@example.com
	    letsencrypt_certbot_enabled: true
	    keycloak_domain_name: keycloak.domain.com
	    ansible_python_interpreter: /usr/bin/python3
	    keycloak_postgres_enabled: true
	    keycloak_postgres_connection_username: "keycloak"
	    keycloak_postgres_connection_password: "secret-keycloak-password"
	    keycloak_postgres_db_name: "keycloak-postgres"
 	```
- inventory/hosts
 
	```
	[keycloak_server]
	keycloak.domain.com ansible_user=ubuntu become=true become_user=root
	```

### Commands

#### Deploy

```
ansible-playbook -i inventory/hosts playbook.yml
```

TODO
----
- Implement Unsed Role Variables
- Document tags
	+ setup-all
	+ setup-keycloak
	+ setup-postgres
	+ setup-nginx
- Implement and Document Handlers
- Let's Encrypt has a 25/day limit. https://letsencrypt.org/docs/rate-limits/
   + Implement Staging Environment

License
-------

GPLv3

Author Information
------------------

This role is in development. Basic funtionality works.

Links
-----
https://quay.io/repository/keycloak/keycloak?tab=info
https://www.keycloak.org/getting-started/getting-started-docker
