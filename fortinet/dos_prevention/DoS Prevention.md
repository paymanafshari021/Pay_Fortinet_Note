#dos #ddos #Prevention
# DoS Prevention
## ICMP Flooding DoS Prevention
```
config firewall DoS-policy
    edit 0
        set name "no icmp flooding"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL_ICMP"
        config anomaly
            edit "icmp_flood"
                set status enable
                set log enable
                set action block
                set threshold 250
            next
        end
    next
end
```
### Test
```
sudo hping3 -i u100 -l <Public IP>
```
---
## TCP syn-flood DoS Prevention

Ein **TCP SYN-Flood-Angriff** ist eine **Denial-of-Service (DoS)**-Attacke:

- Der Angreifer schickt sehr viele **SYN-Anfragen** (Verbindungsanfragen) an einen Server.
- Der Server antwortet jeweils mit **SYN-ACK** und wartet auf die Bestätigung (**ACK**).
- Diese Bestätigung kommt **nie**.
- Dadurch bleiben viele **halboffene Verbindungen** bestehen.
- Die Ressourcen des Servers werden blockiert → **legitime Nutzer kommen nicht mehr durch**.
```
config firewall DoS-policy
    edit 0
        set name "no tcp sync-flood"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL_TCP"
        config anomaly
            edit "tcp_syn_flood"
                set status enable
                set log enable
                set action block
                set threshold 10
            next
        end
    next
end
```
### Test
```
sudo hping3 -i u100 -S -p 80 <Public IP>
```
---
## Port Scan Policy
```
config firewall DoS-policy
    edit 0
        set name "disable port scans"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL"
        config anomaly
            edit "tcp_port_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
            edit "udp_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
            edit "sctp_scan"
                set status enable
                set log enable
                set action block
                set threshold 5
            next
        end
    next
end
```
---
## Adding Quarantine
```
config firewall DoS-policy
    edit 4
        set name "disable port scans"
        set interface "<WAN Interface>"
        set srcaddr "all"
        set dstaddr "<Public IP>"
        set service "ALL"
        config anomaly
            edit "tcp_port_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
            next
            edit "udp_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
            next
            edit "sctp_scan"
                set status enable
                set log enable
                set action block
                set quarantine attacker # Quarantine Attacker
                set quarantine-expiry 4m
                set threshold 5
        end
    next
end
```
### Remove Quarantine Attacker
Dashboard > Network > Quarantine

---
## ICMP_Sweep

An **ICMP sweep** (also called a **ping sweep**) is a **network reconnaissance technique** used to discover which IP addresses in a range are **active**.

**How it works:**

- The attacker sends **ICMP Echo Request (ping)** packets to many IP addresses.
- Hosts that are online reply with **ICMP Echo Reply**.
- This reveals which systems are **alive** on the network.

**Purpose:**

- Used for **network mapping** before an attack.
- Can also be used by administrators for **legitimate network discovery**.

**Security impact:**

- Helps attackers identify potential targets.
- Large ICMP sweeps may trigger **IDS/IPS alerts** or be blocked by firewalls.

```
config firewall DoS-policy
    edit 5
        set name "no icmp sweep"
        set interface "<Inside>"
        set srcaddr "all"
        set dstaddr "all"
        set service "ALL"
        config anomaly
            edit "icmp_sweep"
                set status enable
                set log enable
                set action block
                set threshold 20
            next
        end
    next
end
```
### Test
```
fping -g <start-IP> <end-IP> -a
```
---
## ICMP Source Session

**“ICMP Source Session”** ist **kein standardisierter Netzwerkbegriff**. Meist wird damit **eine ICMP-basierte Kommunikation (Session) gemeint, bei der die Source-IP relevant oder manipuliert ist**. Je nach Kontext kann es Folgendes bedeuten:

**1. ICMP mit gefälschter Source-IP (Spoofing)**

- ICMP-Pakete werden mit einer **gefälschten Absenderadresse** gesendet.
- Ziel: **Anonymität**, Umgehung von Filtern oder **Reflexionsangriffe**.
- ICMP selbst ist **verbindungslos**, daher gibt es technisch keine echte „Session“.

**2. ICMP-basierte Kommunikation / Tunneling**

- ICMP wird missbraucht, um **Daten zu übertragen** (z. B. durch Firewalls).
- Sieht wie Ping aus, ist aber eine **verdeckte Session**.

**3. Falsche Bezeichnung**

- Oft wird „Session“ fälschlich benutzt für **ICMP Request/Reply-Austausch** (Ping).

**Kurzfassung:** ICMP hat **keine echten Sessions**. „ICMP Source Session“ meint meist **ICMP-Verkehr mit besonderem Fokus auf die Absenderadresse** (oft Spoofing oder Tunneling).

```
config firewall DoS-policy
    edit 6
        set name "limit icmp source"
        set interface "vlan191"
        set srcaddr "all"
        set dstaddr "all"
        set service "ALL_ICMP"
        config anomaly
            edit "icmp_src_session"
                set status enable
                set log enable
                set action block
                set threshold 50
            next
        end
    next
end
```
### Test
```
fping -g <start-IP> <end-IP> -a
```