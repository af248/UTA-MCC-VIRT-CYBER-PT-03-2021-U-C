## Networks Fundamentals II Homework: *In a Network Far, Far Away!*

### Background

- You are a Network Jedi working for the Resistance.

- The Sith Empire recently carried out a DoS attack, taking out the Resistance's core network infrastructure, including its DNS servers.

- This attack destroyed the Resistance's ability to communicate via email and retrieve other crucial information about each others' operations. The Empire has taken advantage of this compromised availability by ambushing numerous Resistance outposts, all vulnerable because they can no longer call for help.
 
- Your task is a crucial one: Restore the Resistance's core DNS infrastructure and verify that traffic is routing as expected.

**Good luck and may the Force be with you!**

### Files Required 

-  [Darkside pcap](resources/Darkside.pcap)
-  [Galaxy Network Map](resources/Galaxy_Network_map.png)


### Your Objectives: 

- Review each network issue in the missions below.

- Document each DNS record type found.

- Take note of the DNS records that can explain the reasons for the existing network issue.

- Provide recommended fixes to save the Galaxy!

Submit your results and findings in a document. 

### Topics Covered in Your Assignments

- DNS
- NSLOOKUP
- DNS record types
  - `A`, `PTR`, `MX`, `NS`, `SOA`, `SRV`, `TXT`
- Wireless
  - `WEP`, `WPA`
- Aircrack-ng
- Wireshark Wireless analysis and decryption
  
---

### Mission 1  
**Issue**: Due to the DoS attack, the Empire took down the Resistance's DNS and primary email servers. 

Check MX records for `starwars.com` using the following command:

```bash
nslookup -type=MX starwars.com
```

Results of the `nslookup` command:

![mission1-nslookup](./mission1-nslookup.png)

The mail servers configured for `starwars.com` are as follows:

* `aspmx3.googlemail.com`
* `aspmx.l.google.com`
* `aspmx2.googlemail.com`
* `alt2.aspmx.l.google.com`
* `alt1.aspx.l.google.com`

The resistance is not receiving emails because neither the new primary mail
server (`asltx.l.google.com`) nor the secondary server (`asltx.2.google.com`)
are configured in the DNS record.

Recommend configuring the DNS records as follows to fix email issues for the
resistance:

```bash
starwars.com    mail exchanger = 1 asltx.l.google.com.
starwars.com    mail exchanger = 5 asltx.2.google.com.
```

### Mission 2

**Issue**: Now that you've addressed the mail servers, all emails are coming through. However, users are still reporting that they haven't received mail from the `theforce.net` alert bulletins.

To determine the `SPF` record for `theforce.net`, run the following command:

```bash
nslookup -type=TXT theforce.net
```

Results of the `nslookup` command:

![mission2-nslookup](./mission2-nslookup.png)

The text record containing the SPF record is as follows:

```bash
theforce.net    text = "v=spf1 a mx mx:smtp.secureserver.net include:aspmx.googlemail.com ip4:104.156.250.80 ip4:45.63.15.159 ip4:45.63.4.215"
```

The above SPF record indicates authorized emails coming from `theforce.net`
should only come from the following IP addresses:

* `104.156.250.80`
* `45.63.15.159`
* `45.63.4.215`

SPF records list all authorized hostnames and IP addresses that are permitted to
send email on behalf of a given domain.
Since the IP address supplied in the instructions (`45.23.176.21`) is not in the
SPF record, most spam filters will flag emails coming from `45.23.176.21` with
the sender `theforce.net` as spam.

A corrected SPF record for `theforce.net` should read as follows, when returned
from the above `nslookup` command:

```bash
theforce.net    text = "v=spf1 a mx mx:smtp.secureserver.net include:aspmx.googlemail.com ip4:45.23.176.21"
```

  
### Mission 3

**Issue**: You have successfully resolved all email issues and the resistance can now receive alert bulletins. However, the Resistance is unable to easily read the details of alert bulletins online. 

To determine how a correct `CNAME` record should look using `nslookup`, run one
of the following commands (depending on if one wants the entire record, or only
the `CNAME` record):

```bash
nslookup www.theforce.net
nslookup -type=CNAME www.theforce.net
```

Results of the above `nslookup` command:

