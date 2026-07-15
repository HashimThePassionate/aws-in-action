# Using virtual machines: EC2

Aap apne haath mein pakde hue smartphone ya apne bag mein rakhe laptop ki taqat ko dekh kar heraan hote honge ke yeh kitne saare kaam asani se kar lete hain. Lekin, agar aap ka task aisa hai jise bohot zyada **massive computing power** chahiye, ya bohot zyada **network traffic** handle karni hai, ya phir use bina ruke **24/7** chalte rehna hai, to aap ke laptop ya mobile ki bas ho jayegi! Aise kamo ke liye ek **Virtual Machine (VM)** sab se behtareen solution hai.

### Virtual Machine Kya Hoti Hai? (Bacho Wali Example)

Iski misal aise samajhein ke ek bohot bada, aalishan mall (Physical Server) hai. Is mall ke andar choti-choti partitions kar ke alag-alag shops (Virtual Machines) bana di gayi hain.

* Har shop ka apna shutter, apna counter, aur apna staff hota hai.
* Ek shop mein kya bik raha hai, us se barabar wali shop ko koi lena dena nahi hota.
* Virtual machine ke case mein bhi bilkul aisa hi hota hai. Aap ko ek bohot bade physical computer (jo AWS ke data center mein pada hai) ka ek chota sa hissa ya slice de diya jata hai. Is slice ko aap bilkul apne zati computer ki tarah chalate hain.

AWS par in virtual machines ko jis service ke zariye chalaya jata hai, use hum **Elastic Compute Cloud (EC2)** kehte hain.

Is chapter mein aap seekhenge ke AWS par virtual machine ko kaise launch (start) aur manage kiya jata hai. Is ke sath hi hum is VM se connect karna, applications configure karna, aur is system ki performance ko monitor karna bhi seekhenge. Sab se ahem baat, hum EC2 ke alag-alag **pricing options** ko bhi explore karenge taake aap ke paise zaya na hon aur kam budget mein behtareen computing power mil sake.

---

## Not all examples are covered by Free Tier

Yahan writer humein pehle hi ek warning de raha hai jo ke bohot zaroori hai. Is chapter mein di gayi tamam examples **AWS Free Tier** (muft sahulat) ke daire mein nahi aatein.

### Nuqsan Se Bachne Ka Tarika:

* Jab bhi koi aisi example aayegi jis par paise lagte hain, to wahan ek **special warning message** diya jayega.
* Baqi aam examples ke liye rule yeh hai ke jab tak aap unhein **kuch dino se zyada** on nahi chorenge, aap ko kuch pay nahi karna padega.
* Lekin yaad rakhein, yeh tabhi sach hai agar aap ne is book ke liye ek bilkul **fresh (naya) AWS account** banaya hai aur us par pehle se koi aur cheez nahi chal rahi.
* Behtareen strategy yeh hai ke aap is chapter ke practicals ko **kuch hi dino ke andar mukammal karein** aur aakhir mein apne account ko clean-up (tamam chalne wali cheezon ko delete) kar dein.

---

## Exploring a virtual machine

Virtual machine (VM) hamesha ek physical computer (jise hum **Host Machine** kehte hain) par chalti hai. Is virtual machine ko doosri virtual machines se mukammal taur par alag aur safe rakhne ka kaam **Hypervisor** karta hai.

### Virtualization Ki Layers (Figure 3.1 Breakdown)

Agar hum **Figure 3.1** ko dekhein, to virtualization ka poora dacha (architecture) teen ahem layers par khara hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

1. **The Host Machine (Sab se niche wali layer):** Yeh asli, physical hardware server hai jo AWS ke data center mein laga hota hai. Is ke paas CPU, RAM, Storage (hard disks), aur Network cables hoti hain.
2. **The Hypervisor (Darmeyan wali layer):** Yeh ek behtareen software ya hardware manager hai. Iska kaam physical machine ke hardware ko chote chote hisson mein baant kar upar wali virtual machines ko dena hai. Yeh bilkul ek traffic cop ki tarah kaam karta hai jo har Guest VM ko security aur boundary deta hai taake Guest 1 kabhi bhi Guest 2 ke data mein taank-jhaank na kar sake.
3. **The Guest Machines / Guest OS (Sab se upar wali layer):** Yeh wo virtual machines hain jo hum AWS par banate hain. Har Guest VM ke paas apna isolated (alag) Operating System hota hai (jaise Linux ya Windows).

