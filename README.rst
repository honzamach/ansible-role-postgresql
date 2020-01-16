.. _section-role-postgresql:

Role **postgresql**
================================================================================

Ansible role for convenient installation of the `PostgreSQL <https://www.postgresql.org/>`__
database.

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/postgresql>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-postgresql>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-postgresql>`__


Description
--------------------------------------------------------------------------------

This role is intended to keep the things as simple as possible and performs only
basic installation and configuration of the PostgreSQL service. Any advanced
configuration adjustments have to be done manually. This role installs the latest
version 11 and can optionally remove the 10.x. The actual migration is however
quite complex process and must be done manually.


Requirements
--------------------------------------------------------------------------------

Python2 with pip utility and Python3 with pip3 utility should already be installed
on target system.


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/logrotate.d/postgresql-common``
* ``/etc/nagios/nrpe.d/postgresql.cfg``
* ``/opt/system-status/system-status.d/20-postgresql``


Dependencies
--------------------------------------------------------------------------------

This role is not dependent on any other role.

Following roles have direct dependency on this role:

* :ref:`mentat <section-role-mentat>`


Role variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_pgsql__apt_key_url

	URL leading to GPG key for signing deb packages

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

.. envvar:: hm_pgsql__remove_list

	List of role-related packages, that will be removed from target system.

	* *Datatype:* ``list of strings``
	* *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_pgsql__remove_previous

	Remove previous version of PostgreSQL database (true/false).

	* *Type:* ``bool``
	* *Default:* ``false``

.. envvar:: hm_pgsql__package_list

	List of role-related packages, that will be installed on target system.

	* *Datatype:* ``list of strings``
	* *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_pgsql__apt_force_update

	Force APT cache update before installing any packages ('yes','no').

	* *Datatype:* ``string``
	* *Default:* ``no``

.. envvar:: hm_pgsql__user

	Name of the UNIX system user for PostgreSQL database.

	* *Type:* ``string``
	* *Default:* ``"postgres"``

.. envvar:: hm_pgsql__group

	Name of the UNIX system group for PostgreSQL database.

	* *Type:* ``string``
	* *Default:* ``"postgres"``

.. envvar:: hm_pgsql__logdir

	Path to log file.

	* *Type:* ``string``
	* *Default:* ``"/var/log/postgresql"``

.. envvar:: hm_pgsql__logrotate

	Log rotation switch (true/false).

	* *Type:* ``bool``
	* *Default:* ``true``

.. envvar:: hm_pgsql__logrotate_options

	Log rotation options.

	* *Type:* ``list of strings``
	* *Default:* (please see YAML file ``defaults/main.yml``)

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: ansible_lsb['codename']

	Debian distribution codename is used to generate correct APT repository URL
	and for :ref:`template customization <section-overview-customize-templates>`
	feature.

.. envvar:: group_names

	See section *Group memberships* below for details.


Foreign variables
--------------------------------------------------------------------------------

This role uses following foreign variables defined in other roles:

:envvar:`hm_monitored__service_name`

    Name of the NRPE service in case the server is in **servers_monitored**
    group and the playbook is automagically configuring monitoring of the Mentat
    system.



Group memberships
--------------------------------------------------------------------------------

* **servers_monitored**

  In case the target server is member of this group Nagios monitoring is automagically
  configured for the PostgreSQL database.

* **servers_commonenv**

  In case the target server is member of this group system status script is automagically
  configured for the PostgreSQL database.


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


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

	[servers_postgresql]
	localhost

Example content of role playbook file ``playbook.yml``::

	- hosts: servers_postgresql
	  remote_user: root
	  roles:
	    - role: honzamach.postgresql
	  tags:
	    - role-postgresql

Example usage::

	ansible-playbook -i inventory playbook.yml
	ansible-playbook -i inventory playbook.yml --extra-vars '{"hm_mentat__apt_force_update":"yes"}'


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Honza Mach <honza.mach.ml@gmail.com>
