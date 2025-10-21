# NMAP-local-port-scanning

Objective : To perform basic network reconnaissance using **Nmap** and identify open TCP ports in the local VMware network.  
This helps understand how devices expose network services and highlights potential security risks.

Tools used : 
1. VMWare
2. Nmap
3. Wireshark

Steps Performed: 
1. ip a -> to find the network interface and ip
   <img width="1702" height="471" alt="image" src="https://github.com/user-attachments/assets/40f6266b-0a33-462f-9bf9-a3d5b5bbefca" />
2. ip route show default -> to get default gateway and net range
   <img width="1346" height="99" alt="image" src="https://github.com/user-attachments/assets/84f88973-5c57-42b2-ab0a-ed9e33a355a4" />
3. sudo nmap -sn 192.168.182.128/24 -> To quickly discover live hosts in the LAN
   <img width="1277" height="513" alt="image" src="https://github.com/user-attachments/assets/f5944c76-2820-4e04-8cd3-aa52b1893b44" />
4. sudo nmap -sS 192.168.182.0/24 -oN ~/nmap_scan.txt -oX ~/nmap_scan.xml -> performs a stealthy TCP SYN port scan of all 254 possible host addresses in the 192.168.182.0 local network, and saves the results (information about which ports are open, closed, or filtered on each discovered host) into two separate files in the user's home directory: one for human-readable output and one for machine-readable output.
   <img width="771" height="568" alt="image" src="https://github.com/user-attachments/assets/1b623a26-98e2-430f-a38f-af70757419aa" />
5. xsltproc ~/nmap_scan.xml -o ~/nmap_scan.html -> the command takes the raw, machine-readable Nmap XML scan data and converts it into a nicely formatted, user-friendly HTML report using the xsltproc program.

Wireshark snapshot 
<img width="1721" height="642" alt="image" src="https://github.com/user-attachments/assets/cc75112c-6039-4a6c-b32d-14da95c60c0c" />


Summary of Scan: 
IP Address	      MAC Address (VMware)	    Open Ports	    Service	        State / Comment
192.168.182.1	    00:50:56:C0:00:08        	None	            —	            All ports filtered → probably VMware virtual gateway or NAT interface
192.168.182.2    	00:50:56:E6:2C:43	        53/tcp	        Domain (DNS)	  Open → likely VMware DNS service
192.168.182.254  	00:50:56:F8:7F:30	        None	            —	            All ports filtered → another VMware service or virtual network adapter
192.168.182.128	  (My Kali VM)	          None	            —	            All ports closed (local host didn’t respond)

So only 192.168.182.2 has an open port: 53 (DNS).
Open port 53 (DNS) indicates a local DNS resolver or VMware NAT service that helps VM resolve domain names.
Other hosts showing filtered means a firewall is blocking our probes.
our Kali VM showing closed means the system responds to TCP SYN but has no services listening on the default 1000 ports.

common risk: Can be abused for DNS amplification or data exfiltration if exposed to the internet.

Questions: 
1. What is an open port?
   An open port is a network port actively listening for incoming connections or data. It means a service (like SSH, HTTP, etc.) is running and reachable.

2. How does Nmap perform a TCP SYN scan?
   Nmap sends a SYN packet (like starting a handshake).
   If the target responds with SYN-ACK, the port is open.
   Nmap then sends RST to close it (without completing the handshake).
   Hence, it’s called a stealth (half-open) scan.

3. What risks are associated with open ports?
   Attackers can exploit vulnerabilities in services running on open ports.
   Can be used for unauthorized access, data leaks, or malware infection.

4. Difference between TCP and UDP scanning?
    Type        Method	              Reliability	              Example
    TCP Scan	  Uses SYN/ACK	        Very reliable	            nmap -sS
    UDP Scan	  Sends UDP packets 	  Less reliable 	          nmap -sU
                (no handshake)        (no response = ambiguous)

5. How can open ports be secured?
   Close unused ports.
   Use firewall rules.
   Implement port knocking or VPN access.
   Regularly patch exposed services.

6. What is a firewall’s role regarding ports?
   A firewall controls which ports are open or closed to incoming/outgoing traffic.
   It acts as a barrier between the internal network and external threats.

7. What is a port scan and why do attackers perform it?
   A port scan systematically checks which ports on a host are open.
   Attackers perform it to discover vulnerabilities or services they can exploit.

8. How does Wireshark complement port scanning?
   Wireshark captures and visualizes packets sent/received during a scan.
   It helps verify:
   How targets respond (SYN-ACK, RST, etc.)
   Scan accuracy
   Network security monitoring in real-time.


**Conclusion**
The local network was successfully scanned using Nmap.
Only one host (192.168.182.2) had an open DNS port (53/tcp).
This demonstrates a properly isolated virtual network with minimal exposure and provides foundational experience in network reconnaissance and service enumeration.

*Author: Navya Nandika*
*Environment: Kali Linux (VMware)*
*Date: October 21, 2025*
