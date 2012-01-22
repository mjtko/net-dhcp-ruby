# main.rb
# 4 de octubre de 2007
#

require 'dhcp'
require 'socket'
require 'pcaplet'

server_thread = Thread.new do
  s = "Packets received by the filter:\n"
  s << '-'*s.size + "\n"
  
  dhcpdump = Pcaplet.new('-s 4096')

  DHCP_PACKET  = Pcap::Filter.new('port 67 or port 68', dhcpdump.capture)

  dhcpdump.add_filter(DHCP_PACKET)
  dhcpdump.each_packet {|pkt|
    msg = DHCP::Message.from_udp_payload(pkt.udp_data)
    
    s << "#{pkt.src}:#{pkt.sport} > #{pkt.dst}:#{pkt.dport}\n"
    s << msg.to_s
    puts s if s
  }
end

discover = DHCP::Discover.new
payload = discover.pack


sckt = UDPSocket.new
sckt.setsockopt(Socket::SOL_SOCKET, Socket::SO_BROADCAST,true)
sckt.bind('', 68)
sckt.send(payload, 0, "<broadcast>", 67)

trap('INT') {server_thread.kill}
server_thread.join