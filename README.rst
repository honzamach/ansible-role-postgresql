.. _section-role-postgresql:

Role **postgresql**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/postgresql>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-postgresql>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-postgresql>`__

Ansible role for convenient installation of the `PostgreSQL <https://www.postgresql.org/>`__
database.

This role is intended to keep the things as simple as possible and performs only
basic installation and configuration of the PostgreSQL service. Any advanced
configuration adjustments have to be done manually. This role installs the latest
version 12 and can optionally remove the previous 10.x and 11.x versions. The actual
migration is however quite complex process and must be done manually.

**Table of Contents:**

* :ref:`section-role-postgresql-installation`
* :ref:`section-role-postgresql-dependencies`
* :ref:`section-role-postgresql-usage`
* :ref:`section-role-postgresql-variables`
* :ref:`section-role-postgresql-files`
* :ref:`section-role-postgresql-author`

This role is part of the `MSMS <https://github.com/honzamach/msms>`__ package.
Some common features are documented in its :ref:`manual <section-manual>`.


.. _section-role-postgresql-installation:

Installation
--------------------------------------------------------------------------------

To install the role `honzamach.postgresql <https://galaxy.ansible.com/honzamach/postgresql>`__
from `Ansible Galaxy <https://galaxy.ansible.com/>`__ please use variation of
following command::

    ansible-galaxy install honzamach.postgresql

To install the role directly from `GitHub <https://github.com>`__ by cloning the
`ansible-role-postgresql <https://github.com/honzamach/ansible-role-postgresql>`__
repository please use variation of following command::

    git clone https://github.com/honzamach/ansible-role-postgresql.git honzamach.postgresql

Currently the advantage of using direct Git cloning is the ability to easily update
the role when new version comes out.


.. _section-role-postgresql-dependencies:

Dependencies
--------------------------------------------------------------------------------

This role is not dependent on any other role.

Following roles have direct dependency on this role:

* :ref:`griffin <section-role-griffin>`
* :ref:`mentat <section-role-mentat>`
* :ref:`mentat_dev <section-role-mentat-dev>`


.. _section-role-postgresql-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers_postgresql]
    your-server

Example content of role playbook file ``role_playbook.yml``::

    - hosts: servers_postgresql
      remote_user: root
      roles:
        - role: honzamach.postgresql
      tags:
        - role-postgresql

Example usage::

    # Run everything:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml

    # For package cache update before installing any packages:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml --extra-vars '{"hm_pgsql__apt_force_update":"yes"}'

It is recommended to follow these configuration principles:

* Create/edit file ``inventory/group_vars/all/vars.yml`` and within define some sensible
  defaults for all your managed servers. Example::

        hm_pgsql__monitoring_password: "{{ vault_hm_pgsql__monitoring_password }}"

* Create/edit :ref:`vault <section-overview-vault>` encrypted file ``inventory/group_vars/all/vault.yml``
  and within store your backup encryption password::

        vault_hm_pgsql__monitoring_password: something-so-secret-no1-is-gonna-guess

* Use files ``inventory/host_vars/[your-server]/vars.yml`` to customize settings
  for particular servers. Please see section :ref:`section-role-postgresql-variables`
  for all available options.


.. _section-role-postgresql-variables:

Configuration variables
--------------------------------------------------------------------------------


Internal role variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: hm_pgsql__apt_key_url

    URL leading to GPG key for signing PostgreSQL packages.

    * *Type:* ``string``
    * *Default:* ``"https://www.postgresql.org/media/keys/ACCC4CF8.asc"``

.. envvar:: hm_pgsql__apt_key_id

    Identifier of the signing key.

    * *Type:* ``string``
    * *Default:* ``"ACCC4CF8"``

.. envvar:: hm_pgsql__apt_repository

    Location of PostgreSQL repository.

    * *Type:* ``string``
    * *Default:* ``"deb http://apt.postgresql.org/pub/repos/apt/[ansible_lsb['codename']]-pgdg main"``

.. envvar:: hm_pgsql__major_version

    Major version your wish this role would install to target host.

    * *Type:* ``string``
    * *Default:* ``"12"``

.. envvar:: hm_pgsql__remove_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST NOT be present on target system. Any package on this list will be removed
    from target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_logged__remove_packages:
          debian:
            apt:
              - syslog-ng
              - ...

.. envvar:: hm_pgsql__remove_previous

    Remove previous version of PostgreSQL database (true/false).

    * *Type:* ``bool``
    * *Default:* ``false``

