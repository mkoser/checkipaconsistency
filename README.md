# ipa_check_consistency
## Tool to check consistency across FreeIPA servers

The tool can be used as a standalone consistency checker as well as a Nagios/Opsview plug-in (check [Nagios section below](#nagios-plug-in-mode) for more info).

The script was originally written and developed in BASH (see [v1.3.0](https://github.com/peterpakos/ipa_check_consistency/tree/v1.3.0)) and eventually ported to Python in [v2.0.0](https://github.com/peterpakos/ipa_check_consistency/tree/v2.0.0).

It has been tested with multiple FreeIPA 4.2+ deployments across a range of operating systems.

Requirements:
* FreeIPA 4.2+
* Python 2.7+/3.6+
* Python modules listed in [requirements.txt](#python-modules)

If you spot any problem or have any improvement ideas then feel free to open an issue and I will be glad to look at it for you.

## Python modules
Run the following command to install required Python modules:
```
$ pip install -r requirements.txt
```

## Configuration
Edit the sample config file `config_sample.py` and save it as `config.py`.

## Help
```
$ ./ipa_check_consistency --help
usage: ipa_check_consistency [--version] [--help] [--debug] [--verbose]
                             [--quiet] [-H] [-B]
                             [-n [{,all,users,ustage,upres,ugroups,hosts,hgroups,hbac,sudo,zones,certs,ldap,ghosts,bind,msdcs,replica}]]
                             [-w WARNING] [-c CRITICAL]

Tool to check consistency across FreeIPA servers

optional arguments:
  --version             show program's version number and exit
  --help                show this help message and exit
  --debug               debugging mode
  --verbose             verbose logging mode
  --quiet               don't log to console
  -H, --disable-header  disable table header
  -B, --disable-border  disable table border
  -n [{,all,users,ustage,upres,ugroups,hosts,hgroups,hbac,sudo,zones,certs,ldap,ghosts,bind,msdcs,replica}]
                        Nagios plugin mode
  -w WARNING, --warning WARNING
                        number of failed checks before warning (default: 1)
  -c CRITICAL, --critical CRITICAL
                        number of failed checks before critical (default: 2)
```

## Example
```
$ ./ipa_check_consistency
+--------------------+----------+----------+----------+-----------+----------+----------+-------+
| FreeIPA servers:   | ipa01    | ipa02    | ipa03    | ipa04     | ipa05    | ipa06    | STATE |
+--------------------+----------+----------+----------+-----------+----------+----------+-------+
| Active Users       | 1199     | 1199     | 1199     | 1199      | 1199     | 1199     | OK    |
| Stage Users        | 0        | 0        | 0        | 0         | 0        | 0        | OK    |
| Preserved Users    | 0        | 0        | 0        | 0         | 0        | 0        | OK    |
| User Groups        | 55       | 55       | 55       | 55        | 55       | 55       | OK    |
| Hosts              | 357      | 357      | 357      | 357       | 357      | 357      | OK    |
| Host Groups        | 29       | 29       | 29       | 29        | 29       | 29       | OK    |
| HBAC Rules         | 3        | 3        | 3        | 3         | 3        | 3        | OK    |
| SUDO Rules         | 2        | 2        | 2        | 2         | 2        | 2        | OK    |
| DNS Zones          | 114      | 114      | 114      | 114       | 114      | 114      | OK    |
| Certificates       | N/A      | N/A      | N/A      | N/A       | N/A      | N/A      | OK    |
| LDAP Conflicts     | NO       | NO       | NO       | NO        | NO       | NO       | OK    |
| Ghost Replicas     | NO       | NO       | NO       | NO        | NO       | NO       | OK    |
| Anonymous BIND     | YES      | YES      | YES      | YES       | YES      | YES      | OK    |
| Microsoft ADTrust  | NO       | NO       | NO       | NO        | NO       | NO       | OK    |
| Replication Status | ipa03 0  | ipa03 0  | ipa04 0  | ipa03 0   | ipa03 0  | ipa04 0  | OK    |
|                    | ipa04 0  | ipa04 0  | ipa05 0  | ipa01 0   | ipa01 0  |          |       |
|                    | ipa05 0  | ipa05 0  | ipa01 0  | ipa02 0   | ipa02 0  |          |       |
|                    | ipa02 0  | ipa01 0  | ipa02 0  | ipa06 0   |          |          |       |
+--------------------+----------+----------+----------+-----------+----------+----------+-------+

```

## Nagios plug-in mode
Perform all checks using default warning/critical thresholds:
```
$ ./ipa_check_consistency -n all
OK - 15/15 checks passed
```
Perform specific check with custom alerting thresholds:
```
$ ./ipa_check_consistency -n users -w 2 -c3
OK - Active Users
```

### LDAP Conflicts
Normally conflicting changes between replicas are resolved automatically (the most recent change takes precedence).
However, there are cases where manual intervention is required. If you see LDAP conflicts in the output of this script,
you need to find the conflicting entries and decide which of them should be preserved/deleted.

More information on solving common replication conflicts can be found [here](https://access.redhat.com/documentation/en-us/red_hat_directory_server/10/html/administration_guide/managing_replication-solving_common_replication_conflicts).