### Virtual Machine Kahan Kaam Aati Hai? (Real-world Use Cases)

Writer ne teen bohot ahem real-world use cases bataye hain:

* **Hosting a web application (WordPress):** Agar aap ne koi blog ya website chalani hai.
* **Operating an enterprise application (ERP):** Company ke hisab-kitab aur management ke bade softwares chalane ke liye.
* **Transforming or analyzing data:** Jaise badi video files ko convert (encode) karna ya bade data sets ko process karna.

---

## Launching a virtual machine

Ab hum ek asli practical kaam karne ja rahe hain! Hum ek virtual machine launch karenge taake hum **LinkChecker** naam ka ek tool chala sakein.

### LinkChecker Kya Hai Aur Kyun Chahiye? (Real-World Example)

Aap ki website par agar koi link toota hua hai (yani jab user click kare to "404 Not Found" error aaye), to website ka user experience kharab hota hai aur Google search rankings (SEO score) bhi gir jati hai. **LinkChecker** poori website ko scan kar ke in tootay hue links ko dhoond nikalta hai.
Aap ise apne laptop par bhi chala sakte hain, lekin AWS ke EC2 instance par chalane ka faida yeh hai ke wahan **internet speed** aur **computing power** bohot zyada milti hai, jis se kaam seconds mein ho jata hai.

AWS par virtual machine ko launch karna bohot asaan hai (AWS virtual machine ko **EC2 Instance** kehta hai). **Figure 3.2** ke mutabaq aap ko yeh steps follow karne hain:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

1. Apne browser mein AWS Management Console kholin: `[https://console.aws.amazon.com](https://console.aws.amazon.com)`
2. **Region Selection (Figure 3.2):** Sab se pehle top right corner par ja kar region select karein. Hum is book ke liye **N. Virginia (US East-1)** region select karenge kyunke saari examples isi ke liye design ki gayi hain.
3. **Search for EC2:** Services menu par click karein aur search bar mein "EC2" likh kar enter karein.
4. **Click Launch Instance:** EC2 dashboard par aate hi aap ko ek bada orange button **"Launch instance"** nazar aayega (jaise **Figure 3.2** mein dikhaya gaya hai). Is par click karte hi ek wizard (form) khul jayega jo virtual machine banane mein aap ki rehnumai karega.

Yeh wizard aap se niche batayi gayi 7 details poochega:

1. Virtual machine ka naam rakhna
2. Operating System (OS) choose karna
3. Machine ka size (RAM/CPU) select karna
4. Connection details configure karna
5. Storage (Hard drive) add karna
6. Firewall (Security Group) set karna
7. Permissions set karna (taake VM doosri AWS services se baat kar sake)

---

### NAMING THE VIRTUAL MACHINE

Apni virtual machine ko pehchanna bohot zaroori hai, khaas tor par jab ek hi AWS account ko zyada log istemal kar rahe hon.

**Figure 3.3** ke mutabaq:

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

* **Name and tags** wale section mein ja kar **Name** field ke andar apni machine ka koi munasib naam likhein.
* Book ke mutabaq hum iska naam **"mymachine"** rakhenge. Yeh naam AWS ke andar is instance par ek `Name` tag laga deta hai, jis se ise dhoondna asaan ho jata hai.

---

### SELECTING THE OPERATING SYSTEM

Ab humein apni machine ke liye Operating System chunna hai. AWS mein operating system akela nahi aata, balkey us ke sath kuch zaroori tools aur software pehle se pack (bundle) hote hain. Is poore package ko hum **Amazon Machine Image (AMI)** kehte hain.

