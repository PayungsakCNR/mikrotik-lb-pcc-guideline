1. Accept Network ภายในวิ่งหากันเองโดยไม่ต้องทำ Load balance (Bypass)
/ip firewall mangle add chain=prerouting action=accept src-address-list=NAT dst-address-list=NAT

2. Mark connection ของเเต่ละ WAN 
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan1 passthrough=no in-interface=pppoe-out1
/ip firewall mangle add chain=input action=mark-connection new-connection-mark=conn_wan2 passthrough=no in-interface=pppoe-out2

3. Mark route ตาม WAN Connection ให้ออกทางงที่เข้ามา
/ip firewall mangle add chain=output action=mark-routing new-routing-mark=to_wan1 passthrough=no dst-address-type=!local dst-address-list=!NAT connection-mark=conn_wan1
/ip firewall mangle add chain=output action=mark-routing new-routing-mark=to_wan2 passthrough=no dst-address-type=!local dst-address-list=!NAT connection-mark=conn_wan2

Rule ต่อจากนี้เป็น PCC Rule ตามปกติ

เพิ่มเติม
/ip firewall address-list add list=NAT address=10.200.0.0/16

NAT Address-list คือ Address-List ของ Network ภายในวง LAN ของเรา
