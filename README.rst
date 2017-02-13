=====
duply
=====

Overview
========

This is an Ansible role for making a server backup itself using the
duply_ front-end to duplicity_. Currently it does not support
encryption and it installs a duply profile called "main".

.. _duply: http://duply.net
.. _duplicity: http://duplicity.nongnu.org/

What is backed up
=================

Everything is backed up, except for stuff that is reproduced during
installation (such as ``/usr``), can be recreated (such as
``/var/cache``), is temporary (such as ``/tmp`` and ``/var/tmp``), or
should be backed up in another way (such as live database files).

More specifically, the files and directories excluded are ``/bin``,
``/boot``, ``/dev``, ``/initrd.img``, ``/lib``, ``/lib32``, ``/lib64``,
``/lost+found``, ``/media``, ``/mnt``, ``/opt``, ``/proc``, ``/run``,
``/sbin``, ``/sys``, ``/tmp``, ``/usr``, ``/var/cache``, ``/var/crash``,
``/var/lib/postgres``, ``/var/lib/mysql``, ``/var/lock``, ``/var/run``,
``/var/swap``, ``/var/swapfile``, ``/var/swap.img``, ``/var/tmp``, and
``/vmlinuz``. There is currently no option for changing that.

Pre- and post- scripts
======================

The ``/etc/duply/main/pre-scripts`` and ``/etc/duplicity/post-scripts``
directories contain scripts that will be executed before and after
backup. Each ansible role that needs this should drop executable files
in these directories. These files are executed in no particular order.
``/etc/duply/main/pre`` is particularly useful for storing database
dumps.

Since ``duply`` does not directly supports having many scripts, but only
a single pre and a single post script (in ``/etc/duply/main/pre`` and
``/etc/duply/main/post``), this is implemented by creating a ``pre`` and
a ``post`` script that merely run scripts in these directories. Don't
touch ``/etc/duply/main/pre`` and ``/etc/duply/main/post`` themselves.
  
When backup occurs
==================

The backup script is just thrown in ``/etc/cron.daily/duply``, so backup
occurs when the OS is configured to run daily cron scripts; by default
06:25. There is currently no option available to change this.

Variables
=========

- ``duply_deactivate``: Default false. Set to true in order to
  deactivate backup of a server. This may be useful when setting up
  backup servers themselves.
- ``duply_target``: No default.
- ``duply_verbosity``: Default "notice". See also the
  ``duply_max_fulls_with_incrs`` parameter below.
- ``duply_max_fullbkp_age``: How often to do full backups; default 3M.
- ``duply_max_age``: Oldest backup to keep.  Default 2Y.
- ``duply_max_fulls_with_incrs``: Delete incremental sets
  of all backup sets that are older than the count:th last full
  backup. Note: the related ``duply`` (and therefore ``duplicity``)
  command is always run with verbosity=error, regardless what has been
  set in ``duply_verbosity``. This is because at the usual
  verbosity=warning, on some machines, not understood exactly when, the
  command shows an apparently harmless message "No extraneous files
  found, nothing deleted in cleanup." The default for this variable is
  2; it can also be set to "" so that no incremental sets are removed.
- ``duplicity_extra_parms``: Extra parameters to duplicity.  If you
  backup with ssh and have not added the target host key to known_hosts,
  you might want to use ``--ssh-options=-oStrictHostKeyChecking=no``.

Meta
====

Written by Antonis Christofides

| Copyright (C) 2011-2017 Antonis Christofides
| Copyright (C) 2013 Ministry of Environment of Greece
| Copyright (C) 2014-2017 National Technical University of Athens

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see http://www.gnu.org/licenses/.
