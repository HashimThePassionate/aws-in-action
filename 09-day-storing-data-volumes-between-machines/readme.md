# Sharing data volumes between machines: EFS

## This chapter covers

Is chapter mein hum detail ke sath yeh baatein sikhenge:

* **High Availability wala Network Filesystem Banana:** Ek aisa storage banana jo kabhi down na ho aur multiple data centers mein replicate ho.
* **Network Filesystem ko Multiple EC2 Instances par Mount Karna:** Ek hi storage disk ko ek waqt mein bohot saare alag alag EC2 servers se jodna.
* **EC2 Instances ke Darmiyan Files Share Karna:** Alag alag servers ke darmiyan data ko real-time mein share karna.
* **Network Filesystem ki Performance Ko Improve (Tweak) Karna:** EFS ki speed aur response time ko optimize karna.
* **Network Bottlenecks ko Monitor Karna:** Yeh check karna ke kahin network speed ya bandwidth slow toh nahi ho rahi.
* **Shared Filesystem ka Backup Lena:** Apne poore shared storage ka secure backup maintain karna.

---

### Network Filesystem (EFS) Kya Hai Aur Yeh Kyun Zaroori Hai?

Aap farz karein ke aap ke paas ek purana application (legacy application) hai jo apni files ko kisi local drive (filesystem) par save karta hai.

* **Amazon S3 Kyun Nahi?** Hum ne Chapter 7 mein Object Storage (S3) parha tha. Lekin purane apps S3 ko direct samajh nahi paate. S3 use karne ke liye app ke poore code ko badalna parta hai, jo mehenga aur mushkil kaam hai.
* **EBS / Instance Store Kyun Nahi?** Chapter 8 mein hum ne Block Storage (EBS aur Instance Store) parha tha. EBS ka masla yeh hai ke woh sirf **ek waqt mein kisi ek EC2 server** ke sath hi lag sakta hai. Aur EBS sirf ek single data center (Availability Zone) mein rehta hai, jis wajeh se AWS iski uptime guarantee sirf **99.9%** deta hai.

Agar aap ko ek aisa storage chahiye jo **ek sath multiple EC2 servers** read/write kar sakein aur jo kabhi down bhi na ho, toh **Amazon Elastic File System (EFS)** sab se behtareen solution hai!

EFS **NFSv4.1** (Network File System version 4.1) protocol par kaam karta hai. Yeh aap ke data ko khud ba khud ek se ziada data centers (Availability Zones) mein copy (replicate) kar deta hai, jis wajeh se AWS iski uptime availability **99.99%** promise karta hai.

> **EFS WORKS ONLY WITH LINUX:** EFS filhal Windows EC2 instances ke sath kaam nahi karta. Agar aap Windows servers chalate hain, toh AWS aap ko EFS ke badle **Amazon FSx for Windows File Server** deta hai.

---

### EBS vs Instance Store vs EFS Figure 9.1 Breakdown)

Writer ne (Figure 9.1) mein teenon major storage types ka mawazna (comparison) kiya hai. Aayein isay ek clear architectural diagram se samajhte hain:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>


**Image (`Figure 9.1`) ka Mukammal Breakdown:**

1. **EBS Volume (Virtual Disk):** Diagram mein dekhein, Subnet 1 ka EBS Volume sirf Subnet 1 ke EC2 Instance 1 se jurha hai. Yeh boundary se bahar nahi ja sakta aur na hi kisi doosre server se attach ho sakta hai.
2. **Instance Store (Local Disk):** Diagram mein dekhein, Instance Store EC2 Instance 2 ke sath chipka hua hai kyunki yeh us physical host machine ke andar (hypervisor layer par) laga hota hai. Yeh temporary hai.
3. **EFS Filesystem (Shared Folder):** Diagram ke centre mein dekhein! EFS Filesystem AZ A ke Subnet 1 aur AZ B ke Subnet 2 dono ke saare EC2 instances se **ek hi waqt mein Network (NFSv4.1)** ke zariye jurha hua hai. Agar AZ A poora ka poora tabah bhi ho jaye, tab bhi AZ B ke servers ke paas EFS ka data chal raha hoga!

---

## All examples are covered by the Free Tier

* Is chapter ki tamam practical examples **AWS Free Tier** ke andar aati hain.
* Aap ko koi extra paise nahi dene parenge jab tak aap hidayat par amal karein aur in resources ko kuch dino se ziada na chalne dein.
* Chapter ke aakhir mein saare banaye gaye resources ko delete (clean up) karne ki mukammal guide di jayegi.

