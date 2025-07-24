# GLAB
✅ คืออะไร?
GLBP (Gateway Load Balancing Protocol)
เป็นโปรโตคอลที่ใช้สำหรับ แบ่งโหลดและสำรอง Gateway ให้กับผู้ใช้งานในเครือข่ายเดียวกัน (แตกต่างจาก HSRP/VRRP ที่ Active ได้แค่ตัวเดียวในเวลาเดียวกัน)
DHCP (Dynamic Host Configuration Protocol)
เป็นบริการแจก IP address, Default Gateway และ DNS Server ให้อุปกรณ์ในเครือข่ายโดยอัตโนมัติ

🎯 จุดประสงค์ของการตั้งค่านี้
เพื่อให้ 2 Router หรือ Layer 3 Switch ที่เชื่อมต่อในเครือข่ายเดียวกันสามารถ ทำงานร่วมกันเป็น Default Gateway เดียว (192.168.1.1) ผ่าน GLBP
ให้ผู้ใช้สามารถ รับ IP Address และ Default Gateway อัตโนมัติ ผ่าน DHCP
เพื่อให้เกิด การแบ่งโหลด (load balancing) เมื่อมีหลายอุปกรณ์ใช้งานอินเทอร์เน็ต
เพื่อให้เกิด ความต่อเนื่องในการให้บริการ (redundancy) หาก Gateway ตัวใดตัวหนึ่งล่ม

⚠️ เงื่อนไขสำคัญ
อุปกรณ์ทั้งสองต้องอยู่ใน VLAN และ Subnet เดียวกัน (192.168.1.0/24)
ใช้ GLBP Group เดียวกัน (group 1) และใช้ Virtual IP เดียวกัน (192.168.1.1)
มีการกำหนด priority เพื่อให้รู้ว่าใครจะเป็น Active Virtual Gateway ก่อน (110 > 100)
ใช้ preempt เพื่อให้เมื่อ Router ที่มี priority สูงกลับมา จะสามารถแย่งกลับเป็น Active ได้
glbp load-balancing round-robin ช่วยให้มีการกระจายโหลดให้ทุก router ช่วยตอบ ARP

🛠️ การใช้งานจริง
ใช้ในองค์กรที่ต้องการให้ผู้ใช้งานจำนวนมากสามารถเชื่อมต่อออกอินเทอร์เน็ตได้ผ่านหลาย Gateway โดยไม่ต้องระบุ Gateway หลายตัวเอง
ใช้ในระบบที่ต้องการ uptime สูง เช่น data center, โรงเรียน, องค์กรราชการ
รองรับการ failover และ load balancing ไปพร้อมกัน

🔗 การเชื่อมต่อ
ทั้ง 2 Router ต่ออยู่ใน subnet เดียวกัน เช่น ผ่าน Switch
ผู้ใช้งานเชื่อมต่อเข้ากับ Switch หรือ Access Point ที่เชื่อมกับ Router เหล่านี้
DHCP Server อยู่ในตัว Router ตัวที่ตั้ง DHCP (ในที่นี้คือ router ที่มี IP 192.168.1.2)

📈 ผลลัพธ์
ผู้ใช้จะได้รับ IP อัตโนมัติจากช่วง 192.168.1.11 ถึง 192.168.1.254
Default Gateway ของผู้ใช้งานคือ 192.168.1.1 (Virtual Gateway ของ GLBP)
Router ที่มี priority สูงสุด (110) จะเป็น Active Virtual Gateway (AVG)
การตอบ ARP Gateway จะถูกแบ่งให้ทั้ง 2 ตัวช่วยกัน ทำให้ Load Balancing
หาก Router ตัวใดล่ม อีกตัวหนึ่งจะทำหน้าที่แทนทันที

🧠 สรุป
| รายการ                 | รายละเอียด                                                           |
| ---------------------- | -------------------------------------------------------------------- |
| โปรโตคอลหลัก           | GLBP (Load Balancing + Redundancy)                                   |
| Gateway เสมือน         | 192.168.1.1                                                          |
| Gateway จริง           | 192.168.1.2, 192.168.1.3                                             |
| Router ไหน AVG         | ตัวที่มี Priority 110 (คือ 192.168.1.2)                              |
| DHCP Server            | แจก IP จาก 192.168.1.11 - 192.168.1.254                              |
| Default Gateway ผู้ใช้ | 192.168.1.1                                                          |
| ประโยชน์               | ไม่เกิด single point of failure, balance traffic, failover อัตโนมัติ |