![mission3-nslookup](./mission3-nslookup.png)

The relevant portion of the result of the command is:

```bash
www.theforce.net        canonical name = theforce.net.
```

To determine the current DNS records for `resistance.theforce.net`, run the
following command:

```bash
nslookup resistance.theforce.net
```

Results of the above `nslookup` command:

![mission3-nslookup2](./mission3-nslookup2.png)

The subdomain `resistance.theforce.net` is not redirecting to `theforce.net`
because there is no DNS record specified for `resistance.theforce.net`. To
ensure `resistance.theforce.net` redirects to `theforce.net`, a proper DNS
`CNAME` record should read as follows when returned from the above `nslookup`
command:

```bash
resistance.theforce.net        canonical name = theforce.net.
```
  
### Mission 4

**Issue**: During the attack, it was determined that the Empire also took down the primary DNS server of `princessleia.site`. 

To confirm the DNS records for `princessleia.site`, run the following commands:

```bash
nslookup princessleia.site
nslookup -type=NS princessleia.site
```

The first command checks the IP address of the site, while the second command
checks what nameservers are being used to provide the information returned from
the first command.

Results of the above `nslookup` commands:

![nslookup-princessleia](./nslookup-princessleia.png)

`princessleia.site` is currently using the following nameservers to provide DNS
information on the site:

* `ns25.domaincontrol.com`
* `ns26.domaincontrol.com`

The provided DNS server at `ns2.galaxybackup.com` is not in the above record.
A properly-configured DNS record would read as follows, when returned from the
`nslookup -type=NS princessleia.site`:

```bash
princessleia.site       nameserver = ns2.galaxybackup.com.
```

    
  
### Mission 5

**Issue**: The network traffic from the planet of `Batuu` to the planet of  `Jedha` is very slow.  

The shortest path to go from Batuu to Jedha, while avoiding Planet N, is as
follows:

```
Shortest path for OSPF -N:
Batuu -> D -> C -> G -> O -> R -> Q -> T -> V -> Jedha
```

The route takes a total of 23 hops, and does not include Planet N.

### Mission 6

**Issue:** Due to all these attacks, the Resistance is determined to seek revenge for the damage the Empire has caused. 

To determine the wireless key used to decrypt traffic for the
[Darkside.pcap](./Darkside.pcap) file, run the following command (assuming
`Darkside.pcap` is in the current working directory):

```bash
aircrack-ng Darkside.pcap -w /usr/share/wordlists/rockyou.txt
```

Running the above command yielded the following results:

![aircrack-darkside](./aircrack-darkside.png)

Running `aircrack-ng` returned the password `dictionary`, which can be used to
decrypt the wireless traffic. Analysis of the `.pcap` file indicates the
wireless access point ("WAP") used by the Dark Side has the SSID `linksys`.

Further analysis of the decrypted `ARP` traffic yields the following results:

* `ARP` requests are originating from the IP address `172.16.0.101`, and come
  from an NIC with the mac address `00:13:ce:55:98:ef`.
* An `ARP` reply indicating which computer is using the IP address `172.16.0.1`
  indicates that IP address is being used by a machine with the mac address
  `00:0f:66:e3:e4:01`.

In summary, the following mac address / IP pairs can be found on the `linksys`
network used by the Dark Side:

* `00:13:ce:55:98:ef` => `172.16.0.101`
* `00:0f:66:e3:e4:01` => `172.16.0.1`

### Mission 7 

As a thank you for saving the galaxy, the Resistance wants to send you a secret message!

To view the TXT DNS record for the record used in Mission
4 (`princessleia.site`), run the following command:

```bash
nslookup -type=TXT princessleia.site
```

Results of the above `nslookup` command:

![princessleia-txt](./princessleia-txt.png)

The hidden message in the `TXT` record indicates we should run the following
command to receive our reward:

```bash
telnet towel.blinkenlights.nl
```

Running the above command connects to a telnet server, which plays an ASCII art
animation of _A New Hope_, as documented below.

![ascii-a-new-hope](./ascii-a-new-hope.png)
    
### Conclusion

- Submit your results and findings from every mission.

- Congratulations, you have completed your mission and saved the Galaxy!

---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.        
  