### AMI Kya Hai? (Virtual Appliances ki ELI5 Explanation)

Agar hum **Virtual Appliance** wale diagram ko dekhein, to yeh ek **tayyar-shuda template** ki tarah hai.

```
+--------------------------+                 +--------------------------+
|    Virtual Appliance     |                 |     Virtual Machine      |
|  (Template / Blueprint)  |                 |     (Live & Running)     |
|                          |                 |                          |
|  +--------------------+  |                 |  +--------------------+  |
|  |    Application     |  |                 |  |    Application     |  |
|  +--------------------+  |  Launch Machine |  +--------------------+  |
|  |     Libraries      |  | --------------> |  |     Libraries      |  |
|  +--------------------+  |                 |  +--------------------+  |
|  |  Operating System  |  |                 |  |  Operating System  |  |
|  +--------------------+  |                 |  +--------------------+  |
+--------------------------+                 +--------------------------+

```

Iski asaan misal **frozen pizza** jaisi hai. Aap frozen pizza late hain, use oven mein rakhte hain, aur pizza tayyar! Aap ko dough gundhne ya sauce banane ki mehnat nahi karni padti.

* **Virtual Appliance (AMI):** Wo frozen pizza (template) hai jis mein OS, libraries, aur tools pehle se dale hue hain.
* **Virtual Machine:** Wo paka hua pizza hai jo launch hone ke baad chal raha hai. Jab bhi aap kisi AMI se VM banayenge, har dafa bilkul **exact same result** milega. Is se softwares ko baar baar install aur configure karne ka kharcha aur waqt bach jata hai.

#### AMI ke Technical Details aur 2026 ke Modern Updates:

* **AMI Components:** AMI ke andar ek read-only filesystem (OS, files, config) hoti hai. Lekin purane zamane mein operating system ka main engine (Kernel) alag se **Amazon Kernel Image (AKI)** ke zariye load hota tha.
* **Modern Nitro System (2026 Update):** Aaj ke dor (2026) mein AWS mukammal taur par apne **Nitro System** par shift ho chuka hai. Nitro ek nihayat hi tez, hardware-based virtualization system hai jo **KVM (Kernel-based Virtual Machine)** hypervisor ko custom-made ASICs (chips) ke sath jodta hai. Iska faida yeh hai ke aap ki virtual machine ko bilkul aisi direct speed milti hai jaise wo bina kisi hypervisor ke direct physical hardware (bare metal) par chal rahi ho!

<div align="center">
  <img src="./images/04.png" width="600"/>
</div>

* **Operating System Choice (Figure 3.4):** **Figure 3.4** ke mutabaq humein **Quick Start** mein ja kar **Amazon Linux** select karna hai.
* *Purana rasta:* Book mein Amazon Linux 2 select karne ka kaha gaya hai.
* *2026 Modern Standard:* Aaj kal **Amazon Linux 2023 (AL2023)** standard hai. Yeh Red Hat aur Fedora par mabni hai, bohot secure hai, aur AWS isay khud maintain aur optimize karta hai taake EC2 par maximum performance mil sake. Architecture hamesha **64-bit (x86)** select karein.



---

### CHOOSING THE SIZE OF YOUR VIRTUAL MACHINE

Ab baari aati hai taqat choose karne ki! AWS computing power ko **Instance Types** mein divide karta hai. Har instance type aap ko batata hai ke aap ke paas kitne **virtual CPUs (vCPUs)** aur kitni **Memory (RAM)** hogi.

#### Table 3.1: Examples of instance families and types (Updated for 2026)

Writer ne April 2022 ke rates bataye the, lekin neeche hum ne **2026 ke modern standard** aur rates ke mutabaq is table ko update kiya hai taake aap sahi faisla kar sakein:

