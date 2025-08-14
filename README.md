# Ansible OpenSMTPd

This role installs and configures [OpenSMTPd](https://opensmtpd.org/)
SMTP server.

This role is under heavy development. Probably you should NOT USE IT.

It has been tested for the following systems:

* Linux
  * Debian Bookworm
  * Debian Trixie
  * Ubuntu 24.04 (Noble Numbat)
* OpenBSD 7.6
* NetBSD 10.1

## Default Variables

```yaml
# example
opensmtpd_user_list:
  - username: secops
    password: 4GNADCBiQKBg

  - username: odmin
    password: somerandomshit
```

For other variables, look at defaults/mail.yml


## Example Playbook

That's exactly how playbook-smtp.yml looks like:

```yaml
    - hosts: smtp
      roles:
        - role: sv0-smtpd
```

## Usage

### Add new or modify existing SMTP account

Add credentials to `group_vars/smtp` in inventory as
described in [Role Variables](#role-variables) section.

### Run playbook



### Test mail sending

Test if newly added SMTP account works.
It could be done with **swaks** utility, for example:

    swaks --tls --server smtp.svyrydiuk.eu:587 \
      --from secops@svyrydiuk.eu \
      --to slavik@svyrydiuk.eu \
      --auth-user secops@svyrydiuk.eu \
      --auth-password 4GNADCBiQKBg \
      --auth LOGIN

## Notes

### Certificates

### DKIM

#### RSA

Create private key for RSA dkim siganture:

    openssl genrsa -out smtp-dkim-rsa-private.key 1024

    openssl rsa -in smtp-dkim-rsa-private.key -pubout \
      | sed '1s/.*/v=DKIM1;p=/;:nl;${s/-----.*//;q;};N;s/\n//g;b nl;'

Output add to your DNS zone as TXT record:

v=DKIM1;p=MIGfMA0GCSqGSIb3DQEBAQUAA4GNADitkJzDHBYmUPOdCPFSsTH/DPV2o7bYaDaGAVJcuuRP2CVa7v47miwDSliUX2X3vgY2KqNrj7LrzKYPRnHEW393ABP/NrkbIUKSGrT7Lw3YGhov2Nu9hUG4OSVDKI12325r9M0xjdZl1pYqTwzmu/X+0GJIQIDAQAB


### Configure DKIM, SPF and other DNS records

Get public key value for DKIM DNS record:

    openssl rsa -in files/private.key -pubout | \
        sed '1s/.*/v=DKIM1;p=/;:nl;${s/-----.*//;q;};N;s/\n//g;b nl;'


Add TXT DNS records:

    mail._domainkey.smtp.svyrydiuk.eu. IN TXT "v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEB3bLWqosrT9F3hHatloETHclamCL/cMUTppljoS7/MEfiWhplr1F9iiaFUhbO/+iEbnzOGtF0YggSsYFqwx6/3alcT0PAGwZaLRr5PmrMXckM0SCwWZfbyFIGM6hAwkwIDAQAB"


## Molecule tests

Run `make test` within role directory.
