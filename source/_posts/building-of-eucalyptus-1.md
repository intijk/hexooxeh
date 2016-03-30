---
title: 'Building of Eucalyptus 1 '
tags:
  - Eucalyptus
  - openSUSE
  - rpmbuild
id: 1315
categories:
  - GSOC2013
date: 2013-07-25 05:05:02
---

<!--:zh-->Since the (Building of Eucalyptus 0), a lot of time passed, I found the axis2 and axis2c under Cloud:Eucalyptus are usable, so I just skip to compilation work.

First, I think need to compile the source code on my PC(openSUSE_12.3 x86_64, i5 4 cores) manually.
Second, do a local rpmbuild.
Third, push the work to OBS.

Now local compilation success, I am doing a local rpmbuild now. This log is what I met when I did the manually compilation.

## Packages needed

Besides the packages mentioned in (Building Eucalyptus 0), these packages also required when building it.

    libcurl-devel
    rampartc-devel
    xerces-j2
    bouncycastle
    `</pre>
    The packages in (0) are:

    _(I am sure some packages are not 'BuildRequires' but 'Requires', and some will not be used, but I don't know which one belongs to which group, so for now just install it)._
    <pre>`gcc make ant apache-ivy jpackage-utils libvirt-devel libxml2-devel libxslt-devel python-devel swig velocity xalan-j2-xsltc gawk dhcp python-boto perl-Crypt-OpenSSL-RSA perl-Crypt-OpenSSL-Random sudo jpackage-utils lvm2 velocity drbd euca2ools bitstream-vera-fonts bridge-utils iptables device-mapper coreutils e2fsprogs file parted rsync curl PyGreSQL libcurl4 apache2 libvirt tgt wget kvm dhcp-server iputils
    `</pre>

    ## Choose Java Version

    Here I use openjdk 1.7 for compiling.
    <pre>`export JAVA_HOME="/usr/lib64/jvm/java-1.7.0-openjdk-1.7.0"
    export JAVA="$JAVA_HOME/bin/java"
    export EUCALYPTUS="/opt/eucalyptus" 
    `</pre>

    ## Configure

    ./configure --with-axis2c=/usr/lib64/axis2c --prefix=$EUCALYPTUS --with-apache2-module-dir=/usr/lib64/apache2 --with-axis2=/opt/axis2

    The _/usr/lib64_ will be changed to _${_libdir}_ when writing the spec file.

    After this, you will see:
    <pre>`  Eucalyptus 3.3.0

      Flags and Settings:
        CFLAGS..............: -g -O2 -D_LARGEFILE64_SOURCE  -Wall -Wno-unused-variable -fPIC -DHAVE_CONFIG_H -std=gnu99
        LDFLAGS.............: -L/usr/lib64/axis2c/modules/rampart -L/usr/lib64/axis2c/lib   
        LIBS................: -lxml2 -lz -lrt -lpthread -lneethi -lmod_rampart -lm -laxutil -laxis2_parser -lguththila -laxis2_http_sender -laxis2_http_receiver -laxis2_http_common -laxis2_engine -laxis2_axiom   
        Debugging Enabled...: no

      Build Tools:
        ANT.................: /usr/bin/ant (1.8.2)
        JAVA................: /usr/lib64/jvm/java-1.7.0-openjdk-1.7.0/bin/java (1.7.0)
        PYTHON..............: /usr/bin/python (2.7)
        WSDL2C..............: /usr/lib64/axis2c/bin/tools/wsdl2c/WSDL2C.sh

      Locations:
        PREFIX..............: /opt/eucalyptus
        APACHE2_MODULE_DIR..: /usr/lib64/apache2
        AXIS2C_HOME.........: /usr/lib64/axis2c
        AXIS2C_SERVICES.....: /usr/lib64/axis2c/services
        AXIS2_HOME..........: /opt/axis2
        EUCA_DB_HOME........: Not Set
        EUCA_DB_SUFFIX......: Not Set
        JAVA_HOME...........: /usr/lib64/jvm/java-1.7.0-openjdk-1.7.0
        LIBVIRT_HOME........: Not Set
        VDDK_HOME...........: Not Set
    `</pre>
    Those **Not Set** there makes me very panic, I know PostgreSQL is mentioned in INSTALL when compiling under Ubuntu. But not said under CentOS. Just mark is here, I will test it later with DB arguments given.

    ## Java jar path

    Some jar files are needed. After you installed the packages, it will leave jar files under
    <pre>`/usr/share/java
    `</pre>
    To let them known by javac, export CLASSPATH before compilation.
    <pre>`export CLASSPATH=$(build-classpath-directory /usr/share/java)
    `</pre>

    ## Disable git clone

    There is a repo called cloud-libs under github/eucalyptus, it is a directory with a lot of jar files.When first compile eucalyptus, it will git clone from this repo to clc/lib under source directory.
    <pre>`[intijk@Bumblebee.site](mailto:intijk@Bumblebee.site): lib $ ls
    activemq-core-5.4.1.jar                      jetty-continuation-8.1.10.v20130312.jar
    ant-1.7.jar                                  jetty-deploy-8.1.10.v20130312.jar
    antlr-3.0.1.jar                              jetty-http-8.1.10.v20130312.jar
    ...
    `</pre>
    There are 143 jar files with a total size of 109M. Let me just put these jar files into source code for 2 reasons:
    <pre>`1\. OBS do not allow network usage.
    2\. A huge work needed to be done if we compile all of them, and some of them need specific version.
    2.5 :) This is how they did before. ([https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus](https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus))
    `</pre>
    When do the compilation, give this:
    <pre>`make DISABLEGIT=1
    `</pre>
    ant will let clc/lib known by javac, so we don't need to bother CLASSPATH.

    ## Compilation

    After I installed the necessory packages, made all things above, I met the problem below, <span style="text-decoration: line-through;">it also causes the compilation failure of**openSUSE_12.2** and **openSUSE_Factory** on the old version here:</span>

    <span style="text-decoration: line-through;">[https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus](https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus)</span>

    (The failure here is caused by [https://eucalyptus.atlassian.net/browse/EUCA-4618](https://eucalyptus.atlassian.net/browse/EUCA-4618), and fixed in 3.3.0, a patch provided there for 2.x).

    And still be a problem when I compile the version 3.3.0.1 .
    <pre>`org.codehaus.groovy.control.MultipleCompilationErrorsException: startup failed:
    Compile error during compilation with javac.
    /tmp/groovy-generated-7965685771527083825-java-source/com/eucalyptus/config/ConfigurationMessage.java:10: error: cannot find symbol
    @com.eucalyptus.component.ComponentId$ComponentMessage(value=com.eucalyptus.config.ConfigurationService.class) public class ConfigurationMessage
                             ^
      symbol:   class ComponentId$ComponentMessage
      location: package com.eucalyptus.component
    /tmp/groovy-generated-7965685771527083825-java-source/com/eucalyptus/config/PropertiesMessage.java:10: error: cannot find symbol
    @com.eucalyptus.component.ComponentId$ComponentMessage(value=com.eucalyptus.empyrean.Empyrean$PropertiesService.class) public class PropertiesMessage
                             ^
      symbol:   class ComponentId$ComponentMessage
      location: package com.eucalyptus.component
    2 errors

    1 error

    BUILD FAILED
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/build.xml:118: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/build.xml:108: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/modules/module-inc.xml:175: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/modules/module-inc.xml:207: Compilation Failed
    `</pre>
    To solve it, I found the bug report here:
    <pre>`[https://eucalyptus.atlassian.net/browse/EUCA-5455](https://eucalyptus.atlassian.net/browse/EUCA-5455)
    `</pre>
    This bug report describe exactly what I met, the author says only groovy-all-1.7.2.jar works, current jar from auto git clone is groovy-all-2.1.5.jar. But what wired is that this bug marked as 'solved' in version 3.3.0, I am using the source code of 3.3.0.1, it should not show here.

    I manually downloaded groovy-all-1.7.2.jar and substituted groovy-all-2.1.5.jar, compilation success.<!--:--><!--:en-->Since the (Building of Eucalyptus 0), a lot of time passed, I found the axis2 and axis2c under Cloud:Eucalyptus are usable, so I just skip to compilation work.

    First, I think need to compile the source code on my PC(openSUSE_12.3 x86_64, i5 4 cores) manually.
    Second, do a local rpmbuild.
    Third, push the work to OBS.

    Now local compilation success, I am doing a local rpmbuild now. This log is what I met when I did the manually compilation.

    ## Packages needed

    Besides the packages mentioned in (Building Eucalyptus 0), these packages also required when building it.
    <pre>`libcurl-devel
    rampartc-devel
    xerces-j2
    bouncycastle
    `</pre>
    The packages in (0) are:

    _(I am sure some packages are not 'BuildRequires' but 'Requires', and some will not be used, but I don't know which one belongs to which group, so for now just install it)._
    <pre>`gcc make ant apache-ivy jpackage-utils libvirt-devel libxml2-devel libxslt-devel python-devel swig velocity xalan-j2-xsltc gawk dhcp python-boto perl-Crypt-OpenSSL-RSA perl-Crypt-OpenSSL-Random sudo jpackage-utils lvm2 velocity drbd euca2ools bitstream-vera-fonts bridge-utils iptables device-mapper coreutils e2fsprogs file parted rsync curl PyGreSQL libcurl4 apache2 libvirt tgt wget kvm dhcp-server iputils
    `</pre>

    ## Choose Java Version

    Here I use openjdk 1.7 for compiling.
    <pre>`export JAVA_HOME="/usr/lib64/jvm/java-1.7.0-openjdk-1.7.0"
    export JAVA="$JAVA_HOME/bin/java"
    export EUCALYPTUS="/opt/eucalyptus" 
    `</pre>

    ## Configure

    ./configure --with-axis2c=/usr/lib64/axis2c --prefix=$EUCALYPTUS --with-apache2-module-dir=/usr/lib64/apache2 --with-axis2=/opt/axis2

    The _/usr/lib64_ will be changed to _${_libdir}_ when writing the spec file.

    After this, you will see:
    <pre>`  Eucalyptus 3.3.0

      Flags and Settings:
        CFLAGS..............: -g -O2 -D_LARGEFILE64_SOURCE  -Wall -Wno-unused-variable -fPIC -DHAVE_CONFIG_H -std=gnu99
        LDFLAGS.............: -L/usr/lib64/axis2c/modules/rampart -L/usr/lib64/axis2c/lib   
        LIBS................: -lxml2 -lz -lrt -lpthread -lneethi -lmod_rampart -lm -laxutil -laxis2_parser -lguththila -laxis2_http_sender -laxis2_http_receiver -laxis2_http_common -laxis2_engine -laxis2_axiom   
        Debugging Enabled...: no

      Build Tools:
        ANT.................: /usr/bin/ant (1.8.2)
        JAVA................: /usr/lib64/jvm/java-1.7.0-openjdk-1.7.0/bin/java (1.7.0)
        PYTHON..............: /usr/bin/python (2.7)
        WSDL2C..............: /usr/lib64/axis2c/bin/tools/wsdl2c/WSDL2C.sh

      Locations:
        PREFIX..............: /opt/eucalyptus
        APACHE2_MODULE_DIR..: /usr/lib64/apache2
        AXIS2C_HOME.........: /usr/lib64/axis2c
        AXIS2C_SERVICES.....: /usr/lib64/axis2c/services
        AXIS2_HOME..........: /opt/axis2
        EUCA_DB_HOME........: Not Set
        EUCA_DB_SUFFIX......: Not Set
        JAVA_HOME...........: /usr/lib64/jvm/java-1.7.0-openjdk-1.7.0
        LIBVIRT_HOME........: Not Set
        VDDK_HOME...........: Not Set
    `</pre>
    Those **Not Set** there makes me very panic, I know PostgreSQL is mentioned in INSTALL when compiling under Ubuntu. But not said under CentOS. Just mark is here, I will test it later with DB arguments given.

    ## Java jar path

    Some jar files are needed. After you installed the packages, it will leave jar files under
    <pre>`/usr/share/java
    `</pre>
    To let them known by javac, export CLASSPATH before compilation.
    <pre>`export CLASSPATH=$(build-classpath-directory /usr/share/java)
    `</pre>

    ## Disable git clone

    There is a repo called cloud-libs under github/eucalyptus, it is a directory with a lot of jar files.When first compile eucalyptus, it will git clone from this repo to clc/lib under source directory.
    <pre>`[intijk@Bumblebee.site](mailto:intijk@Bumblebee.site): lib $ ls
    activemq-core-5.4.1.jar                      jetty-continuation-8.1.10.v20130312.jar
    ant-1.7.jar                                  jetty-deploy-8.1.10.v20130312.jar
    antlr-3.0.1.jar                              jetty-http-8.1.10.v20130312.jar
    ...
    `</pre>
    There are 143 jar files with a total size of 109M. Let me just put these jar files into source code for 2 reasons:
    <pre>`1\. OBS do not allow network usage.
    2\. A huge work needed to be done if we compile all of them, and some of them need specific version.
    2.5 :) This is how they did before. ([https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus](https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus))
    `</pre>
    When do the compilation, give this:
    <pre>`make DISABLEGIT=1
    `</pre>
    ant will let clc/lib known by javac, so we don't need to bother CLASSPATH.

    ## Compilation

    After I installed the necessory packages, made all things above, I met the problem below, <span style="text-decoration: line-through;">it also causes the compilation failure of**openSUSE_12.2** and **openSUSE_Factory** on the old version here:</span>

    <span style="text-decoration: line-through;">[https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus](https://build.opensuse.org/package/show/Cloud:Eucalyptus/eucalyptus)</span>

    (The failure here is caused by [https://eucalyptus.atlassian.net/browse/EUCA-4618](https://eucalyptus.atlassian.net/browse/EUCA-4618), and fixed in 3.3.0, a patch provided there for 2.x).

    And still be a problem when I compile the version 3.3.0.1 .
    <pre>`org.codehaus.groovy.control.MultipleCompilationErrorsException: startup failed:
    Compile error during compilation with javac.
    /tmp/groovy-generated-7965685771527083825-java-source/com/eucalyptus/config/ConfigurationMessage.java:10: error: cannot find symbol
    @com.eucalyptus.component.ComponentId$ComponentMessage(value=com.eucalyptus.config.ConfigurationService.class) public class ConfigurationMessage
                             ^
      symbol:   class ComponentId$ComponentMessage
      location: package com.eucalyptus.component
    /tmp/groovy-generated-7965685771527083825-java-source/com/eucalyptus/config/PropertiesMessage.java:10: error: cannot find symbol
    @com.eucalyptus.component.ComponentId$ComponentMessage(value=com.eucalyptus.empyrean.Empyrean$PropertiesService.class) public class PropertiesMessage
                             ^
      symbol:   class ComponentId$ComponentMessage
      location: package com.eucalyptus.component
    2 errors

    1 error

    BUILD FAILED
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/build.xml:118: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/build.xml:108: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/modules/module-inc.xml:175: The following error occurred while executing this line:
    /home/intijk/workspace/eucalyptus-3.3.0.1/clc/modules/module-inc.xml:207: Compilation Failed
    `</pre>
    To solve it, I found the bug report here:
    <pre>`[https://eucalyptus.atlassian.net/browse/EUCA-5455](https://eucalyptus.atlassian.net/browse/EUCA-5455)

This bug report describe exactly what I met, the author says only groovy-all-1.7.2.jar works, current jar from auto git clone is groovy-all-2.1.5.jar. But what wired is that this bug marked as 'solved' in version 3.3.0, I am using the source code of 3.3.0.1, it should not show here.

I manually downloaded groovy-all-1.7.2.jar and substituted groovy-all-2.1.5.jar, compilation success.<!--:-->