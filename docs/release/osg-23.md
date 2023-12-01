title: OSG 23 News

OSG 23 News
===========

**Supported OS Versions:** EL8, EL9 (see [this document](supported_platforms.md) for details)

OSG 23 is the first release series that follows our new [support policy](release_series.md), which aims to increase the
regularity of the OSG Software Stack lifecycle.
Moving forward, we plan to distribute a new release series each year, supporting each release series for approximately 2
years total.
As with OSG 3.6, we will continue to release OSG 23 package updates in a rolling fashion.

Additionally, OSG 23 aligns the OSG and HTCondor Software Suite (HTCSS) release cycles:

-   OSG 23 main Yum repositories will contain HTCSS LTS series
    ([HTCondor 23.0](https://htcondor.readthedocs.io/en/23.0/index.html),
    [HTCondor-CE 23.0](https://htcondor.com/htcondor-ce/v23/installation/htcondor-ce/))
-   OSG 23 upcoming Yum repositories will contain HTCSS feature series (HTCondor 23.x, HTCondor-CE 23.x)

Known Issues
------------

The following issues are known to currently affect packages distributed in OSG 23:

### CA Certificates on EL9 ###

EL9 operating systems have a tighter default cryptographic policy that can cause services to reject certificates issued
by SHA-1 signed CAs.
Some CAs in the `igtf-ca-certs` and `osg-ca-certs` packages are affected and you may see service issues if your server
certificate or certificates presented by clients are issued by these CAs.
The Software Team is investigating solutions but in the meantime, we recommend running the following command on XRootD
hosts to accept certificates issued by SHA-1 signed CAs:

```
root@host # update-crypto-policies --set DEFAULT:SHA1
```

!!! note "Do I need to run this on my Compute Entrypoint (CE) hosts?"
    No. At this time, the Software Team believes that CE hosts are unaffected since their clients only present tokens
    and token issuers present modern CAs.


Latest News
-----------

!!! warning "Incoming changes to `osg-ca-certs`"
    To address an issue with certificates issued by SHA1 CAs on EL9 systems and EL8 systems configured to use the
    `FUTURE` system crypto policy,
    the OSG Software Team intends to release a fix to the `osg-ca-certs` package in the near future.

    -   If you are running OSG-supported software (e.g., HTCondor, XRootD),
        update to the new version of `osg-ca-certs`.

    -   If you are running non-OSG-supported, Java-based software (e.g., dCache) with `osg-ca-certs`,
        repleace `osg-ca-certs` with `osg-ca-certs-java` with the following command:s

    ```
    root@host # yum swap osg-ca-certs osg-ca-certs-java
    ```
        `osg-ca-certs-java` is compatible with Java-based software but does not include the aforementioned fix.

### **November 30, 2023:** VO Package v133, IGTF 1.125
-   [VO Package v133](https://github.com/opensciencegrid/osg-vo-config/releases/tag/release-133)
    -   Update certificates for FNAL and GlueX VOMS servers
-   CA certificates based on [IGTF 1.125](http://dist.eugridpma.info/distribution/igtf/current/CHANGES)
    -   Updated root certificate ArmeSFo CA with extended validity (AM)

### **November 16, 2023:** VO Package v132, XRootD 5.6.3, osg-ce 23-2, HTCondor-CE 23.0.1, osg-system-profiler 1.7.0
-   [VO Package v132](https://github.com/opensciencegrid/osg-vo-config/releases/tag/release-132)
    -   Update certificates for FNAL and SLAC VOMS servers
    -   Update certificates for CLAS12, EIC, GLOW, and HCC
    -   Drop stale certificates for nanohub, STAR, and wisc.edu lz
-   [XRootD 5.6.3](https://listserv.slac.stanford.edu/cgi-bin/wa?A2=ind2310&L=XROOTD-L&P=1554)
    -   Fix parsing of chunked PUT requests
    -   Add HTTP TPC packet marking
    -   Differentiate between push and pull TPC error messages
    -   Use configured CA path for the SciTokens plugin
-   osg-ce meta package
    -   Correctly set value of `OSG_RELEASE_SERIES` attribute for OSG 23
-   [HTCondor-CE 23.0.1](https://htcondor.com/htcondor-ce/v23/releases/#2301)
    -   Add `condor_ce_test_token` command
-   osg-system-profiler 1.7.0
    -   Add system cryptographic policy
    -   Better XRootD configuration information for generated profile

### **November 2, 2023:** IGTF 1.124, CVMFS 2.11.2, cvmfs-x509-helper 2.4
-   CA certificates based on [IGTF 1.124](http://dist.eugridpma.info/distribution/igtf/current/CHANGES)
    -   Updated contact meta-data for ArmeSFo authority (AM)
    -   Removed discontinued AEGIS authority (RS)
    -   Removed suspended KENET Root and issuing CAs (KE)
    -   Removed suspended SDG-G2 authority (CN)
    -   Removed suspended CNIC authority (CN)
    -   Removed all four discontinued DigitalTrust CAs operated by their issuer (AE)
-   [CVMFS 2.11.2](https://cvmfs.readthedocs.io/en/2.11/cpt-releasenotes.html#release-notes-for-cernvm-fs-2-11-2)
    -   Bug fix release
-   [cvmfs-x509-helper 2.4](https://github.com/cvmfs-contrib/cvmfs-x509-helper/releases/tag/2.4)
    -   Important bug fix for reading credentials from within an unprivileged user namespace such
        as unprivileged apptainer users.  This is needed due to a change in recent el8 & el9 kernels.

### **October 31, 2023:** HTCondor 23.0.1 LTS; Upcoming: HTCondor 23.1.0
-   [HTCondor 23.0.1 LTS](https://htcondor.readthedocs.io/en/23.0/version-history/lts-versions-23-0.html#version-23-0-1)
    -   Update to apptainer version 1.2.4 in the HTCondor tarballs
    -   Fix 10.6.0 bug that broke PID namespaces
    -   Fix bug where execution times for ARC CE jobs were 60 times too large
    -   Fix bug where a failed 'Service' node would crash DAGMan
    -   Condor-C and Job Router jobs now get resources provisioned updates
-   Upcoming: [HTCondor 23.1.0](https://htcondor.readthedocs.io/en/23.x/version-history/feature-versions-23-x.html#version-23-1-0)
    -   Enhanced filtering with `condor_watch_q`
    -   The Access Point can now be told to use a non-standard ssh port when
        sending jobs to a remote scheduling system (such as Slurm)
    -   Laid groundwork to allow an Execution Point running without root access
        to accurately limit the job's usage of CPU and Memory in real time via
        Linux kernel cgroups; this is particularly interesting for glidein pools
    -   HTCondor file transfers using HTTPS can now utilize CA certificates
        in a non-standard location
    -   All the fixes from HTCondor 23.0.1

### **October 26, 2023:** CVMFS 2.11.1-1.3, XRootD 5.6.2-2.3, osg-update-vos 1.4.2-2
-   [CVMFS 2.11.1-1.3](https://cvmfs.readthedocs.io/en/2.11/cpt-releasenotes.html#release-notes-for-cernvm-fs-2-11-1)
    -   Important fix to bug impacting osgstorage.org repositories introduced in 2.11.0 --
            all 2.11.0 installations should upgrade urgently
    -   Fix race conditions on concurrent fuse3 mounts
-   XRootD 5.6.2-2.3
    -   Update to -2.3 release to avoid confusion with upstream -2 release
    -   Fix a bug with parsing compound IDs in authfiles
-   osg-update-vos 1.4.2-2
    -   tarballs now contain cpio, so osg-update-vos will work

### **October 3, 2023**: Initial Release

!!! info "OSG 3.6 retirement"
    As part of our transition to our new series release cadence, we are planning to end support for OSG 3.6 on 30 June
    2024 to align with the EL7 end-of-life.
    See our [release series life-cycle](release_series.md#series-life-cycle) table for details.

The initial release of OSG 23 contains [major package updates](#major-package-updates),
[package removals](#package-removals), and [new container images](#container-images).
All other packages may have received minor version and/or packaging updates compared to OSG 3.6.

#### Major package updates ####

This release contains the following major package updates compared to the current OSG 3.6 release:

-   [HTCondor 23.0.0](https://htcondor.readthedocs.io/en/23.0/version-history/lts-versions-23-0.html#version-23-0-0):
    an update from 10.0.8 in OSG 3.6 main and 10.8.0 in OSG 3.6 upcoming.
    -   New features
        -   A `condor_startd` without any slot types defined will now default to a single partitionable slot rather than a
            number of static slots equal to the number of cores as it was in previous versions.
            The configuration template use `FEATURE : StaticSlots` was added for admins wanting the old behavior.
        -   The `TargetType` attribute is no longer a required attribute in most Classads.
            It is still used for queries to the `condor_collector` and it remains in the Job ClassAd and the Machine
            ClassAd because of older versions of HTCondor require it to be present.
        -   The `-dry-run option` of `condor_submit` will now print the output of a `SEC_CREDENTIAL_STORER script`.
            This can be useful when developing such a script.
        -   Added ability to query epoch history records from the python bindings.
        -   The default value of `SEC_DEFAULT_AUTHENTICATION_METHODS` will now be visible in `condor_config_val`.
            The default for `SEC_*_AUTHENTICATION_METHODS will` inherit from this value, and thus no `READ` and `CLIENT`
            will no longer automatically have `CLAIMTOBE`.
        -   Added new tool `condor_test_token`, which will create a SciToken with configurable contents (including
            issuer) which will be accepted for a short period of time by the local HTCondor daemons.
    -   Bug fixes
        -   Fixed a bug that would cause the condor_startd to crash in rare cases when jobs go on hold
        -   Fixed a bug where if a user-level checkpoint could not be transferred from the starter to the AP, the job
            would go on hold
            Now it will retry, or go back to idle
        -   Fixed a bug where the `CommittedTime` attribute was not set correctly for Docker Universe jobs doing user
            level check-pointing
        -   Fixed a bug where `condor_preen` was deleting files named 'OfflineAds' in the spool directory
        -   Fixed a bug where the blahpd would incorrectly believe that an LSF batch scheduler was not working
        -   Fixed the Execution Point’s detection of whether libvirt is working properly for the VM universe
        -   Fixed a bug where container universe did not work for late materialization jobs submitted to the
            `condor_schedd`
        -   Fixed a bug where the condor_startd could crash if a new match is made at the end a drain request

-   [HTCondor-CE 23.0.0](https://htcondor.com/htcondor-ce/v23/installation/htcondor-ce/):
    an update from 6.0.0 in OSG 3.6 main.

    !!! warning "Job router configuration deprecation"
        The configuration macros `JOB_ROUTER_DEFAULTS`, `JOB_ROUTER_ENTRIES`, `JOB_ROUTER_ENTRIES_CMD`, and
        `JOB_ROUTER_ENTRIES_FILE` are deprecated and will be removed for V24 of HTCondor.
        New configuration syntax for the job router is defined using `JOB_ROUTER_ROUTE_NAMES` and
        `JOB_ROUTER_ROUTE_<name>`.
        Note: The removal will occur during the lifetime of the HTCondor V23 feature series, i.e. the versions that will
        be available in OSG upcoming repositories.

    -   Adds deprecation warnings for old job router configuration syntax
    -   Adds grid CA and host certificate/key locations to default SSL search paths
    -   Verifies that HTCondor-CE can access the local HTCondor's `SPOOL` directory on startup
    -   `condor_ce_upgrade_check` checks compatibility with HTCondor 23
    -   Adds an option to allow running `condor_ce_trace` without a SciToken for testing batch system integration

-   [XRootD 5.6.2](https://github.com/xrootd/xrootd/blob/v5.6.2/docs/ReleaseNotes.txt#L1-L123):
    an update from XRootD 5.5.5 in OSG 3.6 main.
    -  New Features
        -   Add xrdfs cache subcommand to allow for cache evictions
        -   Better handling of unicode strings in the API
        -   Add gsi option to display DN when it differs from entity name
        -   Allow specfication of minimum and maximum creation mode
        -   Make maxfd be configurable (default is 256k)
        -   Include token information in the monitoring stream (phase 1)
        -   Implement a file evict function
        -   Increase default number of parallel event loops to 10
        -   xrdcp: number of parallel copy jobs increased from 4 to 128
        -   Allow XRootD to return trailers indicating failure
        -   Denote Accept-Ranges in HEAD response
        -   Report cache object age for caching proxy mode
        -   Allow origin to be a directory of a locally mounted file system
        -   Implement ability to have the token username as a separate claim
        -   Use SHA-256 for signatures, and message digest algorithm
        -   Allow option '-tokenlib none' to disable token validation
        -   Allow to point to a token file using CGI `'?xrd.ztn=tokenfile'`
    -   Major bug fixes
        -   Fix SEGV in case request has object for opaque data but no content
        -   Fix memory leaks in GSI authentication
        -   Fix chunked PUT creating empty files

-   [GlideinWMS 3.10.5](http://glideinwms.fnal.gov/doc.v3_10_5/history.html):
    an update from 3.10.1 in OSG 3.6 main

    !!! warning "If you are using custom setup scripts..."
        If you are using custom setup scripts please change the use of `glidein_config`:

        -   Custom scripts should always read values via `gconfig_get()`.
            The only exception is the parsing of the line to get the `add_config_line` source file.
        -   `add_config_line` is deprecated in favor of `gconfig_add`.
            `add_config_line` will be removed from future versions.
        -   `add_config_line_safe` is deprecated in favor of `gconfig_add_safe`.
            `gconfig_add` is the recommended method to use also in concurrent scripts.
        -   Custom scripts in Python should `import gconfig.py` (compatible with both Python 2 and 3) and use the
            provided functions or classes: `gconfig_get`, `gconfig_add`, etc.

    -   This release completes EL9 and Python 3.9 support
    -   Added structured logging
    -   Various OSG_Autoconf improvements
    -   Fixed bugs with Python 3.9 and rrdtools failures with missing ClassAds and monitoring

#### Package Removals ####

The following packages were removed from OSG 23 compared to OSG 3.6:

-  `blahp`: available as part of the `condor` package
-  `oidc-agent`: available in EPEL
-  `python-jwt`: available in EPEL
-  `python-scitokens`: available in EPEL
-  `rrdtool` available from OS repositories

#### Container Images ####

The following container images have new tags for OSG 23:

| Image name                                                   | Tags                       |
|:-------------------------------------------------------------|:---------------------------|
| `hub.opensciencegrid.org/opensciencegrid/atlas-xcache`       | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/cms-xcache`         | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/frontier-squid`     | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/oidc-agent`         | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/osgvo-docker-pilot` | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/stash-cache`        | `23-release`, `23-testing` |
| `hub.opensciencegrid.org/opensciencegrid/stash-origin`       | `23-release`, `23-testing` |

For example, to retreive an OSG 23 backfill container image, run the following command:

```
docker pull hub.opensciencegrid.org/opensciencegrid/osgvo-docker-pilot:23-release
```

For more details on OSG container images,
see our [policy document](https://osg-htc.org/technology/policy/container-release/).

Announcements
-------------

Updates to critical packages also announced by email and are sent to the following recipients and lists:

-   [Registered administrative contacts](../common/registration.md#registering-resources)
-   [osg-general@opensciencegrid.org](https://listserv.fnal.gov/scripts/wa.exe?A0=OSG-GENERAL)
-   [osg-operations@opensciencegrid.org](https://listserv.fnal.gov/scripts/wa.exe?A0=OSG-OPERATIONS)
-   [osg-sites@opensciencegrid.org](https://listserv.fnal.gov/scripts/wa.exe?A0=OSG-SITES)
-   [site-announce@opensciencegrid.org](https://listserv.fnal.gov/scripts/wa.exe?A0=site-announce)
-   [software-discuss@osg-htc.org](https://groups.google.com/a/osg-htc.org/g/software-discuss)