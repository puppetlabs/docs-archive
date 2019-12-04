---
layout: default
title: "Troubleshooting Puppet core"
canonical: "/pe/latest/trouble_puppet.html"
---

Troubleshooting Puppet core
-----

### Improving profiling and debugging of slow catalog compilations

You can get the Puppet master to log additional debug-level messages about how much time each step of its catalog compilation takes by setting `profile` to `true` in an agent's puppet.conf file (or specify `--profile` on the CLI).

If you’re trying to profile, be sure to check the `--logdest` and `--debug` options on the master — debug must be on, and messages will go to the log destination, which defaults to syslog. If you’re running via Passenger or another Rack server, these options will be set in the config.ru file.

To find the messages, look for the string PROFILE in the master’s logs — each catalog request will get a unique ID, so you can tell which messages are for which request.