---

### EFS Ke Do Main Components Figure 9.2 Breakdown)

EFS system do bunyadi hisson se mil kar banta hai:

1. **Filesystem:** Yeh asal storage resource hai jo AWS Region ke andar aap ke data ko save karta hai. Lekin aap isay direct access nahi kar sakte.
2. **Mount Target:** Yeh Subnet ke andar bana hua ek Network Endpoint (IP Address) hota hai. EC2 instance is Mount Target par NFSv4.1 protocol ke zariye connect hota hai aur data parhta/likhta hai.

**Image Figure 9.2 ka Structural Breakdown:**

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

**Image breakdown:**

* Har Subnet ke andar ek **EFS Mount Target** lagaya gaya hai.
* Subnet 1 ke dono EC2 servers apne hi subnet ke Mount Target se baat karte hain.
* Subnet 2 ke dono EC2 servers apne subnet ke Mount Target se baat karte hain.
* Dono Mount Targets peeche se main **EFS Filesystem** ke sath jure hue hain, jisse charon EC2 servers ko same data nazar aata hai.

---

### Real-World Example: Shared `/home` Directories in Linux

Linux ek multiuser operating system hai, jahan alag alag users apna apna kaam kar sakte hain. Linux mein har user ka apna makhsoos folder hota hai jo `/home/$username` par pada hota hai.

Terminal par jab hum home directories ko dekhne ki command chalate hain:

```bash
$ ls -d -1 /home/* # Absolute paths ke sath tamam home directories ko list karta hai
drwx------ 2 andreas     andreas    4096 Jul 24 06:25 /home/andreas
drwx------ 3 michael     michael    4096 Jul 24 06:38 /home/michael
```

**Command aur Output ki Deep Detail:**

* `ls`: Files aur folders list karne ki command.
* `-d`: Sirf directories (folders) dikhao, unke andar ka saman nahi.
* `-1` (number 1): Har entry ko ek alag line par dikhao.
* `/home/*`: `/home` folder ke andar jitne bhi folders hain sab ka absolute path dikhao.
* `drwx------`:
* `d` = Directory (folder) hai.
* `rwx` = Owner ko **Read, Write, Execute** ki poori permissions hain.
* `------` = Baki kisi user ya group ko koi access nahi hai.


* `2` aur `3`: Hard links ki taadad (folders ki internal counting).
* `andreas andreas`: Folder ka Owner User `andreas` hai aur Owner Group bhi `andreas` hai.
* `4096`: Folder metadata ka size (4 KB).
* `Jul 24 06:25`: Folder banne ya last modify hone ki tareekh aur waqt.
* `/home/andreas`: Andreas user ka private ghar (home folder). Isay sirf Andreas hi khol sakta hai.
* `/home/michael`: Michael user ka private ghar (home folder). Isay sirf Michael hi khol sakta hai.

#### Real-World Masla (Problem)

Agar aap ke paas 5 alag alag EC2 servers chal rahe hain, toh har server par user ka home folder alag alag hoga. Agar Michael `EC2-Server-1` par login karke koi file save karega, aur agli dafa woh `EC2-Server-2` par login hoga, toh usay apni file **nahi milegi** kyunki woh doosra server hai!

#### EFS ka Hal (Solution)

Hum ek **EFS Filesystem** banayenge aur usay tamaam EC2 servers ke `/home` folder par mount kar denge!

Is se yeh hoga ke saare users ke home folders EFS par chale jayenge. Michael jis bhi EC2 server par login karega, usay apna wahi saara data, files, aur environment hamesha ready milega, bilkul waise hi jaise woh apne zati ghar mein ho!

---

## Creating a filesystem

Filesystem woh asal jagah (resource) hoti hai jahan aap ki saari files, directories (folders), aur symbolic links mehfooz hote hain.

EFS ki sab se khoobsurat baat yeh hai ke yeh **Amazon S3 ki tarah khud ba khud barhta (grow karta) hai**. Aap ko pehle se yeh guess nahi karna parta ke "mujhe 100 GB chahiye ya 500 GB". Aap jitna data dalte jayenge, storage space khud ba khud utni hi barhti jayegi.

Yeh filesystem ek makhsoos **AWS Region** (maslan `us-east-1`) mein banta hai, aur AWS background mein aap ke data ko automatically ek se ziada data centers (Availability Zones) mein copy (replicate) karta rehta hai taake data hamesha safe rahe.

