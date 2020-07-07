.. _section-role-logserver:

Role **logserver**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/logserver>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-logserver>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-logserver>`__

Ansible role for convenient installation and configuration of central log server
capable of receiving system logs from many other servers in the network. Access to
log ports is restricted with firewall only to listed IP addresses. Currently
following logging options/ports are supported:

1. Port 514 - Native TLS encryption with client certificate verification.
2. Port 1514 - TLS encryption with client certificate verification via Stunnel.
3. Port 2514 - Native TLS encryption without client certificate verification.

Administrators of servers logging to the central log server are enabled read access
to log files via SSH. These files are linked to their home directories into
``/home/[user-name]/logs``.

**Table of Contents:**

* :ref:`section-role-logserver-installation`
* :ref:`section-role-logserver-dependencies`
* :ref:`section-role-logserver-usage`
* :ref:`section-role-logserver-variables`
* :ref:`section-role-logserver-files`
* :ref:`section-role-logserver-author`

This role is part of the `MSMS <https://github.com/honzamach/msms>`__ package.
Some common features are documented in its :ref:`manual <section-manual>`.


.. _section-role-logserver-installation:

Installation
--------------------------------------------------------------------------------

To install the role `honzamach.logserver <https://galaxy.ansible.com/honzamach/logserver>`__
from `Ansible Galaxy <https://galaxy.ansible.com/>`__ please use variation of
following command::

    ansible-galaxy install honzamach.logserver

To install the role directly from `GitHub <https://github.com>`__ by cloning the
`ansible-role-logserver <https://github.com/honzamach/ansible-role-logserver>`__
repository please use variation of following command::

    git clone https://github.com/honzamach/ansible-role-logserver.git honzamach.logserver

Currently the advantage of using direct Git cloning is the ability to easily update
the role when new version comes out.


.. _section-role-logserver-dependencies:

Dependencies
--------------------------------------------------------------------------------

This role is dependent on following roles:

* :ref:`accounts <section-role-accounts>`

  This role is used to manage user accounts to enable access to server logs for
  administrators of that server.

* :ref:`certified <section-role-certified>`

  This role is used to manage server certificates.

* :ref:`monitored <section-role-monitored>`

  This role is used to configure log server monitoring.

* :ref:`firewalled <section-role-firewalled>`

  This role is used to restrict access to log ports only from certain hosts.

No other roles have dependency on this role.


.. _section-role-logserver-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers]
    your-server

    [server_central_logserver]
    your-server

    [servers_logserver]
    your-server

Example content of role playbook file ``role_playbook.yml``::

    - hosts: servers_logserver
      remote_user: root
      roles:
        - role: honzamach.logserver
      tags:
        - role-logserver

Example usage::

    # Run everything:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml


.. _section-role-logserver-variables:

Configuration variables
--------------------------------------------------------------------------------


Internal role variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: hm_logserver__install_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST be present on target system. Any package on this list will be installed on
    target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_logserver__install_packages:
          debian:
            apt:
              - syslog-ng
              - ...

.. envvar:: hm_logserver__host_log_dir

    Location for remote system log storage.

    * *Datatype:* ``directory``
    * *Default:* ``"/var/log/cls-servers"``

.. envvar:: hm_logserver__log_all_file

    Name of the catch all log file.

    * *Datatype:* ``filepath``
    * *Default:* ``"/var/log/net-all.log"``

hm_logserver__compress_older_than

    Age of the log files in days, that will be compressed to save disk space.

    * *Datatype:* ``integer``
    * *Default:* ``31``

hm_logserver__cleanup_older_than

    Age of the log files in days, that will be permanently removed to free disk space.

    * *Datatype:* ``integer``
    * *Default:* ``1095`` (roughly three years)


Foreign variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`hm_certified__cert_host_dir`

    Syslog-ng daemon will be configured to use custom server certificates.

:envvar:`hm_certified__trustedcert_ca_dir`

    Syslog-ng daemon will be configured to use custom CA certificate directory.

