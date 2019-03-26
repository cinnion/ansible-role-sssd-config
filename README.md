# sssd-config

An ansible role which installs files necessary to configure SSSD for
authentication, authorization and making the other changes for
providing home directories over NFSv4.

The use of this role is intended to be gated based on site-specific variables
(discussed below) which define LDAP and NFS related information. The author
defines these variables by adding specific machines to an inventory group, then
using a file under group_vars to set these variables. While presently not doing
so, this will allow setting up multiple realms having their own authentication
and home directories, such as for development, a group of bastion servers
providing a specific service, etc.

## Requirements

This role has been developed using Ansible 2.6, and presently only works with
RHEL/CentOS 6.x and 7.x, along with Fedora 29.

**NOTE**: Adjustments will need to be made to use this with RHEL, as well as
when RHEL/CentOS 8 is released, perhaps switching to including the preparation
tasks based on yum vs. dnf, or by using the combination of distribution
name/version instead of just the name.

## Role Variables

This role uses variables which are broken down into two separate groups: those
for the role in general, and those specific to a given user realm.

### Generic role variables

The following variables are defined in `defaults/main.yml`.

    ca_trusted_dir: /etc/pki/ca-trust/source/anchors

The directory where CA root certificates are placed when adding them
to the trust databases.

    ca_update_command: update-ca-trust

The command which is run to rebuild the trust databases with any CA
root certificates which we have added.

    ldap_dir: /etc/openldap/

The directory where we place the ldap.conf file.

    sssd_conf_dir: /etc/sssd/conf.d/

The directory where we will place all our SSSD configuration snippets.

    autofs_daemon: autofs

The name of the autofs service/daemon.

    autofs_service_state: started

What the initial state of the initial state of the autofs service is
to be when installed. The recommended values are either `started` or
`stopped`.

    autofs_service_enabled: true

Whether the autofs service is to be enabled: `true` or `false`.

    autofs_restarted_state: restarted

The autofs state when configuration changes are made. The recommended
values are either `restarted` or `reloaded`.

    idmapd_daemon: nfs-idmapd

The name of the idmapd service/daemon, which handles mapping the
remote user IDs to local IDs.

    idmapd_service_state: started

What the initial state of the initial state of the idmapd service is
to be when installed. The recommended values are either `started` or
`stopped`.

    idmapd_service_enabled: true

Whether the idmapd service is to be enabled: `true` or `false`.

    idmapd_restarted_state: restarted

The idmapd state when configuration changes are made. The recommended
values are either `restarted` or `reloaded`

    sssd_daemon: sssd

The name of the sssd service/daemon.

    sssd_service_state: started

What the initial state of the initial state of the sssd service is to
be when installed. The recommended values are either `started` or
`stopped`.

    sssd_service_enabled: true

Whether the sssd service is to be enabled: `true` or `false`.

    sssd_restarted_state: restarted

The sssd state when configuration changes are made. The recommended
values are either `restarted` or `reloaded`.

    nfs_nobody_user: nobody

The username to which root or unmapped users are mapped to during ID
mapping. Used in `templates/idmapd.conf.j2` when producing the
idmapd.conf configuration file.

    nfs_nobody_group: nobody

The group to which root/wheel or unmapped groups are mapped during ID
mapping. Used in `templates/idmapd.conf.j2` when producing the
idmapd.conf configuration file.


### Realm Specific Role Variables

Additionally, the following variables must also be defined through
either the site, host or group variables files.

    nfs_home_server: filer

The name of the server from which home directories are mounted. Used
in `templates/auto.home.j2` when producing the home directory autofs
map.

    nfs_domain: example.com

The domain for NFS, used by the idmapd process. Used in
`templates/idmapd.conf.j2` when producing the idmapd.conf
configuration file.

    nfs_ldap_server: ldap.example.com

The LDAP server used by the idmapd process. Used in
`templates/idmapd.conf.j2` when producing the idmapd.conf
configuration file.

    nfs_ldap_base: dc=example,dc=com

The LDAP DN used as the base while doing the idmapd process. Used in
`templates/idmapd.conf.j2` when producing the idmapd.conf
configuration file.

## Dependencies

None.

## Example Playbook

See `defaults/playbook.yml`

## License

This software is open-sourced software licensed under the
[Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0).

## Author Information

This role was created 2019 March 22 by [Douglas Needham](https://www.ka8zrt.com/).