.. envvar:: hm_pgsql__install_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST be present on target system. Any package on this list will be installed on
    target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_logged__install_packages:
          debian:
            apt:
              - syslog-ng
              - ...

.. envvar:: hm_pgsql__apt_force_update

    Force APT cache update before installing any packages ('yes','no').

    * *Datatype:* ``string``
    * *Default:* ``"no"``

.. envvar:: hm_pgsql__user

    Name for the UNIX system user for PostgreSQL database.

    * *Type:* ``string``
    * *Default:* ``"postgres"``

.. envvar:: hm_pgsql__group

    Name for the UNIX system group for PostgreSQL database.

    * *Type:* ``string``
    * *Default:* ``"postgres"``

.. envvar:: hm_pgsql__monitoring_user

    Name for the restricted user account for monitoring PostgreSQL database.

    * *Type:* ``string``
    * *Default:* ``"watchdog"``

.. envvar:: hm_pgsql__monitoring_password

    Password for the restricted user account for monitoring PostgreSQL database.
    You should encrypt this password with vault.

    * *Type:* ``string``
    * *Default:* ``"watchdog"``

.. envvar:: hm_pgsql__databases

    List of all PostgreSQL databases that should be present on target system.

    * *Type:* ``list of strings``
    * *Default:* ``[]`` (empty list)

.. envvar:: hm_pgsql__logdir

    Path to log directory.

    * *Type:* ``string``
    * *Default:* ``"/var/log/postgresql"``

.. envvar:: hm_pgsql__logfile

    Path to log file.

    * *Type:* ``string``
    * *Default:* ``"{{ hm_pgsql__logdir }}/postgresql-{{ hm_pgsql__major_version }}-main.log"``

.. envvar:: hm_pgsql__logrotate

    Log rotation switch (true/false).

    * *Type:* ``bool``
    * *Default:* ``true``

.. envvar:: hm_pgsql__logrotate_options

    Log rotation options.

    * *Type:* ``list of strings``
    * *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_pgsql__procs_mincnt

    Minimal number of running PostgreSQL processes for health monitoring.

    * *Type:* ``int``
    * *Default:* ``1``

.. envvar:: hm_pgsql__procs_maxcnt

    Maximal number of running PostgreSQL processes for health monitoring.

    * *Type:* ``int``
    * *Default:* ``100``

.. envvar:: hm_pgsql__checks

    Configurations for various Nagios check warning and critical thresholds.

    * *Type:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_pgsql__checks:
          check_postgres_query_time:
            w: 5 minutes
            c: 10 minutes
          ...


Foreign variables
--------------------------------------------------------------------------------

This role uses following foreign variables defined in other roles:

:envvar:`hm_accounts__users`

    You may flag certain users as administrators of this PostgreSQL database and
    superuser accounts will be created for them in database and ``peer`` level
    authentication access will be configured. Use flag ``is_admin_postgresql: true``
    as property of an account.

    * *Example:*

    .. code-block:: yaml

        hm_accounts__users:
          user:
            is_admin_postgresql: true
          ...



:envvar:`hm_monitored__service_name`

    Name of the NRPE service in case the server is in **servers_monitored**
    group and the playbook is automagically configuring monitoring of the Mentat
    system.


Built-in Ansible variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`group_names`

    List of group names current host is member of. This variable is used to resolve
    :ref:`soft role dependencies <section-overview-role-soft-dependencies>`.

:envvar:`ansible_lsb['codename']`

    Linux distribution codename. It is used to generate correct APT repository URL
    and for :ref:`template customizations <section-overview-role-customize-templates>`.


Group memberships
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* **servers_monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the PostgreSQL database.

* **servers_commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the PostgreSQL database.


.. _section-role-postgresql-files:

Managed files
--------------------------------------------------------------------------------

.. note::

    This role supports the :ref:`template customization <section-overview-role-customize-templates>` feature.

This role manages content of following files on target system:

* ``/etc/logrotate.d/postgresql-common`` *[TEMPLATE]*
* ``/etc/nagios/nrpe.d/postgresql.cfg`` *[TEMPLATE]*
* ``/opt/system-status/system-status.d/20-postgresql`` *[TEMPLATE]*


.. _section-role-postgresql-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Honza Mach <honza.mach.ml@gmail.com>
| *Author:* Honza Mach <honza.mach.ml@gmail.com>
| Use of this role is governed by the MIT license, see LICENSE file.
|
