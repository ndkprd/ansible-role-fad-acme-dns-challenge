# ansible-fortiadc-acme-dns-01-challenge

## Description

[Ansible](https://www.ansible.com) role to create [Let's Encrypt](https://letsencrypt.org/docs/client-options/) SSL certificate using DNS01 challenge, using [Fortinet's FortiADC](https://www.fortinet.com/products/application-delivery-controller/fortiadc) global-dns-server.

### What does it do?

1. Create private key and CSR;
2. Do ACME DNS-01 challenge;
3. Add TXT record to FortiADC;
4. Verify and pull all the certificate files into localhost (at this point you should be able to use the certificate normally);
5. (optional) Zip it and send it to your email.

## Usage

### Playbook Example

```yaml
---

- name: Update/create FortiADC GLB Resources.
  hosts: all
  become: false
  gather_facts: false
  connection: local
  vars:
    ansible_user: ndkprd
    acme_domain: devops.ndkprd.com
    acme_account_email: "contact@ndkprd.com"
    acme_files_dir: "{{ acme_cert_files_dir }}/wildcard.{{ acme_domain }}"
    acme_remaining_days: 30
    acme_server_dir: "https://acme-staging-v02.api.letsencrypt.org/directory"
    acme_collect_mail:
      enabled: true
      smtp_server: 10.1.54.10
      smtp_port: 25
      recipient: "andhika.pradana@asdp.id"
      sender: "ansible@asdp.id"
    acme_cert_files_dir: "/home/{{ ansible_user }}/letsencrypt"
    acme_cert_country_id: ID
    acme_cert_org_name: ndkprd.com
    fad_zone_config:
      dns_policy: "DEFAULT_DNS_POLICY"
      ttl: "86400"
      negative_ttl: "3600"
      primary_ns_ipv4: "103.219.197.41"
      primary_ns_ipv6: "::"
      primary_ns_name: "ns2"
      responsible_mail: "it.dctn.asdp.id."
      allow_transfer: ""

  roles:
    - ../../ansible-role-fad-acme-dns-01

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


