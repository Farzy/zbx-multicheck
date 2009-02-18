# Documentation

This is the online documentation printed by the script when run as:
    zbx_multicheck --man

### NAME

zabbix_multichecker - A tool used to execute a serie of Unix commands
and then parse the command’s output to create a number of Zabbix
"items". It then uses "zabbix_sender" to send the items back to the
Zabbix server.

### SYNOPSIS

    zbx_multicheck HOSTNAME [options]

**`HOSTNAME`** must be ’`localhost`’ if the script is running as a
`UserParameter` on a Zabbix Agent.

If running on the Zabbix Server, **`HOSTNAME`** must be the name of the agent
on behalf of which we’re running this script (as an External Script).

Options:

    --agent-conf=FILE, -A FILE     Path to the Zabbix Agentd configuration file
    --server-conf=FILE, -C FILE    Path to the Zabbix Server configuration file
    --multichecker-conf=FILE, -M FILE      Path to zbx_multicheck's configuration file
    --sender=FILE, -S FILE         Path of the zabbix_sender executable
    --debug, -d                    Print more information (do not use in production!)
    --help|-h                      Brief help message
    --version|-v                   Print version and license
    --man                          Full documentation

### OPTIONS

    --agent-conf=FILE|-A FILE
        Path of the Zabbix Agentd configuration file. This file is parsed
        to get the serveur name and port, and the local hostname. This
        parameter is only used if HOSTNAME is equal to ’localhost’.
        Defaults to ’/etc/zabbix/zabbix_agentd.conf’.

    --server-conf=FILE|-C FILE
        Path of the Zabbix Server configuration file. This file is parsed
        to get the serveur name and port. This parameter is only used if
        HOSTNAME is not equal to ’localhost’.  Defaults to
        ’/etc/zabbix/zabbix_server.conf’.

    --multichecker-conf=FILE|-M FILE
        Path of zabbix_multichecker’s configuration file. The file
        contains the commands to execute and the list of regexp/items for
        each command.  Defaults to ’/etc/zabbix/zbx_multicheck.conf’.

    --sender=FILE|-S FILE
        Path of the "zabbix_sender" executable. Defaults to
        ’/usr/local/bin/zabbix_sender’.

    --debug|-d
        Print more information on the execution of the script. Do not use
        in production as it disturb the output processing!

    --help|-h
        Prints a brief help message and exits.

    --man
        Prints the manual page and exists.

    --version|-v
        Print program version and license.

### DESCRIPTION

zbx_multicheck parses a specific configuration file and executes in a
shell the defined command.

A list of regular expression is then executed on the output, and each
corresponding match generates an item and its corresponding value. The
items are then returned back to the server with the zabbix_agentd
program.

This script can either run as a UserParameter on the agent (in which
case the first parameter must explicitely be ’`localhost`’), or as an
External Script on the server (in which case the first parameter is
automatically set to the agent we’re monitoring).

### SAMPLE CONFIGURATION

Every occurrence of `@HOSTNAME@` in the command line will be replaced by
the name of the computer for which we are calculating items.

Sample zbx_multicheck.conf:

    # First command to execute and whose output we are going to parse
    command = sudo mysqladmin status
    # Each line defines a regular expression and an item corresponding to
    # the first group of parentheses in the regexp.
    item = /Uptime: (\d+)/, mysql.uptime
    item = /Threads: (\d+)/, mysql.threads
    item = /Questions: (\d+)/, mysql.questions
    item = /Slow queries: (\d+)/, mysql.slowqueries
    item = /Queries per second avg: (\d+\.?\d*)/, mysql.qps

    # Second command and its set of items
    command = sudo mysqladmin version
    item = /^Server version\s*(.*)\s*$/, mysql.server-version
    item = /^Protocol version\s*(.*)\s*$/, mysql.protocol-version

### AUTHOR

Author: Farzad FARID <ffarid@pragmatic-source.com>

For more information or support check <http://www.pragmatic-source.com/>