---

### Using CloudFormation to describe a filesystem

Hum EFS filesystem ko manually AWS Console par ja kar clicking ke bajaye **CloudFormation** ke zariye (Infrastructure as Code) configure karenge.

Niche **Listing 9.1** ka CloudFormation code aur uski **har ek line ki deep explanation** di gayi hai:

```yaml
Resources: # Ye stack ke resources aur unki properties ko specify karta hai
  [...]
FileSystem:
  Type: 'AWS::EFS::FileSystem'
  Properties:
    Encrypted: true # Hum ye recommend karte hain ke by default encryption at rest enable rakhein
    ThroughputMode: bursting # Default throughput mode ko bursting kaha jata hai. Hum I/O intensive workloads ke liye throughput mode par aagay mazeed baat karein ge
    PerformanceMode: generalPurpose # Default performance mode general purpose hai. Hum performance mode par aagay mazeed baat karein ge
    FileSystemPolicy: # Data in transit ko encrypt karna security ki best practice hai. Filesystem policy ye yakeeni banati hai ke tamam access secure transport istemal karein
      Version: '2012-10-17'
      Statement:
        - Effect: 'Deny'
          Action: '*'
          Principal:
            AWS: '*'
          Condition:
            Bool:
              'aws:SecureTransport': 'false'

```

#### Code Ki Line-by-Line Breakdown

* **`Resources:`** CloudFormation template ka main section jahan hum batate hain ke konsi AWS services banani hain.
* **`FileSystem:`** Yeh hamare EFS resource ka logical naam (nickname) hai.
* **`Type: 'AWS::EFS::FileSystem'`**: Yeh line AWS ko batati hai ke hum ek **Amazon EFS Filesystem** banana chahte hain.
* **`Properties:`** Yahan se EFS ki tamam settings aur configuration shuru hoti hai.
* **`Encrypted: true`**: **Encryption at Rest** ko enable karta hai. Iska matlab hai ke jab aap ka data disk par pada hoga, woh AWS KMS (Key Management Service) ke zariye encrypted hoga. Agar koi physical drive chori bhi kar le, toh data nahi parh sakta.
* **`ThroughputMode: bursting`**: Throughput ka matlab hai data ke aane jaane ki speed. Default mode `bursting` hota hai, jisme aap ke filesystem ka size jitna bada hoga, uski speed utni hi ziada hogi. *(Note: Chote data size ke waqt yeh extra burst credits use karke tez speed deta hai).*
* **`PerformanceMode: generalPurpose`**: Performance mode do qisam ke hote hain. `generalPurpose` default hai jo web servers, content management systems, aur home directories ke liye best hai kyunki is mein latency (delay) bohot kam milti hai.
* **`FileSystemPolicy:`** Yeh EFS ki apni resource-based security policy hai jo access control karti hai.
* **`Version: '2012-10-17'`**: IAM policy language ka standard version.
* **`Statement:`** Security rules ki list.
* **`Effect: 'Deny'`**: Is rule ka maqsad access ko **rokna (block karna)** hai.
* **`Action: '*'`**: Tamam operations par (parhna, likhna, delete karna, wagera).
* **`Principal: AWS: '*'`**: Har user, server, ya request par yeh rule apply hoga.
* **`Condition:`** Condition tabhi sach hogi jab...
* **`Bool: 'aws:SecureTransport': 'false'`**: ...connection unencrypted ho (yani TLS/SSL encryption ke bina plain network par data bheja ja raha ho).
* **Is Policy Ka Aasan Matlab:** "Agar koi bhi user ya EC2 server EFS ke sath bina encryption (Encryption in Transit) ke connect hone ki koshish karega, toh EFS usay **Deny (block)** kar dega!" Yeh security ki sab se behtareen practice hai.

---

## Pricing

EFS ki pricing ko samajhna bohot aasan hai. Iska bill mukhya roop se **3 factors** par depend karta hai:

1. **Stored Data ki Taadad (GB per Month):** Aap ne kitne Gigabytes data store kiya hua hai.
2. **Access Frequency (Kitni Baar Data Parha/Likha Gaya):** Data rozaana access hota hai ya mahine mein ek do dafa.
3. **Availability vs Cost (Multi-AZ vs Single Zone):** Kya aap ko 99.99% High Availability chahiye ya aap saste bill ke liye ek single data center (99.9%) par raazi hain.

---

### Storage Classes Aur Unka Maqsad

