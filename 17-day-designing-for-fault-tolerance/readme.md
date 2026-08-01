# Designing for fault tolerance

## This chapter covers

* **What fault-tolerance is and why you need it** (Fault tolerance kya hoti hai aur aap ko is ki kyun zaroorat hai)
* **Using redundancy to remove single points of failure** (Redundancy ka istemal karke Single Points of Failure ko khatam karna)
* **Improving fault tolerance by retrying on failure** (Kharabi aane par retries ki madad se fault tolerance ko behtar banana)
* **Using idempotent operations to retry on failure** (Kharabi par bina kisi double effect ke retries ke liye Idempotent operations ka istemal karna)
* **AWS service guarantees** (AWS ki mukhtalif services ki resilience aur availability ki guarantees)

---

### Failure Is Inevitable: Build for Failure

Kainat ka usool hai ke hardware toot sakta hai, network cut sakta hai, aur electricity band ho sakti hai. Software ki dunya mein bhi hard disks ka kharab hona, cables ka tootna ya power supply ka jana aik aam baat hai. Lekin ek zabardast system ka matlab yeh hota hai ke **andar chahe kitni bhi kharabi aaye, bahar baithe user ko pata tak na chale**.

Aik **Fault-Tolerant System** aap ke users ko sab se behtareen quality aur experience deta hai. Aap ke system ke pichay chahe koi server phat jaye ya network down ho jaye, user bilkul be-khauf hokar apna kaam karta rehta hai, videos dekhta rehta hai, online shopping karta hai ya doston se baatein karta rehta hai.

Purane zamane mein (kuch saal pehle) aisa system banana bohot mehnga aur azaab ka kaam hota tha kyunki aap ko duplicate physical servers khareed kar rakhne parhte thay. Lekin **AWS Cloud** ki wajah se ab fault-tolerant systems banana sasta aur ek aam standard ban chuka hai. Phir bhi, cloud computing mein fault-tolerant systems design karna sab se uonchi category (top tier) ka kaam hai aur shuruat mein yeh thoda challenging ho sakta hai.

Fault tolerance ke liye design karne ka matlab hai ke **pehle se yeh maan kar chalna ke har cheez kharab hogi (Build for Failure)** aur system ko aisa banana ke wo kharabi aane par khud hi usay **automatically theek (Self-Heal)** kar le.

---

### Core Architectural Concepts (Pehlu)

#### 1. Avoiding Single Points of Failure (SPOF)

* **SPOF Kya Hai?** Aisa samjhein ke ek cycle ka aik hi chain hai. Agar wo chain toot jaye toh poori cycle ruk jati hai. System mein bhi agar koi aik aisa component ho jiske kharab hone se poora system baith jaye, toh usay Single Point of Failure kehte hain. Fault tolerance ka pehla usool hai ke SPOF ko har keemat par khatam kiya jaye.

#### 2. Redundancy (Duplication)

* Redundancy ka matlab hai **ek ki bajaye ek se zyada ek jaisay components lagana**.
* *Misaal:* Agar aap gaadi mein lambay safar par ja rahe hain, toh aap aik extra spare tyre (stepney) sath rakhte hain. Agar ek tyre puncture ho jaye, toh doosra kaam aata hai. EC2 mein hum apni app ko sirf ek single machine par chalane ke bajaye **multiple machines (fleet)** par baant (distribute) dete hain.

#### 3. Decoupling (Components ko Azaad Karna)

* Architecture ke hissno ko ek doosre se is tarah alag (decouple) karna ke ek hissa agar down bhi ho jaye, toh doosra hissa rukay nahi balkey chalta rahay.
* *Writer ki Example:* Agar aap ka **Database down** ho chuka hai, tab bhi aap ka **Web Server** crash hone ki bajaye user ko purana saved (cached) content dikha de taake screen blank na ho.

---

### AWS Resilience Categories

**Resilience (Muqabla Karne Ki Salahiyat):** Kharabi ka muqabla karne ki aisi salahiyat jisse user par zero ya bohot kam asar pare.

AWS ki tamam services ko un ki resilience aur failure handling ke lehaz se 3 mukhtalif darjo (categories) mein baanta gaya hai:

| Resilience Level | Kharabi Aane Par Kya Hota Hai? | User Par Asar | Recovery Time |
| --- | --- | --- | --- |
| **No Guarantees (SPOF)** | Service mukammal taur par kaam karna band kar deti hai. | Request fail ho jati hai, app band ho jati hai. | Manual intervention zaroori hai. |
| **High Availability (HA)** | Service mein chota sa jhatka lagta hai, lekin system khud ko recover kar leta hai. | Thoda sa interruption/delay aa sakta hai. | Kuch seconds se 1 minute. |
| **Fault Tolerant (FT)** | System ke andar kharabi aane ke bawajood service bilkul pehle ki tarah chalti rehti hai. | **Zero Asar!** User ko pata tak nahi chalta. | Immediate (Instant smooth failover). |

> **Zaroori Mashwara:** Apne system ko fault-tolerant banane ka sab se aasan tareeqaa yeh hai ke aap shuru se hi sirf un AWS services ko use karein jo **by default Fault Tolerant** hain. Agar aap ke tamam building blocks fault tolerant honge, toh aap ka poora system automatically fault tolerant ban jayega.

