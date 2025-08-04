# MAC Limit

1. Set recovery timeout for MAC limiting

```
set interfaces ge-0/0/6 unit 0 family ethernet-switching recovery-timeout 60;
```

1.  Set fixed MACs on port

```
set interfaces ge-0/0/6 unit 0 accept-source-mac { mac-address 00:26:88:02:74:85; mac-address 00:26:88:02:74:86; }
```

1. Set limiting actions

```
set switch-options interface ge-0/0/7.0 interface-mac-limit 2
set switch-options interface ge-0/0/7.0 interface-mac-limit packet-action log

set vlans default vlan-id 1
set vlans default switch-options interface-mac-limit 3
set vlans default switch-options interface-mac-limit packet-action drop-and-log;
```

[Actions for MAC Limiting and MAC Move Limiting](https://www.juniper.net/documentation/us/en/software/junos/security-services/topics/concept/port-security-mac-limiting-and-mac-move-limiting.html#understanding-mac-limiting-and-mac-move-limiting__d5324e101)


1. Set MAC move limit. MAC allowed to move once within 1 second within VLAN

```
set vlan-1 switch-options mac-move-limit 1 packet-action shutdown
```

1. Monitoring MAC limiting

```
# Violations
show log messages | match l2ald

# Interface state
show ethernet-switching interface ge-0/0/9
```

1. Clear violations

```
clear ethernet-switching recovery-timeout interface ge-0/0/9
```

# Sticky MAC (persistent MAC learning)

1. Clear sticky MAC (persistent MAC learning)

```
clear ethernet-switching table persistent-learning
```

1. Configure sticky MAC (not on trunks, obviously)

```
set interface ge-0/0/9 persistent-learning
```

1. Monitor sticky MAC (flag P)

```
show ethernet-switching table
```

# Storm Control

1. Configure storm control (in kbps)

```
set interface ge-0/0/0 unit 0 family ethernet-switching storm-control default
set forwarding-options storm-control-profiles default all
set forwarding-options storm-control-profiles default action-shutdown # optional

set forwarding-options storm-control-profiles sc all bandwidth-level 15000
set interface ge-0/0/9 unit 0 family ethernet-switching storm-control sc

set interface ge-0/0/9 unit 0 family ethernet-switching storm-control recovery-timeout 3600
```

1. Clear storm violations (port in SCTL logical state)

```
show ethernet-switching interface ge-0/0/9
clear ethernet-switching recovery-timeout
show ethernet-switching interface ge-0/0/9
```

[Example: Using Storm Control to Prevent Network Outages](https://www.juniper.net/documentation/us/en/software/junos/security-services/topics/topic-map/using-storm-control-to-prevent-network-outages.html)
