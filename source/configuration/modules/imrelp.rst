imrelp: RELP Input Module
=========================

**Module Name:    imrelp**

**Author: Rainer Gerhards**

Provides the ability to receive syslog messages via the reliable RELP
protocol. This module requires `librelp <http://www.librelp.com>`__ to
be present on the system. From the user's point of view, imrelp works
much like imtcp or imgssapi, except that no message loss can occur.
Please note that with the currently supported relp protocol version, a
minor message duplication may occur if a network connection between the
relp client and relp server breaks after the client could successfully
send some messages but the server could not acknowledge them. The window
of opportunity is very slim, but in theory this is possible. Future
versions of RELP will prevent this. Please also note that rsyslogd may
lose a few messages if rsyslog is shutdown while a network connection to
the server is broken and could not yet be recovered. Future version of
RELP support in rsyslog will prevent that. Please note that both
scenarios also exists with plain tcp syslog. RELP, even with the small
nits outlined above, is a much more reliable solution than plain tcp
syslog and so it is highly suggested to use RELP instead of plain tcp.
Clients send messages to the RELP server via omrelp.

Module Parameters
^^^^^^^^^^^^^^^^^

.. function:: Ruleset <name>
   (requires v7.5.0+)

   Binds the specified ruleset to **all** RELP listeners. This can be
   overridden at the instance level.

Input Parameters
^^^^^^^^^^^^^^^^

.. function:: Port <port>

   Starts a RELP server on selected port

.. function:: Name <name>

   Sets a name for the inputname property of this listener.  If no name
   is set, "imrelp" is used by default.  Setting a name is not strictly
   necessary, but can be useful to apply filtering based on which input
   the message was received from.

.. function:: Ruleset <name>

   Binds specified ruleset to this listener.  This overrides the
   module-level Ruleset parameter.

.. function:: tls on/off

   *Default is off*

   If set to "on", the RELP connection will be encrypted by TLS, so
   that the data is protected against observers. Please note that both
   the client and the server must have set TLS to either "on" or "off".
   Other combinations lead to unpredictable results.

.. function:: tls.compression on/off

   *Default is off*

   The controls if the TLS stream should be compressed (zipped). While
   this increases CPU use, the network bandwidth should be reduced. Note
   that typical text-based log records usually compress rather well.

.. function:: tls.dhbits <integer>

   This setting controls how many bits are used for Diffie-Hellman key
   generation. If not set, the librelp default is used. For secrity
   reasons, at least 1024 bits should be used. Please note that the
   number of bits must be supported by GnuTLS. If an invalid number is
   given, rsyslog will report an error when the listener is started. We
   do this to be transparent to changes/upgrades in GnuTLS (to check at
   config processing time, we would need to hardcode the supported bits
   and keep them in sync with GnuTLS - this is even impossible when
   custom GnuTLS changes are made...).

.. function:: tls.permittedPeer 

   Peer Places access restrictions on this listener. Only peers which 
   have been listed in this parameter may connect. The validation bases 
   on the certificate the remote peer presents.

   The *peer* parameter lists permitted certificate fingerprints. Note
   that it is an array parameter, so either a single or multiple
   fingerprints can be listed. When a non-permitted peer connects, the
   refusal is logged together with it's fingerprint. So if the
   administrator knows this was a valid request, he can simple add the
   fingerprint by copy and paste from the logfile to rsyslog.conf.

   To specify multiple fingerprints, just enclose them in braces like
   this:
   ::

     tls.permittedPeer=["SHA1:...1", "SHA1:....2"]
   
   To specify just a single peer, you can either specify the string
   directly or enclose it in braces.

.. function:: tls.authMode <mode> 

   Sets the mode used for mutual authentication.

   Supported values are either "*fingerprint*\ " or "*name"*.

   Fingerprint mode basically is what SSH does. It does not require a
   full PKI to be present, instead self-signed certs can be used on all
   peers. Even if a CA certificate is given, the validity of the peer
   cert is NOT verified against it. Only the certificate fingerprint
   counts.

   In "name" mode, certificate validation happens. Here, the matching is
   done against the certificate's subjectAltName and, as a fallback, the
   subject common name. If the certificate contains multiple names, a
   match on any one of these names is considered good and permits the
   peer to talk to rsyslog.

.. function:: tls.prioritystring <string>

   This parameter permits to specify the so-called "priority string" to
   GnuTLS. This string gives complete control over all crypto
   parameters, including compression setting. For this reason, when the
   prioritystring is specified, the "tls.compression" parameter has no
   effect and is ignored.

   Full information about how to construct a priority string can be
   found in the GnuTLS manual. At the time of this writing, this
   information was contained in `section 6.10 of the GnuTLS
   manual <http://gnutls.org/manual/html_node/Priority-Strings.html>`_.

   **Note: this is an expert parameter.** Do not use if you do not
   exactly know what you are doing.

.. function:: KeepAlive on/off
   
   enable of disable keep-alive packets at the tcp socket layer. The
   default is to disable them.

.. function:: KeepAlive.Probes <number>

   *Default is 0*

   The number of unacknowledged probes to send before considering the
   connection dead and notifying the application layer. The default, 0,
   means that the operating system defaults are used. This has only
   effect if keep-alive is enabled. The functionality may not be
   available on all platforms.

.. function:: KeepAlive.Interval <number>

   *Default is 0*

   The interval between subsequent keepalive probes, regardless of what
   the connection has exchanged in the meantime. The default, 0, means
   that the operating system defaults are used. This has only effect if
   keep-alive is enabled. The functionality may not be available on all
   platforms.

.. function:: KeepAlive.Time <number>

   *Default is 0*

   The interval between the last data packet sent (simple ACKs are not
   considered data) and the first keepalive probe; after the connection
   is marked to need keepalive, this counter is not used any further.
   The default, 0, means that the operating system defaults are used.
   This has only effect if keep-alive is enabled. The functionality may
   not be available on all platforms.

Caveats/Known Bugs
------------------

-  see description
-  To obtain the remote system's IP address, you need to have at least
   librelp 1.0.0 installed. Versions below it return the hostname
   instead of the IP address.

Sample
------

This sets up a RELP server on port 20514.

::

  module(load="imrelp") # needs to be done just once 
  input(type="imrelp" port="20514")

Legacy Configuration Directives
-------------------------------

-  InputRELPServerBindRuleset <name> (available in 6.3.6+) equivalent
   to: RuleSet
-  InputRELPServerRun <port>
   equivalent to: Port

Caveats/Known Bugs
------------------

-  To obtain the remote system's IP address, you need to have at least
   librelp 1.0.0 installed. Versions below it return the hostname
   instead of the IP address.
-  Contrary to other inputs, the ruleset can only be bound to all
   listeners, not specific ones. This issue is resolved in the
   non-Legacy configuration format.

**Sample:**

Legacy Sample
-------------
This sets up a RELP server on port 20514.

::

  $ModLoad imrelp # needs to be done just once
  $InputRELPServerRun 20514

This documentation is part of the
`rsyslog <http://www.rsyslog.com/>`__ project.
Copyright © 2008-2014 by `Rainer
Gerhards <http://www.gerhards.net/rainer>`__ and
`Adiscon <http://www.adiscon.com/>`__. Released under the GNU GPL
version 3 or higher.
