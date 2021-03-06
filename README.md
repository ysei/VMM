VMM (or VMWare-Manager) is a script which greatly simplifies managing VMware servers in a sane and scriptable way.

It performs most of the crucial functionality provided by vSphere without asking too many unnecessary questions.


Command line reference
======================
```
<!-- POD -->
NAME
    vmm - Manage VMware virtual machines

SYNOPSIS
            vmm <command> [options]

    Global syntax:
                vmm <command> [-f] [-v+] [-w seconds] [@profile]

    Command specific syntax:
                vmm clone [-o pool] [-c count] [-a datastore...] [-l folder] <source vm> <new vm name>
                vmm deploy [synonym of 'clone']
                vmm df [-h] [datastore...]
                vmm host [maintenance|restore|restart|shutdown|disconnect|reconnect] <hosts...>
                vmm list [-d col1,col2... | perl string] [-t title] [vm|datastore|host|pool|template][s] [patterns...]
                vmm migrate [-p low|normal|high] [-o pool] <vms...> <host>
                vmm move [-o pool] <vms...> <datastore>
                vmm setpool <low|normal|high> <cpu|mem|all> <pools...>
                vmm show [vm|datastore|host|pool] [items...]
                vmm snapshot [-t title] <vms...>
                vmm state <on|off|suspend|reboot|shutdown|restart|standby> <vms...>
                vmm version

COMMANDS
    clone   Clone a given VM or template to another VM name. If -c is
            specified multiple copies are made. Each VM name will be
            incremented in the usual Perlish way. e.g.: DB1, DB2, DB3 etc.

            If -a is unspcified the datastore of the source VM is copied as
            the new clones datastore. If -a is specified the data store will
            be set during cloning. If -a contains a comma denoted list the
            datastores will be alternated during cloning. e.g. -a 1,2 copies
            to datastore 1 then 2 then starts again at 1. Patterns of
            repeating datastores can be specified - e.g. '-a 1,2,3,2,1'.

    deploy  Synonym of 'clone'.

    df      Display disk usage information about datastores. If a list of
            matches is provided, the data store list is filtered for those
            items.

    list    Display a list of the given items matching a pattern.

            Possible lists include (selection can be plural or singular):

                    vms (default if unspecified)
                    datastores
                    hosts
                    pools

            If '-d' is specified without any containing '$' marks the string
            is evaluated as a CSV with each line extracting the requisite
            information that would be shown with the 'show' command. See the
            EXAMPLES section for further information.

            If '-d' is specified and contains a '$' it is evaluated as a
            perl expression with $_ being set to the currently active item.

            If '-t' is spcified the titles header for the table is set.

    migrate Migrate a given list of VMs to another host.

    move    Move a given list of VMs to another datastore.

    setpool Set the share level on the given resource pools.

    show    Show information about a given object. If no specific object
            type is specified 'vm' is assumed. If no specific matching
            pattern is specified all objects of that type are listed.

    snapshot
            Take a snapshot of the matching VM's. If '-t' is specified, it
            is used as the title of the snapshot. Otherwise the current time
            is used.

    state   Set the state of a list of VMs.

            This can be any of the following choices:

                    on - Power up the specified VMs
                    off - Power down the specified VMs. This is a hard power state so data loss could occur.
                    suspend - Power the machine into standby mode. This is a hard power state which does not rely on VMware tools.
                    restart - Hard power cycle the VMs. Like 'off' this is a forced power state so data loss could occur.
                    shutdown - Try shutting down the machine via VMware tools.
                    standby - Try to put the machine into the soft standby state.
                    reboot - Try shutting down the machine via VMware tools.

    version Display various version informaiton about the connected vServer
            and local API. This command is the default if no actual command
            is specified (i.e. just running 'vmm' with nothing else
            specified).

OPTIONS
    [@profile]

            Used during: All operations
            Default: First specified profile in config
            Type: Profile name or URL

            Specifies which profile to use when addressing the vServer. This
            can be an entry within the config file or the URL (with optional
            login details) e.g.

                    vmm version @cluster1
                    vmm version @cluster2
                    vmm version @https://cluster1.acme.edu
                    vmm version @https://username@cluster1.acme.edu
                    vmm version @https://username:password@cluster1.acme.edu

            Examples 1 and two assume 'custer1' and 'customer2' have been
            defined in the examples file (see EXAMPLES). The further
            examples specify the connection information on the command line.
            Specifying the password from the command line is exceptionally
            silly and should be avoided.

            If username and/or password is omitted (such as in examples 3
            and 4 above) they will be prompted for when vmm is run.

    -c

            Used during: migrate
            Default: 1
            Type: Number

            Specifies how many VMs should be created during a clone
            operation. The name of the target VM is incremented in the usual
            Perlish way. e.g.

            DBS1, DBS2, DBS3... DBS10 DB00, DB01, DB02... DB99 DBAA, DBAB,
            DBAC... DBZZ

    --display [col1,col2...]
    -d [col1,col2...]

            Used during: list
            Default: Name
            Type:
                Command seperated list of columns to display in tabular
                output when using the 'list' command. See also: -s to
                specify the seperation character to use between the columns.

            Specify a data store for operations that require it.

    --datastore [datastore]
    --ds [datastore]
    -a [datastore]

            Used during: migrate
            Default: Same as source VM
            Type: Datastore name

            Specify a data store for operations that require it.

    --force
    -f

            Used during: All operations
            Default: off
            Type: Switch

            Force continue if an error occurs. Normaly if an error occurs
            vmm will stop processing any operations specified on the command
            line. If this flag is enabled vmm will continue operation as if
            no error occured.

    --human
    -h

            Used during: df
            Default: off
            Type: Switch

            Display the numbers of the 'df' command in a human readable
            format.

    --dryrun
    -n

            Used during: All operations
            Default: off
            Type: Switch

            Dry run mode. When enabled vmm will continue as normal but no
            actual call to the VMware VServer is made.

    --folder
    -l

            Used during: clone
            Default: Source VM / templates folder
            Type: String

            Specifies the folder that the cloned machine should be moved
            into. If unspecified the source VM's folder is used instead.

    --pool [pool]
    -o [pool]

            Used during: clone, migrate, move
            Default: Same as the source VM
            Type: Pool name

            Specifies the alternate pool name to use when migrating or
            cloning machines. If unspecified the source VM's pool is used
            instead.

    --priority [priority]
    -p [priority]

            Used during: migrate
            Default: low
            Type: Choice of: low, normal, high

            Specifies the priority when migrating VMs.

    --seperator [character]
    -s [character]

            Used during: list
            Default: \t (tab)
            Type: String

            Specifies the string to display between columns when outputing a
            list.

    --title [title]
    -t [title]

            Used during: list, snapshot
            Default: The Unix EPOC (for VMs)
            Type: String

            The title of the snapshot to create or the title row of the list
            table.

    -v
    --verbose

            Used during: All operations
            Default: 0
            Type: Accumulating switch

            Be more verbose when outputting information to STDERR. Specify
            multiple times to increase verbosity.

    -w [seconds]
    -wait [seconds]

            Used during: clone, host, migrate, setpool, state
            Default: 0
            Type: Number of seconds

            Force a wait for the specified number of seconds between
            operations.

DESCRIPTION
    A command line tool for the manipulation of VMware Virtual Machines
    (VM).

EXAMPLES
    vmm clone VM01 VM02
        Clone VM01 to VM02. Since neither the datasource (-d) or pool (-o)
        is specified these details are copied from VM01.

    vmm clone DB00 DB01 -c 30
        Clone DB01 to DB02 creating 30 copies. This will actually make the
        machines DB01 to DB30. Since neither the datasource (-d) or pool
        (-o) is specified these details are copied from VM01.

    vmm clone DB00 DB01 -c 30 -d SAN1,SAN2 -o Active
        Same as the above example but spread the datastores across SAN1 and
        SAN2 and move the machine to the 'Active' pool.

    vmm deploy Template-DBServer DB05 -l Databases
        Deploys the template Template-DBServer into DB05, moving the
        destination into the Databases folder.

    vmm clone Template-DBServer DB05 -l Databases
        This is exactly the same as above. A clone and deploy operations
        will automatically figure out if the source is a template and act
        accordingly.

    vmm df *2
        Display a datastore usage sheet (similar to the Unix 'df' command)
        for all datastores ending in '2'.

    vmm host maintenance Moe Homer
        Put the hosts 'Moe' and 'Homer' into maintenance mode (use 'restore'
        to recover from this).

    vmm list vms
        List all VMs.

    vmm list vms -d name,host,ip
        List all VMs - showing their name, currently allocated host and IP
        address.

    vmm migrate DBS* Carl
        Migrate all virtual machines matching 'DBS*' to the 'Carl' host.

    vmm migrate DBS* Lenny -w 60 -o Active -p high
        Migrate all virtual machines matching 'DBS*' with high priority to
        the 'Active' pool on the 'Lenny' host waiting 60 seconds between
        machine.

    vmm move DB00 DB01 SAN2 @cluster2
        Move VMs DB00 and DB01 to the SAN2 datastore within profile
        'cluster2'.

    vmm show host Lisa
        Show information on host 'Lisa'.

    vmm snapshot DB04 DB05 -t 'Todays backup'
        Take a snapshot of VMs 'DB04' and 'DB05' using the title 'Todays
        backup'

    vmm state on DB00 DB01 -w 30
        Turn DB00 and DB01 on waiting 30 seconds between machines.

    vmm state on DB00 DB01 -w 30 -f
        Turn DB* VMs on. -f ensures that even if any of the machines fail to
        turn on for any reason the remaining machines will still be sent the
        'on' command.

FILES
    /etc/vmmrc
            VMM config file for all users.

    .vmmrc  VMM config file for the local user.

CONFIG
    The /etc/vmmrc and .vmmrc files will be processed to determine VMM's
    configuration, the latter file taking precedence.

    The layout of the config file spcifies which profiles to use.

            [GLOBAL]
            rewrite host = s/^(.+?)\./\1/
            verbose = 2
            profile = Cluster1
            dryrun = 0
            human = 1
            force = 0
            seperator = \t
            http_proxy = http://myproxy.example.com:8080
            https_proxy = http://myproxy.example.com:8080
        
            [Cluster1]
            url = https://cluster1.acme.edu
            username = admin
            password = password
        
            [Cluster2]
            url = https://cluster2.acme.edu
            username = administrator
            password = changeme

    [GLOBAL]
            The meta global section. Any option specified here will be
            automatically carried into each profiles config.

            In the main example Cluster1 will have a 'verbose' option of 2.
            Cluster2 will have a 'verbose' option of 1 since it overrides
            the global setting.

    [profile]
            The name (case-insensitive) of the profile to define.

    url     The connection URL of the vServer system within the profile

    username and password
            The authentication information when connecting to the vServer.

    dryrun  Specify a default dry run value. See -n for further information.

    force   If an error is encounted during a multiple VM operation the
            default behaviour is to stop execution. If this setting is set
            to '1' this behaviour will be overridden and operations will
            continue even if an error is encounted.

    http_proxy
    https_proxy
            Forceably set the HTTP(s)_PROXY environment variable to the
            provided value before connecting. This is included to assist
            basic login shells where these variables are not imported
            correctly.

    human   Always output numbers in a human readable format rather than the
            raw form.

    priority
            Specify a default priority when using any command that is '-p'
            compatible.

    profile Specify the default profile to use if none is explicitally set.
            If not specified the first found profile in the config file is
            used.

    rewrite host
            Specify a substitution regular expression to use when correcting
            host names. The value given in the above example will remove any
            trailing DNS name correcting 'host1.a.very.long.dns.com' to
            'host1'.

    seperator
            Specify the seperator character used when outputting tabular
            data with the 'list' command.

    verbose Specify a default verbosity level. See -v for further
            information.

INSTALLATION
    VMM requires a few external modules before it can work correctly. Follow
    the following stages to get everything working.

    * Install the VMware Perl SDK from
    http://www.vmware.com/support/developer/viperltoolkit/

    * Install all the required CPAN modules

            cpan Config::IniFiles Getopt::Long Number::Bytes::Human Term::ReadKey Text::Glob

    * Setup the config file. See either the CONFIG section above or use the
    sample file from /usr/share/doc/vmm

    * Run VMM with a simple command to make sure everything is setup right.

            vmm version

    * Enjoy

BUGS
    Quite probably.

    Please report to https://github.com/hash-bang/VMM when found.

AUTHOR
    Matt Carter <m@ttcarter.com>

<!-- END POD -->
```

TODO
====
* Allow pattern ranges e.g. DB{10..20} DB{10,12,15}
* Post action triggers - run this script with set options on certain events. e.g. postclone=vittl-linux-init $host $password

Future commands
* Template, Untemplate
* Delete
* Bios (Sets the 'bios on next boot' flag)
* AttachCD (Sets an ISO to the VM's CD-ROM drive)
* ToolsUpgrade (Upgrade VMware tools)
