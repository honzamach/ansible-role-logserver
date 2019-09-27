.. _section-role-application-logserver:

Role **logserver**
================================================================================

Ansible role for convenient installation and configuration of central log server.

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/logserver>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-logserver>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-logserver>`__


Description
--------------------------------------------------------------------------------

This role is responsible for provisioning of cetral log server.

.. note::

    This role supports the :ref:`template customization <section-overview-customize-templates>` feature.


Requirements
--------------------------------------------------------------------------------

This role does not have any special requirements.


Dependencies
--------------------------------------------------------------------------------

This role is dependent on following roles:

* :ref:`accounts <section-role-accounts>`
* :ref:`certified <section-role-certified>`
* :ref:`monitored <section-role-monitored>`
* :ref:`firewalled <section-role-firewalled>`

No other roles have direct dependency on this role.


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/syslog-ng/syslog-ng.conf``


Role variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_logserver__package_list

    List of role-related packages, that will be installed on target system.

    * *Datatype:* ``string``
    * *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_logserver__host_log_dir

    Location for remote system log storage.

    * *Datatype:* ``directory``
    * *Default value:* ``"/var/log/cls-servers"``

.. envvar:: hm_logserver__log_all_file

    Name of the catch all log file.

    * *Datatype:* ``filepath``
    * *Default value:* ``"/var/log/net-all.log"``

hm_logserver__compress_older_than

    Age of the log files, that will be compressed to save disk space.

    * *Datatype:* ``integer``
    * *Default value:* ``31``

hm_logserver__cleanup_older_than

    Age of the log files, that will be permanently removed to save disk space.

    * *Datatype:* ``integer``
    * *Default value:* ``1095`` (roughly three years)

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: ansible_lsb['codename']

    Debian distribution codename is used for :ref:`template customization <section-overview-customize-templates>`
    feature.

.. envvar:: group_names

    See section *Group memberships* below for details.


Foreign variables
--------------------------------------------------------------------------------

This role makes use of following foreign variables, that are defined within other
roles:

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


Group memberships
--------------------------------------------------------------------------------

* **servers_monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the Syslog-ng system.

* **servers_commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the Syslog-ng system.


Usage and customization
--------------------------------------------------------------------------------

This role is (attempted to be) written according to the `Ansible best practices <https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html>`__. The default implementation should fit most users,
however you may customize it by tweaking default variables and providing custom
templates.


Variable customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most of the usefull variables are defined in ``defaults/main.yml`` file, so they
can be easily overridden almost from `anywhere <https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable>`__.


Template customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This roles uses *with_first_found* mechanism for all of its templates. If you do
not like anything about built-in template files you may provide your own custom
templates. For now please see the role tasks for list of all checked paths for
each of the template files.


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


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers_logserver]
    localhost

Example content of role playbook file ``playbook.yml``::

    - hosts: servers_logserver
      remote_user: root
      roles:
        - role: honzamach.logserver
      tags:
        - role-logserver

Example usage::

    ansible-playbook -i inventory playbook.yml


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Jan Mach <jan.mach@cesnet.cz>, CESNET, a.l.e.