AWS EFS mein 4 main Storage Classes milti hain jinhein aap apni zaroorat ke hisab se chunte hain:

```
                          ┌──────────────────────────────────────────┐
                          │            EFS Storage Classes           │
                          └─────────────────────┬────────────────────┘
                                                │
             ┌──────────────────────────────────┴──────────────────────────────────┐
             ▼                                                                     ▼
┌───────────────────────────┐                                         ┌───────────────────────────┐
│     Multi-AZ (Standard)   │                                         │   Single-AZ (One Zone)    │
│    Availability: 99.99%   │                                         │    Availability: 99.9%    │
├───────────────────────────┤                                         ├───────────────────────────┤
│ • Standard Storage        │                                         │ • One Zone Storage        │
│   (Rozaana frequent access│                                         │   (Frequent access,       │
│    ke liye)               │                                         │    sasta option)          │
│                           │                                         │                           │
│ • Standard-IA Storage     │                                         │ • One Zone-IA Storage     │
│   (Kambhar access karne   │                                         │   (Kambhar access,        │
│    wale data ke liye)     │                                         │    sab se sasta option)   │
└───────────────────────────┘                                         └───────────────────────────┘

```

#### 1. Standard Storage (Frequent Access + Multi-AZ)

* **Kiske Liye Hai?** Aisa data jise rozaana ya bar bar access kiya jata hai.
* **Faida:** Sab se kam latency (fast response) aur high availability (**99.99%**), kyunki data multiple data centers mein replicate hota hai.

#### 2. Standard–Infrequent Access (IA) Storage

* **Kiske Liye Hai?** Aisa data jise mahine mein ek do baar hi dekha jata hai (kambhar access).
* **Faida:** Data storage ki keemat bohot kam ho jati hai ($0.025 per GB).
* **Trade-off:** Jab bhi aap data access (read/write) karenge, toh **Access Request Fee** alag se lagegi, aur pehli byte aane mein thora sa delay (latency) ho sakta hai.

#### 3. One Zone Storage

* **Kiske Liye Hai?** Aisa data jo frequent access hota hai, lekin agar thori dair ke liye unavailable bhi ho jaye toh company ko bara nuqsan na ho.
* **Trade-off:** Yeh data ko multiple data centers mein copy nahi karta, balke **ek hi data center** mein rakhta hai. Availability kam ho kar **99.9%** reh jati hai, lekin storage price lag bhag aadhi ($0.16 per GB) ho jati hai.

#### 4. One Zone–Infrequent Access (IA) Storage

* **Kiske Liye Hai?** Aisa data jo na toh ziada access hota hai aur na hi uske liye Multi-AZ ki zaroorat hai.
* **Faida:** Yeh EFS ka **sab se sasta** storage option hai ($0.0133 per GB).

> **Durability Note:** Sub Storage Classes ki **Durability 99.999999999% (11 nines)** hoti hai! Durability ka matlab hai data ke khud ba khud corrupt ya lose na hone ki guarantee. Lekin agar aap One Zone use kar rahe hain, toh us data center par koi qudrati aafat aane ki surat mein data bachane ke liye backup zaroor rakhein.

---

### EFS Pricing Table

Niche di gayi Table 9.1 US East (N. Virginia - `us-east-1`) region ke rates ko zahir karti hai:

**Table 9.1 EFS storage classes affect the monthly costs for storing data.**

| Storage class | Price per GB/month | Access requests per GB transferred |
| --- | --- | --- |
| **Standard Storage** | $0.30 | $0.00 |
| **Standard–Infrequent Access Storage** | $0.025 | $0.01 |
| **One Zone Storage** | $0.16 | $0.00 |
| **One Zone–Infrequent Access Storage** | $0.0133 | $0.01 |

#### Table ki Aasan Wazahat:

* **Standard Storage:** $0.30 per GB har mahine. Access par koi extra charge nahi ($0.00).
* **Standard-IA:** Storage aam Standard se 12 guna sasti ($0.025), lekin har 1 GB data access karne par $0.01 ka extra request fee lagega.
* **One Zone Storage:** Storage rate $0.16 per GB. Access fee $0.00.
* **One Zone-IA:** Storage rate $0.0133 per GB. Access fee $0.01.

---

### Cost Estimation Example (5 GB Data Ka Hisab)

Aayein 5 GB data ko EFS par rakhne ka mahana kharcha (cost) calculate karte hain:

