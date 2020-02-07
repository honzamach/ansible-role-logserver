.. _section-role-logserver:

Role **logserver**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/logserver>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-logserver>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-logserver>`__

Ansible role for convenient installation and configuration of central log server.

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
* :ref:`certified <section-role-certified>`
* :ref:`monitored <section-role-monitored>`
* :ref:`firewalled <section-role-firewalled>`

No other roles have dependency on this role.


.. _section-role-logserver-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

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


.. _section-role-logserver-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| *Author:* Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
| Use of this role is governed by the MIT license, see LICENSE file.
|
