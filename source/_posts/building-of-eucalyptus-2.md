---
title: Building of Eucalyptus 2
tags:
  - Eucalyptus
  - openSUSE
id: 1324
categories:
  - GSOC2013
date: 2013-08-02 04:10:02
---

After try and error, I got a working compilation result on [https://build.opensuse.org/project/show/home:intijk:eucalyptus](https://build.opensuse.org/project/show/home:intijk:eucalyptus)

My next works are:

1.  Split the eucalyptus to several subpackages.
2.  Test the package on my 2 PCs cluster. Figure out how the service working.
3.  Complete **%pre %post %postun %preun** part in specfile to control the service.
Below are some problems I met before I reach this step.

## Packages belong to Require, not BuildRequire:

    swig
    python-boto
    perl-Crypt-OpenSSL-RSA
    perl-Crypt-OpenSSL-Random
    drbd
    euca2ools
    bitstream-vera-fonts
    PyGreSQL
    tgt
    kvm
    dhcp-server
    xalan-j2-xsltc
    `</pre>

    ## Use smp to speed up the compilation

    <pre>`make %{?_smp_mflags}
    `</pre>

    ## Another BuildRequire

    <pre>`libopenssl-devel
    `</pre>

    ## Layer Cloud:Eucalyptus as dependency:

    <pre>`&lt;project name="home:intijk:eucalyptus"&gt;
      &lt;title&gt;Eucalyptus&lt;/title&gt;
      &lt;description&gt;Eucalyptus&lt;/description&gt;
      &lt;person userid="intijk" role="bugowner"/&gt;
      &lt;person userid="intijk" role="maintainer"/&gt;
      &lt;repository name="openSUSE_Factory"&gt;
        &lt;path project="openSUSE:Factory" repository="snapshot"/&gt;
        &lt;path project="Cloud:Eucalyptus" repository="openSUSE_Factory"/&gt;
        &lt;arch&gt;i586&lt;/arch&gt;
        &lt;arch&gt;x86_64&lt;/arch&gt;
      &lt;/repository&gt;
      &lt;repository name="openSUSE_12.3"&gt;
        &lt;path project="Cloud:Eucalyptus" repository="openSUSE_Factory"/&gt;
        &lt;path project="openSUSE:12.3" repository="standard"/&gt;
        &lt;arch&gt;i586&lt;/arch&gt;
        &lt;arch&gt;x86_64&lt;/arch&gt;
      &lt;/repository&gt;
    &lt;/project&gt;
    `</pre>
    Notice the
    <pre>`&lt;path project="Cloud:Eucalyptus" repository="openSUSE_Factory"/&gt;
    `</pre>
    In my project, I add the existed Cloud:Eucalyptus openSUSE_Factory repo as a dependency, it is mainly for the axis2 and axis2c, I think in the future, the work can be merged together and then the layering can be removed.

    ## A Linking Order Problem:

    <pre>`gcc -rdynamic  -g -O2 -D_LARGEFILE64_SOURCE  -Wall -Wno-unused-variable -fPIC -DHAVE_CONFIG_H -std=gnu99 -I. -I.. -Igenerated -I/home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/storage -I/home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/node -I/home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/util -I/home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/net  -I/usr/include/axis2-* -I/usr/include/rampart-* -D_UNIT_TEST vbr.c -o test_vbr iscsi.o blobstore.o walrus.o http.o diskutil.o       ../util/hash.o ../util/log.o ../util/misc.o ../util/euca_string.o ../util/ipc.o ../util/euca_auth.o ebs_utils.o storage-controller.o -L/usr/lib64/axis2c/modules/rampart -L/usr/lib64/axis2c/lib    -lcurl -lssl -lcrypto -pthread -lpthread  -lxml2 -lz -lrt -lpthread -lneethi -lmod_rampart -lm -laxutil -laxis2_parser -lguththila -laxis2_http_sender -laxis2_http_receiver -laxis2_http_common -laxis2_engine -laxis2_axiom    -pthread -L/usr/lib64/axis2c/modules/rampart -L/usr/lib64/axis2c/lib    -lcurl -lssl -lcrypto -lrampart ../util/euca_axis.o sc-client-marshal-adb.o ../util/fault.o generated/*.o ../util/utf8.o ../util/wc.o
    [  150s] vbr.c: In function 'disk_creator':
    [  150s] vbr.c:1156:9: warning: variable 'root_part' set but not used [-Wunused-but-set-variable]
    [  152s] ../util/euca_axis.o: In function `verify_addr_hdr_elem_loc':
    [  152s] /home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/util/euca_axis.c:526: undefined reference to `axiom_node_get_parent'
    [  152s] /home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/util/euca_axis.c:528: undefined reference to `axiom_util_get_localname'
    [  152s] /home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/util/euca_axis.c:528: undefined reference to `axutil_strcmp'
    `</pre>
    Thanks for Cristian Rodríguez reply my question in opensuse-packaging:
    The linking order is the problem, so I made a patch called **_compileSequence.patch_**, problem solved.

    ## DISABLEGIT in make_install

    <pre>`%make_install DISABLEGIT=1
    `</pre>
    When run to %install part, the macro %make_install will check the git under clc/ directory again, so here we use DISABLE=1 again to stop it.

    ## Add classpath as BuildRequire:

    <pre>`Exception in thread "main" java.lang.NoClassDefFoundError: java/lang/Object
    `</pre>
    Eh... After several hours try and error, I found just to add 'BuildRequire: classpth' :-(

    ## %ghost /var/log and /var/run

    <pre>`[  803s] eucalyptus.x86_64: E: non-ghost-in-var-run (Badness: 1000) /var/run/eucalyptus/net
    [  803s] eucalyptus.x86_64: E: non-ghost-in-var-run (Badness: 1000) /var/run/eucalyptus
    [  803s] A file or directory in the package is located in /var/run. Files installed in
    [  803s] this directory should be marked as %ghost and created at runtime to work
    [  803s] properly in tmpfs /var/run setups.
    `</pre>
    Although no %ghost specfile complition success on my local rpmbuild, obs insist on give /var/run and /var/log as %ghost, so just:
    <pre>`%ghost %{_localstatedir}/log/eucalyptus
    %ghost %{_localstatedir}/run/eucalyptus
    %ghost %{_localstatedir}/run/eucalyptus/net
    `</pre>
    The compilation success after I did the modification above. Now rpm packages with i586 and x86_64 on repo openSUSE_12.3 and openSUSE_Factory can be found here:
    <pre>`[https://build.opensuse.org/project/repositories/home:intijk:eucalyptus](https://build.opensuse.org/project/repositories/home:intijk:eucalyptus)
    `</pre>
    But I didn't test it now, My next works are:

1.  Split the eucalyptus to several subpackages.
2.  Test the package on my 2 PCs cluster. Figure out how the service working.
3.  Complete %pre %post %postun %preun part in specfile to control the service.

    ## groovy-all-1.7.2.jar problem

    It is mentioned in (Building of Eucalyptus 1), still a problem now, I contacted **Matt Spaulding** who report the bug, he says this bug already fixed in developing version on github and will be relase in version 3.4.0, I am using 3.3.0.1 now. Currently I think just keep 1.7.2.jar there, I will catch up the 3.4.0 version from upstream, and it can be solved that time.

    ## A problem about %{_libexecdir}

    As /usr/lib/rpm/macros told me,
    <pre>`%{_libexecdir} 
    `</pre>
    is /usr/libexec, but it just failed when I use it on obs. So I have to change to
    <pre>`%{_prefix}/libexec
    `</pre>
    Why _libexecdir cannot pass?

    ## Some warnings:

    Below are some warnings I saw, I don't know if they will cause some problems, so just record them here.
    <pre>`do-jar:
    Cannot find annotation method 'name()' in type 'javax.persistence.PersistenceContext': class file for javax.persistence.PersistenceContext not found
    Cannot find annotation method 'name()' in type 'javax.persistence.Column': class file for javax.persistence.Column not found
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    Cannot find annotation method 'name()' in type 'javax.persistence.Column'
    `</pre>

    * * *

    <pre>`[  634s] Buildfile: /home/abuild/rpmbuild/BUILD/eucalyptus-3.3.0.1/clc/build.xml
    [  635s] impossible to define new type: class not found: org.apache.ivy.plugins.signer.bouncycastle.OpenPGPSignatureGenerator in [] nor Ivy classloader
    [  635s] :: Ivy 2.2.0-local-20130128063722 - 20130128063722 :: [http://ant.apache.org/ivy/](http://ant.apache.org/ivy/) ::
    [  635s] :: loading settings :: url = jar:file:/usr/share/java/ivy-2.2.0.jar!/org/apache/ivy/core/settings/ivysettings.xml
    `</pre>

    * * *

    <pre>`[  713s] ... testing for pre/postinstall scripts that are not idempotent
    [  713s] mount: none is already mounted or /proc busy
    [  713s] ... running 08-check-permissions
    [  713s] ... testing for modified permissions
    [  722s] ... running 09-check-packaged-twice
    [  722s] ... running 10-check-lanana
    [  722s] warning: Failed to read auxiliary vector, /proc not mounted?
    [  722s] warning: Failed to read auxiliary vector, /proc not mounted?
    [  722s] warning: Failed to read auxiliary vector, /proc not mounted?
    [  722s] warning: Failed to read auxiliary vector, /proc not mounted?
    [  722s] warning: Failed to read auxiliary vector, /proc not mounted?