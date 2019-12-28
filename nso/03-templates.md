# Baseconfig template

This template will configure below parameters
- domain name
- ntp server
- syslog server
- username fisi

The template models `ios` `iosxr` `nxos` and `junos` syntax

<pre>
admin@ncs(config)# show configuration
devices template baseconf
 ned-id cisco-ios-cli-3.8
  config
   ios:ip domain name basondole.org
   ios:username fisi
    privilege 15
    secret secret fisi123
   !
   ios:logging host 10.10.1.100
   !
   ios:ntp server ip peer-list 10.10.1.100
   !
  !
 !
 ned-id cisco-iosxr-cli-3.5
  config
   cisco-ios-xr:logging host 10.10.1.100
   !
   cisco-ios-xr:domain name basondole.org
   cisco-ios-xr:ntp server server-list 10.10.1.100
   !
   cisco-ios-xr:username fisi
    privilege 15
    secret password fisi123
   !
  !
 !
 ned-id juniper-junos-nc-3.0
  config
   junos:configuration system domain-name basondole.org
   junos:configuration system login user fisi
    class super-user
    authentication plain-text-password-value fisi123
   !
   junos:configuration system syslog host 10.10.1.100
   !
   junos:configuration system ntp server 10.10.1.100
   !
  !
 !
 ned-id cisco-nx-cli-3.0
  config
   nx:username fisi
    password password fisi123
   !
   nx:ip domain-name basondole.org
   nx:ntp server 10.10.1.100
   !
   nx:logging server host 10.10.1.100
  !
 !
!
admin@ncs(config)# commit
</pre>

### Applying the template to devices
```
admin@ncs(config)# devices device-group netsim apply-template template-name baseconf
apply-template-result {
    device netsim-junos-00
    result ok
}
apply-template-result {
    device netsim-nx-00
    result ok
}
apply-template-result {
    device netsim-xr-00
    result ok
}
admin@ncs(config)# show configuration
devices device netsim-junos-00
 config
  junos:configuration system domain-name basondole.org
  junos:configuration system login user fisi
   class super-user
   authentication plain-text-password-value fisi123
  !
  junos:configuration system syslog host 10.10.1.100
  !
  junos:configuration system ntp server 10.10.1.100
  !
 !
!
devices device netsim-nx-00
 config
  nx:username fisi password fisi123
  nx:ip domain-name basondole.org
  nx:ntp server 10.10.1.100
  nx:logging server 10.10.1.100
 !
!
devices device netsim-xr-00
 config
  cisco-ios-xr:logging 10.10.1.100
  cisco-ios-xr:domain name basondole.org
  cisco-ios-xr:ntp
   server 10.10.1.100
  exit
  cisco-ios-xr:username fisi
   privilege 15
  exit
 !
!
admin@ncs(config)# commit dry-run outformat native
native {
    device {
        name netsim-junos-00
        data <rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0"
                  message-id="1">
               <edit-config xmlns:nc="urn:ietf:params:xml:ns:netconf:base:1.0">
                 <target>
                   <candidate/>
                 </target>
                 <test-option>test-then-set</test-option>
                 <error-option>rollback-on-error</error-option>
                 <with-inactive xmlns="http://tail-f.com/ns/netconf/inactive/1.0"/>
                 <config>
                   <configuration xmlns="http://xml.juniper.net/xnm/1.1/xnm">
                     <system>
                       <syslog>
                         <host>
                           <name>10.10.1.100</name>
                         </host>
                       </syslog>
                       <domain-name>basondole.org</domain-name>
                       <login>
                         <user>
                           <name>fisi</name>
                           <class>super-user</class>
                           <authentication>
                             <plain-text-password-value>fisi123</plain-text-password-value>
                           </authentication>
                         </user>
                       </login>
                       <ntp>
                         <server>
                           <name>10.10.1.100</name>
                         </server>
                       </ntp>
                     </system>
                   </configuration>
                 </config>
               </edit-config>
             </rpc>
    }
    device {
        name netsim-nx-00
        data username fisi password fisi123
             ip domain-name basondole.org
             ntp server 10.10.1.100
             logging server 10.10.1.100
    }
    device {
        name netsim-xr-00
        data logging 10.10.1.100
             domain name basondole.org
             ntp
              server 10.10.1.100
             exit
             username fisi
              privilege 15
             exit
    }
}
admin@ncs(config)# commit
Commit complete.
```
