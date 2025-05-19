# Wireshark: Network Traffic Analysis - SYN Flood DoS Attack

**Scenario:**

>   *This scenario is based on a fictional company:*
>
>   As a security analyst for a travel agency, I received an automated alert indicating a web server issue. Upon attempting to access the company website, I encountered a connection timeout. Using a packet sniffer, I observed a flood of TCP SYN requests from an unknown IP, indicating a likely denial-of-service (DoS) attack impacting employee access to the sales webpage. I temporarily took the server offline and blocked the attacking IP, but recognizing this is a short-term solution due to potential IP spoofing, I need to immediately alert my manager to discuss further mitigation and prevention strategies. I'll explain the SYN flood attack and its impact.

## Network Traffic Analysis

**Part 1: Summary of the Problem Found in the Network Traffic Log**

The [Wireshark log](https://docs.google.com/spreadsheets/d/1BClpD8lkzDfOgLNSbvtnxLuEaKi8LgKMXV_vWDqOWIw/edit?usp=sharing) clearly demonstrates a SYN flood DoS attack. The log is filled with numerous TCP SYN packets, with the vast majority originating from the IP address `203.0.113.0`, attempting to initiate TCP connections with the web server on port `443`.

![image](https://github.com/user-attachments/assets/30ae5bc3-b227-4faf-8e69-5bf79ad41483)

A SYN flood attack exploits the TCP handshake process. In a normal TCP connection, the client sends a SYN packet, the server responds with a SYN-ACK packet, and the client completes the handshake with an ACK packet. However, in a SYN flood, the attacker sends a large number of SYN packets but does not complete the handshake. The server allocates resources for each connection attempt, and by sending many SYN packets without completing the handshake, the attacker quickly exhausts the server's resources, preventing it from responding to legitimate connection attempts.

The log shows the server sending SYN-ACK packets in response to the SYN floods, but it also shows numerous RST (reset) packets, indicating connections that were not properly established or were reset by the server due to being overwhelmed. The HTTP `504 Gateway Time-out` errors further confirm the server's inability to handle the traffic load under the sustained barrage of SYN requests from `203.0.113.0`.

**Part 2: Analysis of the Data and Cause of the Incident**

* **Time of Incident:** The incident occurred during the afternoon, as indicated by the initial alert and the timeline within the Wireshark log (starting around 3 seconds into the capture).
* **How the IT Team Became Aware:** The IT team became aware of the incident due to an automated alert from the monitoring system and subsequent reports of the website being unreachable.
* **Actions Taken by the IT Department:** The IT department used a packet sniffer (Wireshark) to capture and analyze network traffic to and from the web server.
* **Key Findings:** The analysis revealed a large number of TCP SYN requests, indicative of a SYN flood attack, directed at the web server on port `443`. The server was unable to handle the volume of requests, leading to connection timeouts and unresponsiveness.
* **Likely Cause:** The most likely cause of this incident is a Distributed Denial of Service (DDoS) attack, specifically a SYN flood.

## Detailed Explanation

* **Network Attacks Overview:** Network attacks encompass various malicious activities targeting network infrastructure, devices, and communication. These attacks aim to disrupt operations, steal data, or gain unauthorized access. Examples include denial-of-service (DoS) attacks, malware distribution, and unauthorized access attempts.
* **Likely Attack Type:** The symptoms described in the scenario (website timeout, large number of SYN requests) strongly suggest a Denial of Service (DoS) attack, specifically a SYN flood. The consistent and overwhelming source IP address `203.0.113.0` for the attack traffic confirms this.
* **DoS vs. DDoS:** A Denial of Service (DoS) attack involves a single attacker overwhelming a target with malicious traffic, making it unavailable to legitimate users. A Distributed Denial of Service (DDoS) attack is a type of DoS attack where the malicious traffic comes from multiple sources simultaneously, making it harder to block and mitigate. In this incident, while there might be a few initial SYN packets from other IPs, the sustained and dominant flood from `203.0.113.0` clearly points to a DoS attack.
* **Website Unresponsiveness:** The website was slow or unresponsive due to the overwhelming number of SYN requests from `203.0.113.0` consuming the server's resources. The server could not process legitimate requests, leading to timeouts.

## Analysis of the Attack

The Wireshark log clearly demonstrates a SYN flood DoS attack. The log is filled with numerous TCP SYN packets, with the vast majority originating from the IP address `203.0.113.0`, attempting to initiate TCP connections with the web server on port `443`.

A SYN flood attack exploits the TCP handshake process. In a normal TCP connection, the client sends a SYN packet, the server responds with a SYN-ACK packet, and the client completes the handshake with an ACK packet. However, in a SYN flood, the attacker sends a large number of SYN packets but does not complete the handshake. The server allocates resources for each connection attempt, and by sending many SYN packets without completing the handshake, the attacker quickly exhausts the server's resources, preventing it from responding to legitimate connection attempts.

The log shows the server sending SYN-ACK packets in response to the SYN floods, but it also shows numerous RST (reset) packets, indicating connections that were not properly established or were reset by the server due to being overwhelmed. The HTTP `504 Gateway Time-out` errors further confirm the server's inability to handle the traffic load under the sustained barrage of SYN requests from `203.0.113.0`.

## Impact and Consequences

This DoS attack significantly impacted the travel agency's operations:

* **Website Unavailability:** The primary impact was the inaccessibility of the company's website, preventing employees from accessing sales information and hindering customer interactions.
* **Loss of Productivity:** Employees were unable to perform their job functions effectively, leading to lost productivity.
* **Reputational Damage:** Prolonged website unavailability can damage the company's reputation and erode customer trust.
* **Potential Revenue Loss:** Inability to access or conduct online sales can lead to direct revenue loss.

## Security Recommendations

To mitigate and prevent such attacks in the future, the travel agency can consider the following:

* **Firewall and Intrusion Prevention Systems (IPS):** Implement robust firewalls and IPS to detect and filter malicious traffic, including the ability to identify and block SYN flood attacks from specific source IPs.
* **Rate Limiting:** Configure network devices to limit the number of connections from a single source IP address within a specific timeframe. This would be highly effective against a DoS attack from a primary IP like `203.0.113.0`.
* **SYN Cookies:** Utilize SYN cookies to help the server handle SYN flood attacks more effectively, even from a single, persistent source.
* **Network Monitoring:** Implement continuous network monitoring to detect anomalies and potential attacks early, paying close attention to high volumes of SYN requests from single IPs.
* **Ingress Filtering:** Implement strict ingress filtering at the network perimeter to block traffic from suspicious or unexpected source IP ranges.