:envvar:`hm_monitored__plugins_dir`

    Path to the Nagios plugin directory in case the server is in **servers_monitored**
    group and the playbook is automagically configuring monitoring of the Syslog-ng
    system.

:envvar:`hm_monitored__service_name`

    Name of the NRPE service in case the server is in **servers_monitored**
    group and the playbook is automagically configuring monitoring of the Syslog-ng
    system.

:envvar:`hm_accounts__users`

    Logs of remotely logged servers are symlinked to home directories of appropriate
    users.


Built-in Ansible variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: ansible_lsb['codename']

    Debian distribution codename is used for :ref:`template customization <section-overview-role-customize-templates>`
    feature.


Group memberships
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* **servers_monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the Syslog-ng system.

* **servers_commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the Syslog-ng system.


.. _section-role-logserver-files:

Managed files
--------------------------------------------------------------------------------

.. note::

    This role supports the :ref:`template customization <section-overview-role-customize-templates>` feature.

This role manages content of following files on target system:

* ``/etc/syslog-ng/syslog-ng.conf`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/syslog-ng.conf.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/syslog-ng.conf.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/syslog-ng.conf.j2``
    ``inventory/group_files/servers/honzamach.logserver/syslog-ng.conf.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/syslog-ng.conf.j2``

* ``/etc/stunnel/stunnel.conf`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/stunnel.conf.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/stunnel.conf.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/stunnel.conf.j2``
    ``inventory/group_files/servers/honzamach.logserver/stunnel.conf.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/stunnel.conf.j2``

* ``/etc/logrotate/syslog-ng`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/logrotate_syslog-ng.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_syslog-ng.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_syslog-ng.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_syslog-ng.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_syslog-ng.j2``

* ``/etc/logrotate/stunnel4`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/logrotate_stunnel4.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_stunnel4.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_stunnel4.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_stunnel4.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_stunnel4.j2``

* ``/etc/logrotate/central-log-server`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/logrotate_central-log-server.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_central-log-server.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/logrotate_central-log-server.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_central-log-server.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/logrotate_central-log-server.j2``

* ``/etc/nagios/nrpe.d/syslog-ng.cfg`` *[TEMPLATE]*

  This file will be present in case the server is has membership in ``servers_monitored`` group.
  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/nrpe_syslog-ng.cfg.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/nrpe_syslog-ng.cfg.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/nrpe_syslog-ng.cfg.j2``
    ``inventory/group_files/servers/honzamach.logserver/nrpe_syslog-ng.cfg.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/nrpe_syslog-ng.cfg.j2``

* ``/opt/system-status/system-status.d/20-syslog-ng`` *[TEMPLATE]*

  This file will be present in case the server is has membership in ``servers_commonenv`` group.
  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/system-status-syslog-ng.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/system-status-syslog-ng.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/system-status-syslog-ng.j2``
    ``inventory/group_files/servers/honzamach.logserver/system-status-syslog-ng.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/system-status-syslog-ng.j2``

* ``/root/manage-logs.sh`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/manage-logs.sh.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/manage-logs.sh.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/manage-logs.sh.j2``
    ``inventory/group_files/servers/honzamach.logserver/manage-logs.sh.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/manage-logs.sh.j2``

* ``/etc/cron.d/manage-logs`` *[TEMPLATE]*

  Customizable with following templates::

    ``inventory/host_files/{{ inventory_hostname }}/honzamach.logserver/cron_manage-logs.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/cron_manage-logs.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers_{{ msms_server_type }}/honzamach.logserver/cron_manage-logs.j2``
    ``inventory/group_files/servers/honzamach.logserver/cron_manage-logs.{{ ansible_lsb['codename'] }}.j2``
    ``inventory/group_files/servers/honzamach.logserver/cron_manage-logs.j2``




.. _section-role-logserver-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| *Author:* Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| Use of this role is governed by the MIT license, see LICENSE file.
|
