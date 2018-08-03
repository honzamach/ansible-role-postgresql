.. _section-role-postgresql:

Role *postgresql*
================================================================================

*Ansible role for convenient installation of the `PostgreSQL <https://www.postgresql.org/>`__
database.*


Description
--------------------------------------------------------------------------------

This role is intended to keep the things as simple as possible and performs only
basic installation and configuration of the PostgreSQL service. Any advanced
configuration adjustments have to be done manually.


Requirements
--------------------------------------------------------------------------------

Python2 with pip utility and Python3 with pip3 utility should already be installed
on target system.


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/logrotate.d/postgresql-common``


Dependencies
--------------------------------------------------------------------------------

This role is not dependent on any other role.

Following roles have direct dependency on this role:

* :ref:`mentat <section-role-mentat>`


Role Variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_pgsql__apt_key_url
	URL leading to GPG key for signing deb packages

	*Type:* ``string`` | *Default:* ``"https://www.postgresql.org/media/keys/ACCC4CF8.asc"``

.. envvar:: hm_pgsql__apt_key_id
	Identifier of the signing key.

	*Type:* ``string`` | *Default:* ``"ACCC4CF8"``

.. envvar:: hm_pgsql__apt_repository
	Location of PostgreSQL repository.

	*Type:* ``string`` | *Default:* ``"deb http://apt.postgresql.org/pub/repos/apt/ {{ ansible_lsb['codename'] }}-pgdg main"``

.. envvar:: hm_pgsql__user
	Name of the UNIX system user for PostgreSQL database.

	*Type:* ``string`` | *Default:* ``"postgres"``

.. envvar:: hm_pgsql__group
	Name of the UNIX system group for PostgreSQL database.

	*Type:* ``string`` | *Default:* ``"postgres"``

.. envvar:: hm_pgsql__logdir
	Path to log file.

	*Type:* ``string`` | *Default:* ``"/var/log/postgresql"``

.. envvar:: hm_pgsql__logrotate
	Log rotation switch (true/false).

	*Type:* ``bool`` | *Default:* ``true``

.. envvar:: hm_pgsql__logrotate_options
	Log rotation options.

	*Type:* ``list of strings``

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: ansible_lsb['codename']
	Debian distribution codename is used to generate correct APT repository URL.


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

The advantage of using direct Git cloning is the ability to easily update the role
when new version comes out.


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

	[servers-postgresql]
	localhost

Example content of role playbook file ``playbook.yml``::

	- hosts: servers-postgresql
	  remote_user: root
	  roles:
	    - role: honzamach.postgresql
	  tags:
	    - role-postgresql

Example usage::

	ansible-playbook -i inventory playbook.yml


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Honza Mach <honza.mach.ml@gmail.com>
