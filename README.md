1. Accept Network ภายในวิ่งหากันเองโดยไม่ต้องทำ Load balance (Bypass) <br/>
/ip firewall mangle add chain=prerouting action=accept src-address-list=NAT dst-address-list=NAT <br/>
<br/>
2. Mark connection ของเเต่ละ WAN <br/>
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan1 passthrough=no in-interface=pppoe-out1 <br/>
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan2 passthrough=no in-interface=pppoe-out2 <br/>
<br/>
3. Mark route ตาม WAN Connection ให้ออกทางที่เข้ามา <br/>
/ip firewall mangle add chain=output action=mark-routing new-routing-mark=to_wan1 passthrough=no dst-address-type=!local dst-address-list=!NAT connection-mark=conn_wan1 <br/>
/ip firewall mangle add chain=output action=mark-routing new-routing-mark=to_wan2 passthrough=no dst-address-type=!local dst-address-list=!NAT connection-mark=conn_wan2 <br/>
<br/>
Rule ต่อจากนี้เป็น PCC Rule ตามปกติ <br/>
<br/>
เพิ่มเติม <br/>
/ip firewall address-list add list=NAT address=10.200.0.0/16 <br/>
<br/>
NAT Address-list คือ Address-List ของ Network ภายในวง LAN ของเรา

<br/>

Port Forward Hirpin NAT <br/>
1. Source NAT สำหรับให้ Network ในวง LAN ออกเน็ตได้ <br/>
/ip firewall nat add chain=srcnat action=masquerade src-address-list=NAT out-interface-list=WAN <br/>
<br/> 
2. Port Forward (ตัวอย่างการ Forward Port 8081 โดยให้วิ่งไปยัง 10.200.102.4) <br/>
2.1 /ip firewall nat add chain=srcnat action=masquerade src-address-list=NAT out-interface-list=LAN <br/>
2.2 /ip firewall nat add chain=dstnat action=dst-nat to-addresses=10.200.102.4 to-ports=8081 protocol=tcp in-interface-list=WAN dst-port=8081 <br/>
2.3 /ip firewall nat add chain=dstnat action=dst-nat to-addresses=10.200.102.4 to-ports=8081 protocol=tcp dst-address-type=local src-address-list=NAT dst-port=8081 <br/>

<br/>
เพิ่มเติม  <br/>
จะต้อง Add Interface list ชื่อ WAN เเละ LAN ด้วย <br/>
โดย WAN = Interface สำหรับขาออกเน็ต เช่น pppoe <br/>
LAN คือ Interface ขา LAN ของเรา <br/>
<br/>
