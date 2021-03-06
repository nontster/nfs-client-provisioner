﻿# nfs-client-provisioner

การใช้งาน storage ใน Kubernetes สำหรับข้อมูลที่เป็น persistent data ปกติเรามักนำข้อมูลไปเก็บไว้นอก Kubernetes cluster ซึ่งส่วนใหญ่เป็น network storage

เทคโนโลยี storage ที่เอามาใช้บ่อยที่สุดน่าจะเป็น Network File System (NFS) เนื่องจาก setup ง่ายและรองรับ [access mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) ทุกรูปแบบไม่ว่าจะเป็น ReadWriteOnce ReadOnlyMany หรือ ReadWriteMany

ความไม่สะดวกอันหนึ่งของการใช้ NFS คือ Administrator ต้องมาคอยสร้าง NFS share path ให้ ส่วน Kubernetes Administrator ต้องมาสร้าง PV เพื่อรอให้ PVC มา claim ไปใช้ ซึ่งต้องทำทุก NFS mount point ถ้าเรามี Kubernetes หลาย cluster และมี หลาย Application ทาง Administrator ก็จะต้องยุ่งวุ่นวายกับงานนี้ค่อนข้างมาก

ทางออกหนึ่งที่สามารถช่วยลดภาระของ Administrator ได้คือการใช้ [Kubernetes NFS-Client Provisioner](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner)
การใช้งาน Kubernetes NFS-Client Provisioner จะใช้งานผ่าน [StorageClass](https://kubernetes.io/docs/concepts/storage/storage-classes) ซึ่งต้องระบุ Provisioner ที่จะใช้ในการกำหนดว่า volume plugin ไหนจะถูกใช้สำหรับ provisioning PV

ชื่อของ provisioner สามารถเปลี่ยนได้ตามที่เราต้องการ ตามที่ระบุในตัวแปร PROVISIONER_NAME โดย 1 provisioner จะผูกกับ 1 NFS share path เช่นในกรณีนี้ NFS server อยู่ที่ IP 10.254.254.151 และ share path อยู่ที่ /mnt/nfspool

เราสามารถกำหนดแต่ละ NFS share path ให้กับ 1 application โดยแต่ละ application จะถูก deploy ใน namespace ที่ไม่ซ้ำกัน ดังนั้นเราควรที่จะ deploy Kubernetes NFS-Client Provisioner ตาม namespace ที่เราต้องการใช้งาน โดยอาจตั้งชื่อ provisioner ให้สัมพันธ์กับชื่อ namespace หรือ application แต่จากตัวอย่างนี้จะ deploy Kubernetes NFS-Client Provisioner ใน default namespace ถ้าหากต้องการใช้ namespace อื่นนอกเหนือจาก default namespace ต้องตามไปเปลี่ยนค่าใน ไฟล์ deployment.yaml และ rbac.yaml ด้วย

สำหรับตัวอย่างจะทำการ deploy Kubernetes NFS-Client Provisioner ใน default namespace และตั้งชื่อ StorageClass ว่า managed-nfs-storage และตั้งชื่อ provisioner ว่า nfs-provisioner 
