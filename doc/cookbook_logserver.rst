.. _section-cookbook-roles-logserver:

Central logging - server
================================================================================


.. _section-cookbook-roles-logserver-newclient:

Enable logging to central log server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For configuration steps from the point of view of the log client please refer to
section :ref:`section-cookbook-roles-logged-newserver` for more details.

From the point of view of the log server you only need to meet following conditions:

1. You must use appropriate ``trusted_ca`` certificates to be able to verify client
   identity. These certificates are managed by the :ref:`certified <section-role-certified>`
   role and you will ussually use the same set of certificates on both client and
   server. The most easiest approach is then to place all CA certificate files to
   ``inventory/group_files/servers/honzamach.certified/ca_certs`` directory. They
   will be uploaded to ``/etc/ssl/trusted_ca`` directory on remote servers and
   the syslog-ng daemon will be automatically configured to use them.

2. You must add ``your-server`` to inventory groups ``servers_logserver`` and
   ``server_central_logserver``.

3. You must open the log port for the client. Please refer to documentation of
   role :ref:`logserver <section-role-logserver>` for the list of all available
   options. After you pick one, open the appropriate port::

     hm_firewalled__open_port_hosts:
       514:
         # postino.cesnet.cz
         - 195.113.144.242
         - 2001:718:1:101::144:242

Then execute the role::

    apbf role_logserver.yml

Please refer to role :ref:`logserver <section-role-logserver>` for more details.


.. _section-cookbook-roles-logserver-newuser:

Enable user access to log files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To enable access to server log files for particular user please follow these simple
steps:

1. Make sure you have appropriate record for the user in user registry file ``inventory/group_vars/all/users.yml``.
   It should look similiar to this::

     site_users:
       mach:
         name: Jan Mach
         name_utf: Jan Mach
         firstname: Jan
         lastname: Mach
         email: jan.mach@cesnet.cz
         ssh_keys:
           - "ssh-rsa AAAA== mek@omnius"
           - "ssh-rsa AAAA== mek@rimmer"
         workstations:
           - "192.168.1.1"
           - "::1"

2. Now use that user record to enable access to you central log server. Place configuration
   similar to this one to ``inventory/host_vars/[your-central-log-server]/vars.yml`` file::

    # Create system group to restrict access to log files.
    hm_accounts__groups:
      my_group: {}

    # Create user account and add it to the group created above.
    hm_accounts__users:
      mach:
        groups:
          - my_group

    # Map server log files to system group.
    hm_logserver__host_logs:
      "clientserver": my_group
