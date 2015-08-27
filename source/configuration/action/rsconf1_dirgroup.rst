$DirGroup
---------

**Type:** global configuration directive

**Default:**

**Description:**

Set the group for directories newly created. Please note that this
setting does not affect the group of directories already existing. The
parameter is a group name, for which the groupid is obtained by rsyslogd
on during startup processing. Interim changes to the user mapping are
not detected.

**Sample:**

``$DirGroup loggroup``

This documentation is part of the `rsyslog <http://www.rsyslog.com/>`_
project.
Copyright © 2007 by `Rainer Gerhards <http://www.gerhards.net/rainer>`_
and `Adiscon <http://www.adiscon.com/>`_. Released under the GNU GPL
version 2 or higher.