---

### AWS Services Classification Breakdown

Writer ne AWS ki tamam key services ko un ki resilience capability ke hisab se alag alag divide kiya hai:

#### Category 1: Neither Highly Available nor Fault Tolerant (Single Point of Failure)

Jab aap in services ko akele use karte hain, toh aap apne infrastructure mein SPOF add kar rahe hote hain:

* **Single Amazon EC2 Instance:** Single Virtual Machine kisi bhi waqt hardware issue, network fail, ya Availability Zone (AZ) outage ki wajah se band ho sakti hai.
* *Solution:* Single instance ke bajaye **Auto Scaling groups** ke zariye EC2 instances ka redundant fleet banayein.


* **Single Amazon RDS Instance:** Single database instance bhi EC2 ki tarah crash ho sakta hai.
* *Solution:* High Availability hasil karne ke liye **Multi-AZ mode** enable karein.



#### Category 2: Highly Available (HA) by Default

Yeh services kharabi ke waqt thoda sa downtime leti hain lekin khud hi recover ho jati hain:

* **Elastic Network Interface (ENI):** Network interface ek specific AZ se bound hota hai. Agar wo AZ down ho jaye toh ENI bhi unavailable ho jata hai. Lekin chote outage mein aap ENI ko kisi doosri virtual machine ke sath dobara attach kar sakte hain.
* **Amazon VPC Subnet:** Subnet ek AZ tak mehdood hoti hai. Agar AZ down hua toh subnet tak pohnch band ho jayegi. Is se bachne ke liye multiple AZs mein subnets banayi jati hain.
* **Amazon EBS Volume:** EBS volume ka data ek AZ ke andar multiple storage systems par duplicate hota hai. Lekin agar poora AZ hi fail ho jaye toh volume unavailable ho jayega (data safe rehta hai). Is se bachne ke liye periodic **EBS Snapshots** liye jaate hain taake kisi doosre AZ mein volume recreate kiya ja sake.
* **Amazon RDS Multi-AZ Instance:** Jab RDS Multi-AZ mode mein chalta hai, toh master database fail hone par standby database active hota hai aur DNS records change hone mein lagbhag **1 minute ka chota sa downtime** ata hai.

#### Category 3: Fault Tolerant (FT) by Default

In services ko use karte waqt aap ko kisi failure ka pata tak nahi chalta:

* **Elastic Load Balancing (ELB)** (Kam az kam 2 AZs mein deployed ho)
* **Amazon EC2 Security Groups**
* **Amazon VPC** (Network ACL aur Route Table ke sath)
* **Elastic IP Addresses (EIP)**
* **Amazon Simple Storage Service (S3)**
* **Amazon EBS Snapshots**
* **Amazon DynamoDB**
* **Amazon CloudWatch**
* **Auto Scaling Groups**
* **Amazon Simple Queue Service (SQS)**
* **AWS CloudFormation**
* **AWS Identity and Access Management (IAM)** (Yeh global service hai; ek jagah user banao toh poore dunya ke regions mein milta hai).

---

## Chapter requirements

Is chapter ke practical aur theoretical concepts ko samajhne ke liye aap ko pehle se in baato ka pata hona zaroori hai:

* **Amazon EC2** (Virtual Machines ki basics)
* **Auto Scaling** (Instances ko automatically kam ya zyada karna)
* **Elastic Load Balancing (ELB)** (Traffic ko distribute karna)
* **Simple Queue Service (SQS)** (Messages aur tasks ko queue mein rakhna)

Is chapter ke hands-on project mein hum in tools ka intensive istemal karenge:

* **Amazon DynamoDB** (NoSQL Database)
* **Express Framework** (Node.js par bani hui web application framework)

---

### The Practical Hands-on Application Overview

Is chapter mein aap EC2 instances (jo ke by default fault tolerant nahi hotay) par mabni aik **100% Fault-Tolerant Web Application** design karna seekhein ge.

#### Application Ka Maqsad:

1. User aik image upload karega.
2. System us image par **Sepia Filter** (purana/vintage photo effect) apply karega.
3. User process hui image ko download kar sakega.

#### System Ka Design Step-by-Step:

1. **Workload Distribution:** Task ko aik single machine par chalane ke bajaye hum multiple EC2 instances par baant denge jo alag alag Data Centers (**Availability Zones**) mein chal rahe honge.
2. **Resilience in Code:** Code ko aisa banayein ge ke agar koi task fail ho toh wo crash na ho balkey retries aur idempotent mechanisms ke zariye recover ho.
3. **Complete Fault-Tolerant Infrastructure:** Hum in components ko aapas mein jodein ge:
* **SQS (Queue):** Upload hone walay tasks ko safely manage karne ke liye.
* **ALB (Application Load Balancer):** Traffic ko healthy EC2 instances par bhejne ke liye.
* **Auto Scaling Groups:** Kharab hone walay EC2 instances ko automatically khatam karke naye instances khade karne ke liye.
* **DynamoDB:** Data ko highly available aur fault-tolerant tareeqay se store karne ke liye.

---