| Instance Type | Virtual CPUs (vCPUs) | Memory (RAM) | Description / Core Behavior | Typical Use Case | 2026 Est. Monthly Cost (On-Demand Linux) |
| --- | --- | --- | --- | --- | --- |
| **t3.nano** (Replacing t2.nano) | 2 vCPUs | 0.5 GiB | Cheap aur burstable performance. 2 vCPUs ke sath aati hai jo t2 se behtar performance deti hai. | Chote testing environments, low-traffic web apps. | ~$3.80 |
| **m6i.large** / **m7i.large** | 2 vCPUs | 8 GiB | Balanced ratio. CPU aur RAM ka behtareen tawazun. (m7i features modern Intel Xeon processors). | Medium databases, backend APIs, enterprise apps. | ~$65 - $70 |
| **r6i.large** / **r7i.large** | 2 vCPUs | 16 GiB | Memory-optimized. CPU ke muqable RAM bohot zyada hoti hai. | In-memory caches (Redis/Memcached), heavy database servers. | ~$85 - $90 |

---

### Instance Families (Bacho ki tarah asaan classification)

AWS ne har workload ke liye alag alag "gariyan" banayi hui hain. In gariyon ko hum families kehte hain:

* **T Family (The Budget Hybrid Car):** Yeh sasti hoti hain. Inki baseline performance normal hoti hai, lekin agar achanak load barh jaye, to yeh **burst** kar ke apni speed bohot tez kar sakti hain (short periods ke liye).
* **M Family (The Everyday Sedan):** General purpose. Jitna CPU, utni RAM. Har aam kaam ke liye fit.
* **C Family (The Sports Car):** Compute-optimized. Iska engine (CPU) bohot heavy hota hai. Heavy calculations aur video rendering ke liye bani hai.
* **R Family (The Delivery Truck):** Memory-optimized. Is ke paas bohot badi space (RAM) hoti hai taake bade databases ko dimaag mein fit rakh sake.
* **X Family (The Cargo Train):** Intehai zyada RAM wali machines (up to several Terabytes) jo in-memory databases ke liye use hoti hain.
* **D & I Families (The Warehouse Servers):** Storage optimized. Huge hard drives (HDDs/SSDs) fast data reading ke liye.
* **P, G, CG Families (The Supercars with Nitro):** In mein **GPUs** (Graphics cards) lage hote hain jo Machine Learning, AI, aur high-end graphics processing ke liye hote hain.
* **F Family (The Shapeshifter):** FPGAs based, jin ke hardware chips ko custom tasks ke liye reprogram kiya ja sakta hai.

> **Rule of Thumb:** Shuru mein hum aksar zaroorat se zyada badi machine select kar lete hain. DevOps ka sunehra asool yeh hai ke **hamesha sab se choti machine (jaise t2.micro ya t3.micro) se shuru karein**. Agar zaroorat paray, to hum baad mein asani se size barha sakte hain.

#### Disassembling `t2.micro` (Figure 3.5 & Diagram Breakdown)

```
      t       2    .   micro
     ---     ---      -------
      |       |          |
    Family Generation   Size

```

Agar hum **Figure 3.5** aur is breakdown diagram ko dekhein:

<div align="center">
  <img src="./images/08.png" width="600"/>
</div>

* **Family (`t`):** Batata hai ke yeh burstable family hai.
* **Generation (`2`):** Batata hai ke yeh is family ki doosri nasal (generation) hai. (2026 mein hamare paas `t3` aur `t4g` generations standard hain).
* **Size (`micro`):** Batata hai ke yeh is family ka bohot hi chota aur sasta variant hai.

Select **t2.micro** (ya agar aap ke region mein t3.micro available hai aur free tier mein hai to wo) kyunke yeh **Free Tier eligible** hai (is mein 1 vCPU aur 1 GiB RAM milti hai).

---

### Graviton Processors (The Future of AWS Chips)

Aap ne Apple ke M1/M2/M3 chips (ARM architecture) ke bare mein suna hoga jo laptop ko bina garam kiye behtareen speed aur lambi battery life dete hain. AWS ne bilkul isi tarah servers ke liye apna custom ARM chip banaya hai jise **Graviton** kehte hain.

