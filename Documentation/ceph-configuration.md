---
title: Configuration
weight: 3700
indent: true
---

# Configuration

For most any Ceph cluster, the user will want to--and may need to--change some Ceph
configurations. These changes often may be warranted in order to alter performance to meet SLAs or
to update default data resiliency settings.

**WARNING:** Modify Ceph settings carefully, and review the
[Ceph configuration documentation](https://docs.ceph.com/docs/master/rados/configuration/) before
making any changes. Changing the settings could result in unhealthy daemons or even data loss if
used incorrectly.


## Required configurations
Rook and Ceph both strive to make configuration as easy as possible, but there are some
configuration options which users are well advised to consider for any production cluster.

### Default PG and PGP counts
`osd_pool_default_pg_num` and `osd_pool_default_pgp_num`. The number of PGs and PGPs can be
configured on a per-pool basis, but it is highly advised to set default values that are appropriate
for your Ceph cluster. Appropriate values depend on the number of OSDs the user expects to have
backing each pool. The Ceph
[OSD and Pool config docs](https://docs.ceph.com/docs/master/rados/operations/placement-groups/#a-preselection-of-pg-num)
provide detailed information about how to tune these parameters.

An easier option exists for Rook-Ceph clusters running Ceph Nautilus (v14.2.x) or newer. Nautilus
[introduced the PG auto-scaler mgr module](https://ceph.com/rados/new-in-nautilus-pg-merging-and-autotuning/)
capable of automatically managing PG and PGP values for pools. This module is not enabled by default
but can be enabled by running the following from the Ceph toolbox pod (more on the toolbox pod and
Ceph CLI later in this document). Using this module, the user does not need to configure
`osd_pool_default_pg_num` or `osd_pool_default_pgp_num`.
```
ceph mgr module enable pg_autoscaler
```

The user must also decide whether they want the autoscaler to be used for all new pools by default.
Turning this on is recommended. The Ceph CLI command for turning this on is shown below, but this
can be configured via any of the configuration methods detailed later in this document.
```
ceph config set global osd_pool_default_autoscale_mode on
```


## Specifying configuration options

### Toolbox + Ceph CLI
The most recommended way of configuring Ceph is to set Ceph's configuration directly. The first
method for doing so is to use Ceph's CLI from the Rook-Ceph toolbox pod. Using the toolbox pod is
detailed [here](ceph-toolbox.md). From the toolbox, the user can change Ceph configurations, enable
manager modules, create users and pools, and much more.

### Ceph Dashboard
The Ceph Dashboard, examined in more detail [here](ceph-dashboard.md), is another way of setting
some of Ceph's configuration directly. Configuration by the Ceph dashboard is recommended with the
same priority as configuration via the Ceph CLI (above).

### Advanced configuration via ceph.conf override ConfigMap
Setting configs via Ceph's CLI requires that at least one mon be available for the configs to be
set, and setting configs via dashboard requires at least one mgr to be available. Ceph may also have
a small number of very advanced settings that aren't able to be modified easily via CLI or
dashboard. The **least** recommended method for configuring Ceph is intended as a last-resort
fallback in situations like these. This is covered in detail
[here](ceph-advanced-configuration.md#custom-cephconf-settings).
