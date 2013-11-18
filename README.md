ibossreg
<https://github.com/korylprince/ibossreg>

[iBoss](http://www.iboss.com/) is an Internet filter. This is a python version of their "Registration Agent" which is used to keep track of which user belongs with which computer (IP Address.)

#Installation <a name="installation"></a>#

Copy `ibossreg` and `ibossregctl` to `$PATH`, and make `/etc/ibossreg/ibossreg.ini` from the included sample.

Optionally use the include systemd service file (It expects `ibossreg` to be in `/usr/sbin`.)

These programs run on python 2 or 3 unmodified. If you wish to use the `subnets` option you must have the [`ipaddress`](http://docs.python.org/3.3/library/ipaddress) module. If you are on python 2.7, you can use [py2-ipaddress](https://pypi.python.org/pypi/py2-ipaddress).

#Configuration#

Configuration is done using python's [`ConfigParser`](http://docs.python.org/2.7/library/configparser.html) ini format.

All configuration options are listed below, sorted by section:

<table>
    <th><tr><td colspan=2><strong>server</strong></td></tr></th>
    <tr><td>protocol</td><td>Either http or https. Defaults to http.</td></tr>
    <tr><td>host</td><td>Hostname or IP Address of iBoss server.</td></tr>
    <tr><td>port</td><td>Port of iBoss server. Defaults to 8015.</td></tr>
    <tr><td>key</td><td>The iBossNetID SSO Group Security key.</td></tr>
    <th><tr><td colspan=2><strong>client</strong></td></tr></th>
    <tr><td>hostname</td><td>Client's hostname to send to the server.</td></tr>
    <tr><td>username</td><td>Default username to send to server.</td></tr>
    <tr><td>timeout</td><td>How long to wait for iBoss server to respond. Defaults to 5 seconds.</td></tr>
    <tr><td><interval/td><td>How long to wait between sending each update. Defaults to 150 seconds.</td></tr>
    <tr><td>debug</td><td>Print debugging message. Defaults to False</td></tr>
    <tr><td>subnets</td><td>List of CIDR subnets separated by commas. If this option is set, an update will only be sent to the iBoss server if the client IP address lies in one of the given subnets. See more information below.</td></tr>
    <th><tr><td colspan="2"><strong>groups</strong></td></tr></th>
    <tr><td>group number</td><td>Username in group (see below)</td></tr>
</table>

## Subnets ##

The `ipaddress` module is required to use the `subnets` option as discussed in the [Installation](#installation) section. When this option is enabled, every `interval` period, a simple socket connection (with `timeout` used as the timeout) is made to `host`:`port` to determine the clients IP Address. If this address is in one of the subnets given, then the actual request is made to the server.

If `subnets` is not given, a simple `time.sleep` loop is used.

## Groups ##

If the `groups` options is given, the daemon opens a named pipe at `/run/ibossreg` which, with the `ibossregctl` tool, allows you to change groups on the fly. For each group you wish to be able to change to, you must provide a username that belongs to that group. For example:

    [groups]
    1=default_user
    2=administrator

To switch groups simply call `ibossregctl <group number>` and your current user is changed to the value of the group number in the `groups` option. Note that change of user lasts only as long as the daemon is alive. If you restart the process, the `username` option will be read again.

Whatever user you wish to run the daemon as must be able to create the pipe in `/run`, and the user you run `ibossregctl` with must be able to write to `/run/ibossreg`.

If you have any issues or questions (or want to make it better), email the email address below, or open an issue at: <https://github.com/korylprince/ibossreg/issues>

#Caveats#

This code hasn't been thoroughly tested. The iBoss API is pretty simple, but they might change it without notice. Don't use this in production without testing.

#Copyright Information#
Copyright 2013 Kory Prince (korylprince AT gmail DAWT com).

License is BSD.
