# Logins

## Cluster
The cluster accepts 2 distinct 16-bit login values using the KWP1281 login command.

The first login value is the SKC (secret key code), which is stored at EEPROM addresses $0CC-$0CD. This login is used to pair the cluster with an ECU. It varies from cluster to cluster for anti-theft purposes.

The second login value (50315) is hardcoded in ROM. It has not yet been determined what this login is used for.

## CAN Gateway

The CAN Gateway accepts one login value. This value is stored at EEPROM addresses $13C-$13D and is typically 10777. It has not yet been determined what this login is used for.
