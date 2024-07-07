# Tcpdump CheatSheet 

## Flags
- **-i \<interface\>**: Specifies the network interface.
- **-c \<count\>**: Stops capturing after a specified number of packets.
- **-A**: Displays each packet in ASCII format.
- **-x**: Displays packets in hexadecimal format.
- **-X**: Displays packets in both hex and ASCII formats.
- **-v, -vv, -vvv**: Increases the level of verbosity of the output.
- **-s \<size\>**: Sets the snapshot length for capturing packets.
- **-w \<file\>**: Writes captured packets to a file.
- **-r \<file\>**: Reads captured packets from a file.
- **-n**: Doesn't resolve hostnames.
- **-t**: Doesn't print a timestamp on each dump line.
- **-q**: Less verbose output summary.

## Filters
- **host \<address\>**: Displays packets associated with a specified host.
- **net \<network\>**: Displays packets associated with a specified network.
- **port \<port\>**: Displays packets associated with a specified port.
- **src \<address\>**: Displays packets sent from a specified address.
- **dst \<address\>**: Displays packets sent to a specified address.
- **tcp, udp, icmp**: Displays packets of a specified type.
- **portrange \<start\>-\<end\>**: Displays packets within a specified range of ports.

## Logical Operators
- **and** or **&&**: Displays packets that match both criteria.
- **or** or **||**: Displays packets that match either criterion.
- **not** or **!**: Excludes packets that match the criterion.

## Command Examples Explanation

1. `tcpdump -r file.pcap`
   This command reads packets from the `file.pcap` file. No flags are used, so packets will be displayed with default settings.

2. `tcpdump -r file.pcap -n`
   Adding the `-n` flag prevents hostnames from being resolved to domain names, displaying IP addresses instead.

3. `tcpdump -r file.pcap -ntc 2`
   This command reads the first 2 packets from `file.pcap`, with `-n` disabling hostname resolution, `-t` omitting timestamps, and `-c 2` stopping after 2 packets.

4. `tcpdump -r file.pcap -ntxc 2`
   Reads the first 2 packets from `file.pcap` with hexadecimal output (`-x`), without resolving hostnames (`-n`) and without printing timestamps (`-t`).

5. `tcpdump -r file.pcap -ntvc 2`
   This command is similar to the previous one but adds verbosity (`-v`) to the output.

6. `tcpdump -r file.pcap -ntec 2`
   Increases the verbosity level even more (`-e`), providing extensive details about each packet.

7. `tcpdump -r file.pcap -nvA`
   Reads packets from `file.pcap` with verbose output (`-v`) and displays each packet in ASCII format (`-A`), without resolving hostnames (`-n`).

8. `tcpdump -r file.pcap -X 'dst host 10.10.10.5 and src port 4444'`
   Filters and displays packets with destination host `10.10.10.5` and source port `4444`, showing the output in both hex and ASCII (`-X`).

9. `tcpdump -r file.pcap -vnt 'icmp[0] = 8'`
   Filters for ICMP packets with a type of 8 (echo request), with verbosity (`-v`), no hostname resolution (`-n`), and no timestamps (`-t`).

10. `tcpdump -nt -r file.pcap 'tcp[13] = 0x??'`
    This command filters for TCP packets within `file.pcap` where the 13th byte of the TCP header, which represents the TCP flags, is equal to a specific hexadecimal value (`0x??`). 
    The `tcp[13]` is the offset for the TCP flags within the header, and `0x??` should be replaced with the hexadecimal value of the flag you're interested in. 
    For example, if you want to capture only SYN packets, you would replace `0x??` with `0x02`, as the SYN flag's hexadecimal value is `0x02`.
    This filter is useful for isolating packets that have specific TCP flags set, aiding in the analysis of TCP traffic and troubleshooting network issues.

11. `tcpdump -nt -r file.pcap -c 5 'tcp dst port 22 and tcp[13] & 0x?? = 0x??'`
    This command applies a filter to read the first 5 TCP packets (`-c 5`) from `file.pcap` that are destined for port 22 (`tcp dst port 22`). The filter `tcp[13] & 0x?? = 0x??` is used to check for specific TCP flags in the packet header. 
    The `tcp[13]` refers to the 13th byte of the TCP header, which contains the flag information. The `& 0x??` is a bitwise AND operation with a hexadecimal value that you would replace with the specific flags you're interested in. 
    The `= 0x??` part is used to compare the result of the bitwise AND operation to another hexadecimal value, which should also be replaced with the corresponding flag value you want to match.
    Replace `??` with the hexadecimal value of the TCP flags you want to filter. For example, if you want to match packets with the SYN flag set, you would replace `0x??` with `0x02`, since the SYN flag's hexadecimal value is `0x02`.

12. `tcpdump -r file.pcap 'net 10.10.10 & ip[19] 0xd0 = 0xd0'`
    The first part of the filter expression selects packets located on the 10.10.10.0/24 network. Whereas `& ip[19] 0xd0 = 0xd0` this part of the filter expression performs a bitwise AND on the 20th byte of the IP header with the hexadecimal value `0xd0` and checks whether the result is 0xd0. 
    Finally, the entire filter will search for packets containing the IP address 10.10.10.208

13. `tcpdump -nt -r file.pcap 'udp dst port 53 and udp[10] & 0x80 = 0 and udp[18:2] > 0'`
    This command filters and reads packets from `file.pcap` that are UDP packets with a destination port of 53 (`udp dst port 53`), which is typically used for DNS queries. 
    The filter `udp[10] & 0x80 = 0` checks if the response bit in the DNS header is not set, indicating a query rather than a response. The `udp[18:2] > 0` part checks if the query contains at least one question, as the 18th and 19th bytes of the UDP payload in a DNS packet represent the question count in a DNS query.
    This filter is particularly useful for capturing DNS query packets to analyze network traffic related to domain name resolution.