* **Faida:** Kam kharche mein Intel/AMD se behtar performance milti hai.
* **Shart:** Aap ka software **ARM64 architecture** ke liye compile hona chahiye (jo ke Linux par ab bohot hi asaan hai).
* **2026 Graviton Options:** 2026 mein **Graviton 4 (C8g/M8g/R8g)** tak ke processors aa chuke hain! Lekin seekhne ke liye aap niche diye gaye types ko explore kar sakte hain:
* **T4g:** Intehai sasti aur burstable machines.
* **M6g/M7g:** General-purpose.
* **C6g/C7g:** Compute-optimized.
* **R6g/R7g:** Memory-optimized.



---

### CONFIGURING THE KEY PAIR FOR LOGIN

Purane zamane mein jab bhi kisi Linux machine par login karna hota tha, to hum password ya phir ek **SSH Key Pair (Public/Private Key)** use karte the.

**Figure 3.6** ke mutabaq AWS wizard bhi aap se poochega ke aap kaunsa Key Pair use karna chahte hain.

<div align="center">
  <img src="./images/09.png" width="600"/>
</div>

### Hum Key Pair Kyun Use Nahi Kar Rahte? (The Trade-Off)

Writer yahan ek bohot hi kamal ka architectural design decision bata raha hai. Hum is wizard mein **Key Pair configure nahi karenge** aur **"Proceed without a key pair"** select karenge.

**Kyun?**

1. **Single User Limit:** SSH key aam tor par ek hi user ke liye banti hai. Agar poori DevOps team ne login karna ho, to ek hi key sab ko share karni paregi jo ke security ka bera-ghark kar deti hai.
2. **Hard to Change:** Agar VM launch ho jaye, to bahar se uski SSH key badalna intehai mushkil kaam hai. Agar aap se key kho gayi, to machine hamesha ke liye lock ho sakti hai.

**Behtar Rasta (2026 Standard):**
Hum aage chal kar AWS ka modern tareeqa seekhenge (jaise **AWS Systems Manager Session Manager** ya **EC2 Instance Connect**). Is tareeqe mein humein kisi key file (`.pem` ya `.ppk`) ki zaroorat nahi hoti, aur hum direct browser ke zariye secure tareeqe se login kar lete hain. Is liye abhi ke liye be-fakar ho kar dropdown se **"Proceed without a key pair (Not recommended)"** select kar lein.

## DEFINING NETWORK AND FIREWALL SETTINGS

Ab hum apni virtual machine ke liye rasta aur deewar (Network aur Firewall) tayar karne lage hain. AWS par network ko hum **VPC (Virtual Private Cloud)** kehte hain aur firewall ko **Security Group** kehte hain.

### Security Group aur Firewall Kya Hai? (Bacho Wali Example)

Iski misal aise samajhein ke aap ne ek naya ghar (EC2 Instance) banaya hai. Is ghar ke bahar ek chowkidar (Security Group/Firewall) betha hai.

* Chowkidar ke paas ek register (Inbound Rules) hai.
* Agar aap register mein likh dein ke *"Ali ko andar aane do (Allow SSH/Port 22)"*, to chowkidar Ali ko andar jaane dega.
* Lekin agar register bilkul khali hai, to chowkidar kisi ko bhi ghar ke andar qadam nahi rakhne dega.

Book ke mutabaq, default settings bilkul theek hain, lekin humein ek ahem tabdeeli karni hai: **"Allow SSH Traffic" ke option ko uncheck (deselect) kar dena hai.**

### Is Architectural Decision Ka Trade-Off:

Pehle zamane mein log SSH chalane ke liye port 22 ko internet par khol dete the. Iska nuqsan yeh hota tha ke poori duniya ke hackers aap ke server ke bahar khare ho kar bar bar galat passwords ya keys try karte rehte the (jise brute-force attack kehte hain).

**Figure 3.7** ke mutabaq, jab hum "Allow SSH traffic" ko deselect kar dete hain:

<div align="center">
  <img src="./images/10.png" width="600"/>
</div>

