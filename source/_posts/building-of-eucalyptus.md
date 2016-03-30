---
title: Building of Eucalyptus 0
id: 1310
categories:
  - GSOC2013
date: 2013-06-08 04:00:46
tags:
---

<!--:zh-->Downloaded eucalyptus 3.2.2, according to INSTALL, founded all dependencies necessary to build it. The summary of packages information:

## Dependencies

found:

    gcc make ant apache-ivy jpackage-utils libvirt-devel libxml2-devel libxslt-devel python-devel swig velocity xalan-j2-xsltc gawk dhcp python-boto perl-Crypt-OpenSSL-RSA perl-Crypt-OpenSSL-Random sudo jpackage-utils lvm2 velocity drbd euca2ools bitstream-vera-fonts bridge-utils iptables device-mapper coreutils e2fsprogs file parted rsync curl PyGreSQL libcurl4 apache2 libvirt tgt wget kvm dhcp-server iputils
    `</pre>
    not found:
    <pre>`ant-nodeps #'pure ant' no longer need I think
    axis2-adb-codegen
    axis2c-devel 
    axis2c  #"network:cluster:xtreemos:devel &gt; axis2" provide '12.3', "Cloud:Eucalyptus &gt; axis2" provide 'factory'
            #"Cloud:Eucalyptus" provide "axis2,axis2c"

    rampartc #"home:decriptor:eucalyptus &gt; rampartc " provide 'SLE_11'
    rampartc-devel

    java
    java-devel #use java-1_7_0-openjdk

    iscsi-initiator-utils
    drbd83-kmod
    drbd-utils
    vtun #"Virtualization &gt; vtun" provide full platform
    httpd #substitute by apache2
    vconfig
    `</pre>
    Found Similar:
    <pre>`no python-setuptools but python-setuptools-git
    no openssl-devel but libopenssl-devel
    no curl-devel but libcurl-devel
    no postgresql91 but postgresql92 and postgresql
    no postgresql91-server but postgresql92-server postgresql-server
    no which but util-linux include the command 'which'
    no drbd83 but drbd
    no dejavu-serif-fonts but dejavu-fonts
    no scsi-target-utils but iscsitarget
    `</pre>
    To Summary:

    The main dependency problem is axis2c and rampartc, the project home:decriptor:eucalyptus already work on it, but only limited to 'SLE_11', my task is to branching this project, and build axis2c and rampartc for 'openSUSE_Factory' .

    ## Problem met when build axis2c:

    <pre>`make[5]: Entering directory `/home/intijk/rpmbuild/BUILD/axis2c-src-1.6.0/util/src/platforms/unix'
    uuid_gen_unix.c: In function 'axutil_uuid_gen_v1':
    uuid_gen_unix.c:62:20: error: variable 'tv' set but not used [-Werror=unused-but-set-variable]
    cc1: all warnings being treated as errors
    `</pre>
    According to [this](https://wiki.edubuntu.org/GCC4.6):
    <pre>`Either fix it, or if the package is built with -Werror, add as a workaround: -Wno-error=unused-but-set-variable
    `</pre>
    I think I'd better not touch the source code, but modify the CFLAGS in Makefile.in , need to learn some knowledge of automake.<!--:--><!--:en-->Downloaded eucalyptus 3.2.2, according to INSTALL, founded all dependencies necessary to build it. The summary of packages information:

    ## Dependencies

    found:
    <pre>`gcc make ant apache-ivy jpackage-utils libvirt-devel libxml2-devel libxslt-devel python-devel swig velocity xalan-j2-xsltc gawk dhcp python-boto perl-Crypt-OpenSSL-RSA perl-Crypt-OpenSSL-Random sudo jpackage-utils lvm2 velocity drbd euca2ools bitstream-vera-fonts bridge-utils iptables device-mapper coreutils e2fsprogs file parted rsync curl PyGreSQL libcurl4 apache2 libvirt tgt wget kvm dhcp-server iputils
    `</pre>
    not found:
    <pre>`ant-nodeps #'pure ant' no longer need I think
    axis2-adb-codegen
    axis2c-devel 
    axis2c  #"network:cluster:xtreemos:devel > axis2" provide '12.3', "Cloud:Eucalyptus > axis2" provide 'factory'
            #"Cloud:Eucalyptus" provide "axis2,axis2c"

    rampartc #"home:decriptor:eucalyptus > rampartc " provide 'SLE_11'
    rampartc-devel

    java
    java-devel #use java-1_7_0-openjdk

    iscsi-initiator-utils
    drbd83-kmod
    drbd-utils
    vtun #"Virtualization > vtun" provide full platform
    httpd #substitute by apache2
    vconfig
    `</pre>
    Found Similar:
    <pre>`no python-setuptools but python-setuptools-git
    no openssl-devel but libopenssl-devel
    no curl-devel but libcurl-devel
    no postgresql91 but postgresql92 and postgresql
    no postgresql91-server but postgresql92-server postgresql-server
    no which but util-linux include the command 'which'
    no drbd83 but drbd
    no dejavu-serif-fonts but dejavu-fonts
    no scsi-target-utils but iscsitarget
    `</pre>
    To Summary:

    The main dependency problem is axis2c and rampartc, the project home:decriptor:eucalyptus already work on it, but only limited to 'SLE_11', my task is to branching this project, and build axis2c and rampartc for 'openSUSE_Factory' .

    ## Problem met when build axis2c:

    <pre>`make[5]: Entering directory `/home/intijk/rpmbuild/BUILD/axis2c-src-1.6.0/util/src/platforms/unix'
    uuid_gen_unix.c: In function 'axutil_uuid_gen_v1':
    uuid_gen_unix.c:62:20: error: variable 'tv' set but not used [-Werror=unused-but-set-variable]
    cc1: all warnings being treated as errors
    `</pre>
    According to [this](https://wiki.edubuntu.org/GCC4.6):
    <pre>`Either fix it, or if the package is built with -Werror, add as a workaround: -Wno-error=unused-but-set-variable

I think I'd better not touch the source code, but modify the CFLAGS in Makefile.in , need to learn some knowledge of automake.<!--:-->