* **Scenario:** Data rozaana din mein kai baar access hota hai aur high availability (Multi-AZ) laazmi chahiye.
* **Storage Class Selection:** Hum **Standard Storage** class chunenge.
* **Calculation:**

$$\text{Total Cost} = 5\text{ GB} \times \$0.30/\text{GB} = \$1.50\text{ per month}$$



Poore ek mahine ke liye 5 GB data EFS par rakhne ka kul kharcha sirf **$1.50 (USD)** banta hai.

> **AWS Free Tier Benefit:** Agar aap ka AWS account naya hai (pehlay 12 mahine mein hai), toh AWS Free Tier ke tehat har mahine **5 GB (Standard Storage)** bilkul **MUFT (Free)** milti hai!

---


## Creating a mount target

Mount target ek aisa zariya (Network Endpoint / IP address) hai jo kisi makhsoos Subnet ke andar banta hai aur aap ke EC2 instances ko **NFSv4.1 protocol** ke zariye EFS filesystem se connect hone deta hai. EC2 instance aur mount target ke darmiyan saari baat-cheet standard TCP/IP network connection ke zariye hoti hai.

Security Groups ka istemal kar ke hum yeh control karte hain ke mount target ke andar konsa network traffic aa sakta hai. **NFS protocol** hamesha **Port 2049** ka istemal karta hai, is liye humein security group mein port 2049 ko allow karna parta hai.

---

### Dynamic Security Group Pattern (Security Groups Ki Chaining)

Kisi specific IP address ko allow karne ke bajaye, hum do alag alag security groups banate hain. Yeh AWS ka ek bohot hi taqatwar aur safe design pattern hai:

1. **EFS Client Security Group:** Yeh security group un tamam EC2 instances par lagaya jata hai jinhein EFS filesystem se connect hona hota hai. Is security group mein koi khas Inbound Rule (ijazat) nahi hoti, yeh sirf EC2 instances par ek **"Identity Card"** ya **"Badge"** ka kaam karta hai.
2. **Mount Target Security Group:** Yeh security group EFS Mount Target par lagaya jata hai. Is mein hum rule banate hain ke: *"Port 2049 par sirf wahi traffic andar aa sakta hai jiske paas 'EFS Client Security Group' ka badge ho!"*

#### Iska Bara Faida Kya Hai?

Aap ko kabhi bhi IP addresses manually add ya remove nahi karne parenge. Agar aap Auto Scaling ke zariye 10 naye EC2 servers bhi launch kar dete hain, toh jab tak un par Client Security Group laga hoga, woh automatically EFS se connect ho sakenge.

---

> ### 💡 EFS IS NOT ONLY ACCESSIBLE FROM EC2 INSTANCES
> 
> 
> Is chapter mein hum EFS ko EC2 instances par mount kar rahe hain kyunki yeh sab se aam use-case hai. Lekin EFS ko in jagahon par bhi istemal kiya ja sakta hai:
> * **Containers:** Amazon ECS (Elastic Container Service) aur Amazon EKS (Kubernetes)
> * **Serverless Functions:** AWS Lambda
> * **On-Premises Servers:** Aap ke apne office ke physical servers (AWS Direct Connect ya VPN ke zariye)
> 
> 

---

### Figure 9.3 Ka Visual Breakdown

Book mein di gayi image (Figure 9.3) is poore security concept ko wazeh karti hai. Aayein isay ek structural diagram se samajhte hain:

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

**Diagram ki Wazahat:**

* **Client Security Group:** Dono Availability Zones (AZ A aur AZ B) ke EC2 instances ke gird ek boundary (badge) bana hua hai.
* **Mount Target Security Group:** Yeh protection layer Mount Target ke upar lagi hui hai jo keh rahi hai: *"Main sirf Client Security Group wale servers se aane wali Port 2049 requests ko hi EFS tak jaane dunga."*

---

### Listing 9.2 CloudFormation snippet of an EFS mount target and security groups

Is snippet mein hum CloudFormation ke zariye do security groups aur **Subnet A** ke liye EFS mount target define kar rahe hain:

