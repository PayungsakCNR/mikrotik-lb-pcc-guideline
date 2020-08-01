1. Accept Network ภายในวิ่งหากันเองโดยไม่ต้องทำ Load balance (Bypass) <br/>
/ip firewall mangle add chain=prerouting action=accept src-address-list=NAT dst-address-list=NAT <br/>
<br/>
2. Mark connection ของเเต่ละ WAN <br/>
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan1 passthrough=no in-interface=pppoe-out1 <br/>
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan2 passthrough=no in-interface=pppoe-out2 <br/>
<br/>
3. Mark route ตาม WAN Connection ให้ออกทางงที่เข้ามา <br/>
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
