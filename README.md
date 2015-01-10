jvpn
====
Connect to Juniper VPN

## Features
 * Works *without Java* on both x86 and x86_64 hosts
 * Emulates web browser to get authentication data
 * Automatically starts juniper client and passing data to it using TCP
   connection (it is not possible with command line).
 * Able to download Linux client from the Juniper VPN server without browser or
   Java.
 * Supports launching Host Checker to perform checks on a client host.
 * Option to protect resolv.conf by setting +i attribute for the connection time
 * Ability to run scripts on connect/disconnect events
 * Integration with external password/token providers, including "stoken" RSA
   softkey.

## Requirements
 * Perl with LWP modules (https)
 * openssl binary
 * unzip (only for client unpacking)

### Extra requirements for ncui mode (optional):
 (note the i686 requirements if your OS is 64-bit)
 * gcc
 * glibc-devel.i686
 * zlib.i686
 * libgcc.i686

## Usage
To configure script edit jvpn.ini. If you don`t have installed client - first run of
jvpn.pl (under sudo) and it will download and install it automatically. If you
want to run it without sudo - set suid bit on the "ncsvc" binary.

If you have multiple configurations - use --conf switch to define ini file.

### How it works
 1. Script connecting to the VPN web portal with provided user name and password.
 2. Then script handling different authentication scenarios to get DSID value
 3. After getting DSID value script getting md5 fingerprint of the SSL certificate.
 4. If VPN client is not installed script downloading and unpacking it.
 5. Script starting ncsvc and connecting to daemon (using TCP 127.0.0.1:4242
    socket in ncsvc mode or using "ncui" wrapper in ncui mode).
 6. Script emulates native GUI and passing configuration data to daemon. After
    this step VPN should work.
 7. Script can optionally protect resolv.conf from dhcpd or Network Manager by
    setting +i flag on it (disabled by default).
 8. On Ctrl+C script sending "Disconnect" command to the daemon and logging out
    on the web site.

### Difference between `mode=ncui` and `mode=ncsvc`
By default jvpn work in the ncsvc mode. This could be changed in jvpn.ini using
"mode" configuration setting.

In "ncsvc" (default) mode jvpn establishing TCP socket to nvsvc daemon and trying
to establish connection using it protocol.

In "ncui" mode jvpn tryin to use main() function libncui.so which later calling
ncsvc. If default mode does not work for you, try ncui mode. Please note that
to use ncui mode you must have gcc and other stuff (noted above) installed.

### Scripting support
It is possible to run user-defined scripts on conncect/disconnect events. To
use this functionality you will need to define script to run in the jvpn.ini
using `script=<scriptname>` line. Script should be executable file.
List of pre-defined variables and sample route table modification could be found
in scripts/sample-script.sh.

### Different ways to provide password
By default jvpn asks for password on startup. It is also possible to define
password in configuration file or to use external program to provide pass/token.
To store password in configuration use `password=plaintext:mypassword` in ini
file. To use external scripts use `password=helper:scripts/script.sh`.
Helper script should provide password to stdout. If it is called second time
(some VPN servers requesting additional tokens) jvpn defines OLDPIN variable
containing first token code. See scripts/stoken.sh for example of "stoken"
integration.

### Host checker support
In version 0.7.0 it is possible to run hostchecker using `hostchecker=1` setting
in jvpn.ini. Host Checker is used to perform checks on endpoint computers that
connect to the VPN device to make sure the endpoints meet certain security
requirements. If hostchecker support is enabled jvpn trying to run tncc.jar using
Java, as web browser applet do. JRE needs to be installed to support this
feature. It is recommended to enable only if you are unable to connect without
host checker running.

### Bugs and debugging
This script is done without any documentation, only using wireshark and
debugger. It is very likely that it has a bugs or will not work correctly for
you. If you need some support - enable debug and send me all information.
Script debug is written to stdout and daemon log is written to the
~/.juniper_networks/network_connect/ncsvc.log file.

## License
The author has placed this work in the Public Domain, thereby relinquishing
all copyrights. Everyone is free to use, modify, republish, sell or give away
this work without prior consent from anybody.

This software is provided on an "as is" basis, without warranty of any
kind. Use at your own risk! Under no circumstances shall the author(s) or
contributor(s) be liable for damages resulting directly or indirectly from
the use or non-use of this software.

## Authors
Original Author: Alex Samorukov <samm@os2.kiev.ua>
2015 revision author: Jeff Vier <jeff@jeffvier.com>
