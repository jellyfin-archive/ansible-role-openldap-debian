# Ansible Role: Jellyfin

[![Build Status](https://travis-ci.com/TiedToAStar/ansible-role-openldap-debian.svg?branch=master)](https://travis-ci.com/TiedToAStar/ansible-role-openldap-debian)

An Ansible Role that installs and configures openldap on Debian and Ubuntu based systems.

The role is based off of these administrative guides:
* https://help.ubuntu.com/lts/serverguide/openldap-server.html#openldap-server-installation
* https://www.adimian.com/blog/2014/10/how-to-enable-memberof-using-openldap/

## Requirements

None.

## Role Variables

* `openldap_public`: opens a whole in your firewall to allow traffic through the configured ldap port, defaults to `false`
* `openldap_FQDN`: Fully Qualified Domain Name of the server i.e. `www.example.com`
* `openldap_domain`: the top node of the ldap i.e. `dc=example,dc=com`
* `openldap_dc`: Domain Component i.e. `example`
* `openldap_o`: i.e. `example com`
* `openldap_rootpw`: admin password to administer the ldap. Access ldap via commands: `ldapadd -x -D cn=admin,dc=example,dc=com -w <openldap_rootpw> -f add_nodes.ldif`
* `openldap_ous`: List of organizational unites that will be created for you by the role
* `openldap_groups`: List of groups that will be created by the role, a list of attributes can be specified
    One limitation of this role is that currently only one user can be added to a group during the creation of the group. 
* `openldap_users`: List of users that will be created by the role, userPassword should be specified, objectClass, dn, and attributes
* `openldap_is_setup`: Set to `true` if you're skipping right to the group and user creation
* `openldap_user`: specify the process / file owner
* `openldap_group`: specify the process /file owner group

# Dependencies

None

# Example Playbook

```yaml
- name: Install ldap
  hosts: all
  become: true
  roles:
    - role: tiedtoastar.openldap
      openldap_is_setup: false
      openldap_FQDN: example.com
      openldap_domain: dc=example,dc=com
      openldap_dc: example
      openldap_o: example com
      openldap_rootpw: ChangeMe
      openldap_user: ansible
      openldap_group: ansible
      openldap_groups:
      - jellyfinusers:
        attributes:
          description: Jellyfin users
          member: uid=jellyfinadmin,ou=people,{{ openldap_domain}}
      - webadmins:
        attributes:
          description: admins that are both machines accounts and people
          member: uid=jellyfinadmin,ou=people,{{ openldap_domain }}
      openldap_users:
      - dn: uid=jellyfin-ldap-service,ou=machineaccounts,{{ openldap_domain}}
        objectClass:
        - inetOrgPerson
        - simpleSecurityObject
        userPassword: ChangeMe 
        attributes:
          cn: jellyfin-ldap-service
          sn: jellyfin-ldap-service
          description: accounts used to connect jellyfin and the ldap for user authentication
      - dn: uid=jellyfinadmin,ou=people,{{ openldap_domain}}
        objectClass:
        - inetOrgPerson
        userPassword: ChangeMe
        attributes:
          cn: jellyfinadmin
          sn: jellyfinadmin
          description: account used to administrate the jellyfin web application

```

## License

MIT

## Author Information

TiedToAStar

## Want to help?

If you find this project useful, please consider a donation to the following Monero address: 47q3TVnd79QcMLqFE2HJC5HTWDadUXtMDVavERPfeT3xFiBeqQQX6knBNALTz4aciC6pSbnLoMCHXXsQDCPV1BT7TqoqZxW

Haven't heard of Monero? Then do me the favor by checking out: https://www.getmonero.org/