```yaml
Resources:
  [...]
EFSClientSecurityGroup: # Is security group ko kisi rule ki zaroorat nahi hoti. Ye sirf EC2 instances se bahar jane wale traffic ko mark karne ke liye istemal hoti hai
  Type: 'AWS::EC2::SecurityGroup'
  Properties:
    GroupDescription: 'EFS Mount target client'
    VpcId: !Ref VPC

MountTargetSecurityGroup: # Ye security group mount target ke sath linked hoti hai
  Type: 'AWS::EC2::SecurityGroup'
  Properties:
    GroupDescription: 'EFS Mount target'
    SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 2049 # Port 2049 par traffic ki ijazat deta hai
        ToPort: 2049
        SourceSecurityGroupId: !Ref EFSClientSecurityGroup # Ye sirf us security group se traffic ki ijazat deta hai jo EC2 instances ke sath linked ho
    VpcId: !Ref VPC

MountTargetA:
  Type: 'AWS::EFS::MountTarget'
  Properties:
    FileSystemId: !Ref FileSystem # Mount target ko filesystem ke sath attach karta hai
    SecurityGroups:
      - !Ref MountTargetSecurityGroup # Security group assign karta hai
      - SubnetId: !Ref SubnetA # Mount target ko subnet A ke sath link karta hai

```

#### Code Ki Har Detail (Deep Breakdown)

* **`EFSClientSecurityGroup:`** Is resource ka naam client security group rakha gaya hai.
* **`Type: 'AWS::EC2::SecurityGroup'`**: AWS ko batata hai ke hum ek Security Group bana rahe hain.
* **`GroupDescription: 'EFS Mount target client'`**: Is security group ki choti si wazahat.
* **`VpcId: !Ref VPC`**: Batata hai ke yeh security group kis Virtual Private Cloud (VPC) ke andar banega. *(Is Security Group mein koi `SecurityGroupIngress` rules nahi hain kyunki iska kaam sirf EC2 servers ko mark karna hai).*
* **`MountTargetSecurityGroup:`** Yeh mount target ko protect karne wala doosra security group hai.
* **`SecurityGroupIngress:`** Inbound rules ki list (yani kaun andar aa sakta hai).
* **`IpProtocol: tcp`**: Traffic TCP protocol istemal karega.
* **`FromPort: 2049` & `ToPort: 2049**`: Port number 2049 ko allow kar raha hai (jo NFS protocol ki default port hai).
* **`SourceSecurityGroupId: !Ref EFSClientSecurityGroup`**: **Sab se eham line!** Yeh IP address ke bajaye keh raha hai ke *"Sirf wahi servers allow hain jin par `EFSClientSecurityGroup` laga ho."*
* **`MountTargetA:`** Subnet A ke andar EFS Mount Target banane ka main block.
* **`Type: 'AWS::EFS::MountTarget'`**: Batata hai ke hum EFS Mount Target bana rahe hain.
* **`FileSystemId: !Ref FileSystem`**: Is mount target ko pehle se banaye gaye EFS Filesystem ke sath jod deta hai.
* **`SecurityGroups:`**: Is mount target par `MountTargetSecurityGroup` wali security firewall apply kar deta hai.
* **`SubnetId: !Ref SubnetA`**: Is mount target ko **Subnet A** (Availability Zone A) mein rakhta hai taake us subnet ke EC2 instances is se connect ho sakein.

---

### Listing 9.3 CloudFormation snippet of an EFS mount target and security groups

High Availability (99.99% uptime) haasil karne ke liye zaroori hai ke hum ek mount target **Subnet B** (doosre data center/AZ) mein bhi banayen. Code ka snippet yeh raha:

```yaml
Resources:
  [...]
MountTargetB:
  Type: 'AWS::EFS::MountTarget'
  Properties:
    FileSystemId: !Ref FileSystem
    SecurityGroups:
      - !Ref MountTargetSecurityGroup
    SubnetId: !Ref SubnetB # Ye mount target ko subnet B ke sath attach karta hai

```

#### Code Ki Har Detail (Deep Breakdown)

* **`MountTargetB:`** Resource ka naam `MountTargetB` rakha gaya hai taake yeh `MountTargetA` se alag pehchana ja sake.
* **`Type: 'AWS::EFS::MountTarget'`**: AWS resource type define kar raha hai.
* **`FileSystemId: !Ref FileSystem`**: Yeh bhi wahi same main EFS Filesystem istemal karega jo `MountTargetA` kar raha tha.
* **`SecurityGroups: - !Ref MountTargetSecurityGroup`**: Wahi same security firewall istemal ho gi jo Port 2049 par client traffic ko check karti hai.
* **`SubnetId: !Ref SubnetB`**: **Main Faraq!** Yeh mount target **Subnet B** (Availability Zone B) ke andar banega.

Is tarah hamara EFS filesystem ab dono Subnets (A aur B) ke EC2 instances ke liye fully accessible aur Highly Available ho chuka hai!

---