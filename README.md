# ansible-fortiadc-acme-dns-01-challenge

## Introduction

Frankenstein [Ansible](https://www.ansible.com) playbook to create [Let's Encrypt](https://letsencrypt.org/docs/client-options/) SSL certificate using DNS01 challenge, using [Fortinet's FortiADC](https://www.fortinet.com/products/application-delivery-controller/fortiadc) global-dns-server.

## Requirement

My Ansible env version:
```
ansible [core 2.14.11]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/root/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.11/site-packages/ansible
  ansible collection location = /root/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.11.8 (main, Feb  7 2024, 00:00:00) [GCC 13.2.1 20231011 (Red Hat 13.2.1-4)] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```

## Directory Tree

```
├── hosts
├── playbook.yaml
├── README.md
└── roles
    └── fortiadc-dns-01-acme
        ├── defaults
        ├── handler
        ├── meta
        ├── tasks
        │   └── main.yaml
        ├── templates
        └── vars
            └── main.yaml
```

## How to use

1. Clone this repo;
2. `cd` inside the repo;
3. Rename `./roles/fortiadc-dns-01-acme/vars/main.example.yaml` to `./roles/fortiadc-dns-01-acme/vars/main.yaml`;
4. Modify the file as you need (put your FortiADC REST API Key and global-dns-server zone name here);
5. Rename `./hosts.example` file to `./hosts`;
6. Modify the file as you need (put your base domain and certificate details here);
5. Run the `playbook.yaml` file:

```bash
ansible-playbook -i hosts playbook.yaml
```

## How does this work?

1. It prepare the directory in target hosts' `/home/{{ ansible_user }}/letsencrypt/`;
2. It create private key using `community.crypto.openssl_privatekey` module: it create 2 private key--one used for account key, another used to sign the CSR;
3. It create the CSR using  `community.crypto.openssl_csr` module;
4. It uses `community.crypto.acme_certificate` module to request certificate using ACME DNS01 challenge;
5. It add  `_acme-challenge` TXT entry in (existing and defined) DNS zone in FortiADC using `ansible.builtin.uri` (skipped if the acme_certificate module didn't create new cert;
6. Wait 30 seconds;
6. Revalidate and download all the CRT (the cert, intermediate, and fullchain) files.

At this point we could use the files as if it's a normal certificate.