* **No Inbound Traffic:** Hamare chowkidar (Firewall) ke register mein koi rule nahi hoga. Iska matlab hai ke bahar se koi bhi hamari machine se connect nahi ho sakega.
* **Maximum Security:** Port 22 mukammal tor par band hogi, jis se hackers hamari VM par attack nahi kar sakenge.
* **The Question:** Agar sab raste band hain, to hum khud kaise connect honge? Iska jawab hum aage **Systems Manager (SSM)** ke zariye seekhenge, jo bina koi port khole humein andar jaane deta hai!

#### Figure 3.7 Ka Breakdown:

* **Network & Subnet:** Is mein default settings ko hi rehne diya gaya hai (`vpc-a17392c7`).
* **Auto-assign public IP:** Isay **Enable** rakhna hai taake machine ko internet par ek pehchan mil sake.
* **Firewall (security groups):** Wizard ek naya security group banayega jiska naam `launch-wizard-1` hoga, jis mein hum ne SSH, HTTP aur HTTPS ke tamam boxes ko khali (unchecked) chora hai taake koi incoming traffic allow na ho.

---

## ATTACHING STORAGE

Ab baari aati hai machine ke dimaag aur storage ki, jahan hamara Operating System aur zaroori files save hongi. Isay hum **Root Volume** kehte hain.

### Root Volume Kya Hai? (ELI5)

Yeh bilkul aap ke laptop ki **C: Drive** ki tarah hai. Jab bhi computer on hota hai, wo isi drive se windows ya linux ko chala raha hota hai. AWS par is hard disk/SSD ko **EBS (Elastic Block Store)** kaha jata hai, jo network ke zariye hamari machine se jura hota hai.

**Figure 3.8** ke mutabaq, default settings mein humein **8 GiB** size aur **gp2** volume type milta hai.

<div align="center">
  <img src="./images/11.png" width="600"/>
</div>

#### 2026 Modern AWS Standard Update:

* **Book Standard (gp2):** Book mein **gp2 (General Purpose 2)** volume type select karne ka kaha gaya hai.
* **2026 Industry Standard (gp3):** Aaj ke dor (2026) mein AWS par **gp3** default aur standard ban chuka hai. Hum aap ko highly recommend karenge ke aap **gp3** select karein.
* **Kyun?** gp3 volumes **gp2 se 20% sasti** hain. Is ke sath sath, gp2 mein speed (IOPS) is baat par depend karti thi ke aap ki disk kitni badi hai (choti disk = kam speed). Lekin gp3 mein aap ko choti se choti disk par bhi bina kisi extra charge ke **3000 IOPS** ki behtareen speed milti hai.


* **Free Tier Note:** AWS Free Tier ke andar aap ko har mahine **30 GB** tak ki EBS storage muft milti hai, is liye 8 GB volume bilkul free chalega.

---

## SETTING ADVANCED DETAILS

Ab hum setup ke aakhri aur nihayat hi ahem hisse par pahuche hain jise **Advanced details** kehte hain. Yahan hum ne ek cheez set karni hai jise **IAM Role** kaha jata hai.

### IAM Role Kya Hota Hai? (Bacho Wali Example)

Imagine karein ke AWS ek bohot bada secure campus hai. Aap ki EC2 machine us campus mein ek worker hai.

* Agar is worker ne campus ke doosre departments (jaise Systems Manager) mein ja kar koi file lani hai ya baat karni hai, to usay ek identity card ya badge chahiye hoga.
* **IAM Role** bilkul wahi security badge hai jo hum is machine ko pehna dete hain.
* Jab machine ke paas yeh badge hota hai, to AWS ki doosri services khud-ba-khud samajh jati hain ke is machine ko aane ki ijazat hai, aur humein machine ke andar koi password ya secret key rakhne ki zaroori nahi parti.

Humein yeh IAM role is liye chahiye taake **Systems Manager (SSM)** hamari virtual machine ke sath bina kisi open port ya key pair ke ek secure connection (SSH) bana sake.

---

## CREATING IAM ROLE

Chunke hamare paas pehle se koi role nahi bana hua, is liye hum ek naya browser tab khol kar **IAM Console** par jayenge aur niche diye gaye steps ke mutabaq role banayenge:

