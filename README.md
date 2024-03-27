# ansible-fortiadc-acme-dns-01-challenge

## Description

[Ansible](https://www.ansible.com) role to create [Let's Encrypt](https://letsencrypt.org/docs/client-options/) SSL certificate using DNS01 challenge, using [Fortinet's FortiADC](https://www.fortinet.com/products/application-delivery-controller/fortiadc) global-dns-server.

### What does it do?

1. Create private key and CSR;
2. Do ACME DNS-01 challenge;
3. Add TXT record to FortiADC;
4. Verify and pull all the certificate files into localhost (at this point you should be able to use the certificate normally);
5. Zip it and send it to your email.

## Usage

### Install Role

#### Create Requirement Files

```yaml
---
# ./requirements.yaml

- src: https://github.com/ndkprd/ansible-role-fortiadc-acme-dns-01.git
  scm: git
  version: main
```

#### Install Role from Requirement

```bash
ansible-galaxy install --role-file requirements.yaml --roles-path ./roles --force
```

### Playbook Example

```yaml
# ./playbook.yaml

- name: Do ACME DNS-01 challenge against FortiADC.
  hosts: fortiadc
  become: false
  gather_facts: false
  vars_files:
    - fortiadc-acme-dns-01-vars.yaml

  roles:
    - ansible-role-fortiadc-acme-dns-01
```

### Needed Variables Example

```yaml
base_domain: devops.ndkprd.com
  
acme_server_dir: "https://acme-staging-v02.api.letsencrypt.org/directory" # ACME directory to be used
acme_remaining_days: 30 # if the current cert expired days is less than this, then renew
acme_account_email: "contact@ndkprd.com"

cert_files_dir: /home/{{ ansible_user }}/letsencrypt # server dir to save the cert files
cert_country_id: ID
cert_org_name: ndkprd.com

collect_mail:
  smtp_server: 10.10.10.10
  smtp_port: 25
  recipient: contact@ndkprd.com
  sender: ansible@ndkprd.com
```

### Hosts File Example
```ini
[fortiadc]
fad1 ansible_host=fad1.infra.ndkprd.com fad_apitoken=mysupersecrettoken1 fad_vdom=root
fad2 ansible_host=fad2.infra.ndkprd.com fad_apitoken=mysupersecrettoken2 fad_vdom=root
fad3 ansible_host=fad3.infra.ndkprd.com fad_apitoken=mysupersecrettoken3 fad_vdom=root

[fortiadc:vars]
fad_http_port=80
fad_https_port=443
```

## License

MIT, please use at your own risk.


