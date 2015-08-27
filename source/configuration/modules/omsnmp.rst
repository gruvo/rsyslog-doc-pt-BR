omsnmp: SNMP Trap Output Module
===============================

**Module Name:** omsnmp

**Author:** Andre Lorbach <alorbach@adiscon.com>

**Description**:

Provides the ability to send syslog messages as an SNMPv1 & v2c traps.
By default, SNMPv2c is preferred. The syslog message is wrapped into a
OCTED STRING variable. This module uses the
`NET-SNMP <http://net-snmp.sourceforge.net/>`_ library. In order to
compile this module, you will need to have the
`NET-SNMP <http://net-snmp.sourceforge.net/>`_ developer (headers)
package installed.

 

**Action Line:**

%omsnmp% without any further parameters.

 

**Configuration Directives**:

-  **$actionsnmptransport**\ (This parameter is optional, the default
   value is "udp")

   Defines the transport type you wish to use. Technically we can
   support all transport types which are supported by NET-SNMP.
   To name a few possible values:
   udp, tcp, udp6, tcp6, icmp, icmp6 ...

   Example: ``$actionsnmptransport udp``
 
 
-  **$actionsnmptarget**

   This can be a hostname or ip address, and is our snmp target host.
   This parameter is required, if the snmptarget is not defined, nothing
   will be send.

   Example: ``$actionsnmptarget server.domain.xxx``
 
 
-  **$actionsnmptargetport** (This parameter is optional, the default
   value is "162")

   The port which will be used, common values are port 162 or 161.

   Example: ``$actionsnmptargetport 162``
 
 
-  **$actionsnmpversion** (This parameter is optional, the default
   value is "1")

   There can only be two choices for this parameter for now.
   0 means SNMPv1 will be used.
   1 means SNMPv2c will be used.
   Any other value will default to 1.
   
   Example: ``$actionsnmpversion 1``
 
 
-  **$actionsnmpcommunity** (This parameter is optional, the default
   value is "public")

   This sets the used SNMP Community.

   Example: ``$actionsnmpcommunity public``
 
 
-  **$actionsnmptrapoid** (This parameter is optional, the default
   value is "1.3.6.1.4.1.19406.1.2.1" which means
   "ADISCON-MONITORWARE-MIB::syslogtrap")

   This configuration parameter is used for **SNMPv2** only.
   This is the OID which defines the trap-type, or notifcation-type
   rsyslog uses to send the trap.
   In order to decode this OID, you will need to have the
   ADISCON-MONITORWARE-MIB and ADISCON-MIB mibs installed on the
   receiver side. Downloads of these mib files can be found here:

   `http://www.adiscon.org/download/ADISCON-MIB.txt <http://www.adiscon.org/download/ADISCON-MIB.txt>`_

   `http://www.adiscon.org/download/ADISCON-MONITORWARE-MIB.txt <http://www.adiscon.org/download/ADISCON-MONITORWARE-MIB.txt>`_
   Thanks to the net-snmp mailinglist for the help and the
   recommendations ;).

   Example: ``$actionsnmptrapoid 1.3.6.1.4.1.19406.1.2.1``
   If you have this MIBS installed, you can also configured with the
   OID Name: ``$actionsnmptrapoid ADISCON-MONITORWARE-MIB::syslogtrap``
 
 
-  **$actionsnmpsyslogmessageoid** (This parameter is optional, the
   default value is "1.3.6.1.4.1.19406.1.1.2.1" which means
   "ADISCON-MONITORWARE-MIB::syslogMsg")

   This OID will be used as a variable, type "OCTET STRING". This
   variable will contain up to 255 characters of the original syslog
   message including syslog header. It is recommend to use the default
   OID.
   In order to decode this OID, you will need to have the
   ADISCON-MONITORWARE-MIB and ADISCON-MIB mibs installed on the
   receiver side. To download these custom mibs, see the description of
   **$actionsnmptrapoid.**

   Example: ``$actionsnmpsyslogmessageoid 1.3.6.1.4.1.19406.1.1.2.1``
   If you have this MIBS installed, you can also configured with the
   OID Name: ``$actionsnmpsyslogmessageoid``
   ADISCON-MONITORWARE-MIB::syslogMsg
 
 
-  **$actionsnmpenterpriseoid** (This parameter is optional, the
   default value is "1.3.6.1.4.1.3.1.1" which means
   "enterprises.cmu.1.1")

   Customize this value if needed. I recommend to use the default value
   unless you require to use a different OID.
   This configuration parameter is used for **SNMPv1** only. It has no
   effect if **SNMPv2** is used.

   Example: ``$actionsnmpenterpriseoid 1.3.6.1.4.1.3.1.1``
 
 
-  **$actionsnmpspecifictype** (This parameter is optional, the default
   value is "0")

   This is the specific trap number. This configuration parameter is
   used for **SNMPv1** only. It has no effect if **SNMPv2** is used.

   Example: ``$actionsnmpspecifictype 0``
 
 
-  **$actionsnmptraptype** (This parameter is optional, the default
   value is "6" which means SNMP\_TRAP\_ENTERPRISESPECIFIC)

   There are only 7 Possible trap types defined which can be used here.
   These trap types are:

   ::

     0 = SNMP_TRAP_COLDSTART
     1 = SNMP_TRAP_WARMSTART
     2 = SNMP_TRAP_LINKDOWN
     3 = SNMP_TRAP_LINKUP
     4 = SNMP_TRAP_AUTHFAIL
     5 = SNMP_TRAP_EGPNEIGHBORLOSS
     6 = SNMP_TRAP_ENTERPRISESPECIFIC

   Any other value will default to 6 automatically. This configuration
   parameter is used for **SNMPv1** only. It has no effect if **SNMPv2**
   is used.

   Example: ``$actionsnmptraptype 6``

 

**Caveats/Known Bugs:**

-  In order to decode the custom OIDs, you will need to have the adiscon
   mibs installed.

**Sample:**

The following commands send every message as a snmp trap.

::

   $ModLoad omsnmp
   $actionsnmptransport udp
   $actionsnmptarget localhost
   $actionsnmptargetport 162
   $actionsnmpversion 1
   $actionsnmpcommunity public
   *.* :omsnmp:

This documentation is part of the `rsyslog <http://www.rsyslog.com/>`_
project.

Copyright © 2008-2014 by `Rainer Gerhards <http://www.gerhards.net/rainer>`_
and `Adiscon <http://www.adiscon.com/>`_. Released under the GNU GPL
version 3 or higher.