<div align="center">
  <img src="./images/12.png" width="600"/>
</div>

### Step-by-Step Role Creation:

1. **Open IAM Dashboard:** Browser mein `[https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/)` par jayein.
2. **Go to Roles:** Left side par navigation menu se **Roles** par click karein aur phir **Create role** ka button dabayein.
3. **Select Trusted Entity (Figure 3.9):**
* **AWS service** ko select karein (kyunke hum AWS ki service yani EC2 ko yeh permission de rahe hain).
* **Use case** dropdown se **EC2** ko select karein (Figure 3.9 ke mutabaq). Iska matlab hai ke yeh badge sirf EC2 instances hi pehan sakte hain. Phir **Next** par click karein.


4. **Add Permissions (Figure 3.10):**

<div align="center">
  <img src="./images/13.png" width="600"/>
</div>

* Search bar mein filter karein: `AmazonSSMManagedInstanceCore`.
* Is policy ke sath bane checkbox ko tick mark karein (jaise Figure 3.10 mein dikhaya gaya hai).
* **Yeh Policy Kyun Zaroori Hai?** Is policy ke andar wo saari permissions maujood hain jo AWS Systems Manager ke Agent (SSM Agent) ko hamari machine par sahi tareeqe se chalne ke liye chahiye hoti hain. Is ke bagair hum browser se machine ko control nahi kar sakenge. Phir **Next** dabayein.


<div align="center">
  <img src="./images/14.png" width="600"/>
</div>

5. **Name and Create (Figure 3.11):**
* Role ka naam exactly **`ec2-ssm-core`** likhein. Isay small letters mein likhiye ga kyunke aage chal kar hum ne isi naam ko use karna hai.
* Description mein aap likh sakte hain: *"Allows EC2 instances to interact with SSM."*
* Page ke aakhir mein ja kar **Create role** button par click kar dein.



#### EC2 Tab Par Wapas Aana (Figure 3.12):

<div align="center">
  <img src="./images/15.png" width="600"/>
</div>

Ab wapas apne pehle wale EC2 tab par aayein jahan hum machine launch kar rahe the:

* Scroll kar ke sab se niche **Advanced details** section mein jayein.
* **IAM instance profile** ke dropdown ke barabar mein ek **Reload (refresh) button** hoga, use click karein taake naya role list mein aa jaye.
* Dropdown se **`ec2-ssm-core`** ko select kar lein (Figure 3.12).
* Baqi tamam settings ko default par hi choren (jaise Spot Instances ko unchecked chor dein).

---

## LAUNCHING THE EC2 INSTANCE

Mubarak ho! Aap ne machine ki tamam settings mukammal kar li hain. Ab aakhri rasm baqi hai.

### Figure 3.13 (The Summary Panel) Ka Breakdown:

<div align="center">
  <img src="./images/16.png" width="600"/>
</div>

Aap ke browser ke right side par ek summary box nazar aa raha hoga, jo aap ko aap ke poore setup ka ek quick overview deta hai:

* **Number of instances:** `1` (Hum abhi sirf ek hi machine launch kar rahe hain).
* **Software Image (AMI):** `Amazon Linux 2` (Ya AL2023 modern standard ke mutabaq).
* **Virtual server type (Instance type):** `t2.micro` (Jo Free Tier ke liye eligible hai).
* **Firewall (security group):** `New security group` (Jo hum ne launch-wizard-1 ke naam se banaya hai, jis mein tamam incoming raste band hain).
* **Storage (volumes):** `1 volume(s) - 8 GiB` (Hamari SSD hard drive).

Ab bina kisi hichkichahat ke, niche diye gaye orange button **"Launch instance"** par click kar dein!

AWS background mein physical server par aap ke liye space allocate karega, OS install karega, storage attach karega, aur mushkil se 1-2 minutes mein aap ki virtual machine chalne ke liye bilkul tayar ho jayegi. Aap ne kamyabi se apna pehla cloud server launch kar diya hai!

---
