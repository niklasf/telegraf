# Nftables Plugin

This plugin gathers packet and bytes counters and set element counts for rules
within Linux's [nftables][nftables] firewall.

⭐ Telegraf v1.37.0
🏷️ network, system
💻 linux

[nftables]: https://wiki.nftables.org/wiki-nftables/index.php/Main_Page

## Global configuration options <!-- @/docs/includes/plugin_config.md -->

Plugins support additional global and plugin configuration settings for tasks
such as modifying metrics, tags, and fields, creating aliases, and configuring
plugin ordering. See [CONFIGURATION.md][CONFIGURATION.md] for more details.

[CONFIGURATION.md]: ../../../docs/CONFIGURATION.md#plugins

## Configuration

```toml @sample.conf
[[inputs.nftables]]
  ## Use the specified binary which will be looked-up in PATH
  # binary = "nft"

  ## Use sudo for command execution, can be restricted to
  ## "nft --json list table"
  # use_sudo = false

  ## Tables to monitor (may use "family table" format, e.g., "inet filter")
  # tables = [ "filter" ]

  ## Include anonymous counters, identified by a unique comment on the rule.
  ## Rules without a comment are ignored.
  # anonymous_counters = true
```

Since telegraf will fork a process to run nftables, `AmbientCapabilities` is
required to transmit the capabilities bounding set to the forked process.

### Using sudo

You may edit your sudo configuration with the following:

```sudo
telegraf ALL=(root) NOPASSWD: /usr/bin/nft --json list table *
```

## Metrics

Counters:

* nftables
  * tags:
    * table
    * family
    * counter
  * fields:
    * pkts (integer, count)
    * bytes (integer, bytes)

Sets:

* nftables
  * tags:
    * table
    * family
    * set
  * field:
    * count (integer, count)

Anonymous counters on commented rules:

* nftables
  * tags:
    * table
    * family
    * chain
    * rule -- comment associated to the rule
  * fields:
    * pkts (integer, count)
    * bytes (integer, bytes)

## Example Output

```text
> nftables,chain=incoming,host=my_hostname,rule=comment_val_1,table=filter,family=inet bytes=66435845i,pkts=133882i 1757367516000000000
> nftables,chain=outgoing,host=my_hostname,rule=comment_val2,table=filter,family=inet bytes=25596512i,pkts=145129i 1757367516000000000
> nftables,host=my_hostname,counter=my_counter,table=filter,family=inet bytes=66435845i,pkts=133882i 1757367516000000000
> nftables,host=my_hostname,set=my_set,table=filter,family=inet count=10i 1757367516000000000
```
