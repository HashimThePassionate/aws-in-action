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


## Connecting to your virtual machine

Ab jab hamari virtual machine chal rahi hai, toh us se kaam lene ke liye humein us mein **login** karna hoga. Login karne ke baad, hum apne real-world use case yani **LinkChecker** ko install aur run karenge taake broken links dhoond sakein.

Yeh exercise toh ek example hai, lekin ek baar jab aap ko virtual machine ki administrator (root) access mil jati hai, toh aap ke paas system ka poora control hota hai. Aap apni marzi ka operating system configure kar sakte hain aur koi bhi application install kar sakte hain.

### AWS Systems Manager Session Manager: Ek Bilkul Naya Tareeqa (ELI5 Explanation)

Pehle ke zamane mein server se connect karne ke liye humein ghar ka darwaza (Port 22) kholna parta tha aur ek chabi (SSH Key Pair) chahiye hoti thi. Lekin AWS Systems Manager Session Manager is se bilkul alag aur behtareen tareeqe se kaam karta hai.

Iski asaan misal **"Outbound Walkie-Talkie"** jaisi hai:

* Purane tareeqe (SSH) mein aap bahar se server ke andar ghusne ki koshish karte hain. Is ke liye server par **Port 22 (SSH)** ka khula hona zaroori hai. Agar darwaza khula hai, toh hackers bhi us par hamla kar sakte hain.
* Session Manager (SSM) mein hum server ka darwaza bahar se bilkul band rakhte hain (koi port khuli nahi hoti). Lekin server ke andar ek chota sa worker betha hota hai jise **SSM Agent** kehte hain.
* Yeh SSM Agent khud andar se AWS Systems Manager ke sath ek secure connection (tunnel) banata hai. Jab aap connect hote hain, toh aap AWS Console ke zariye us agent se baat karte hain. Darwaza andar se khulta hai, bahar se koi rasta nahi hota!

#### Session Manager Ke Faide:

* **No upfront Key Pairs:** Aap ko pehle se koi SSH `.pem` ya `.ppk` key file sambhal kar rakhne ki zaroori nahi hai. AWS temporary keys ke zariye connection handle karta hai.
* **No Inbound Ports Needed:** Aap ko Firewall (Security Group) mein Port 22 kholne ki bilkul zaroorat nahi parti. Is se aap ka attack surface (wo raste jahan se hacker hamla kar sakein) na hone ke barabar reh jata hai.

#### Session Manager Ki Sharaat (Requirements):

* **OS Support:** Yeh Amazon Linux 2, CentOS, Oracle Linux, Red Hat Enterprise Linux, SUSE Linux, macOS, aur Windows Server par kaam karta hai.
* **SSM Agent:** Yeh agent system mein install hona chahiye. Amazon Linux 2 (aur modern AL2023), macOS (>10.14), Ubuntu (>16.04), aur Windows Server par yeh pehle se install aata hai.
* **IAM Role:** Aap ke instance par wo IAM role (`ec2-ssm-core`) laga hona chahiye jo hum ne pichle step mein banaya tha.

---

### Step-by-Step Connection Guide (Figures Ka Breakdown)

Hum ne pichle section mein jo machine launch ki thi, wo in tamam sharaat ko poora karti hai. Ab us se connect karne ke liye niche diye gaye steps follow karein:

#### 1. Figure 3.14 Ka Breakdown: EC2 Dashboard Par Instance Dhoondna

<div align="center">
  <img src="./images/17.png" width="600"/>
</div>

Agar aap **Figure 3.14** ko dekhein:

* EC2 dashboard ke left menu se **Instances** par click karein.
* Aap ke samne chalne wale servers ki list aa jayegi. Yahan hamari machine **"mymachine"** nazar aa rahi hogi.
* **Instance State:** Check karein ke iska status green color mein **Running** ho chuka ho (is mein 1-2 minutes lag sakte hain).
* **Details Section:** Niche details tab mein aap ko machine ki public/private IP addresses aur doosri ahem networking details nazar aayengi.
* Machine ke naam ke sath bane checkbox ko select karein aur top right corner par maujood **Connect** button par click karein.

#### 2. Figure 3.15 Ka Breakdown: Connection Method Select Karna

<div align="center">
  <img src="./images/18.png" width="600"/>
</div>

Jab aap Connect par click karenge, toh ek naya page khulega jaisa **Figure 3.15** mein dikhaya gaya hai:

* Yahan aap ko connect karne ke mukhtalif tabs nazar ayenge (EC2 Instance Connect, Session Manager, SSH client, EC2 Serial Console).
* Hum ne **Session Manager** wale tab par click karna hai.
* Niche diye gaye orange rang ke **Connect** button par click kar dein.

#### 3. Figure 3.16 Ka Breakdown: Browser Ke Andar Terminal

<div align="center">
  <img src="./images/19.png" width="600"/>
</div>

* Orange button dabane ke thode hi seconds baad, aap ke browser ke andar ek kali screen (Terminal/Shell) khul jayegi, jaisa **Figure 3.16** mein dikhaya gaya hai.
* **Admin Login:** Yeh terminal aap ko direct system ke admin (`ssm-user`) ke tor par login karwa deta hai.
* Aap screen par direct command likhna shuru kar sakte hain. Kaam khatam hone par aap top right par maujood **Terminate** button dabakar session band kar sakte hain.

---

## SSH and SCP

Browser ke zariye direct terminal access milna bohot hi shandar sahulat hai. Lekin baaz auqat, ek DevOps engineer ke tor par aap chahenge ke aap apne local computer ke terminal (jaise VS Code, Terminal, ya Putty) se direct connection banayein.

Khaas tor par tab jab aap ko **SCP (Secure Copy Protocol)** ke zariye apne local computer se koi file server par transfer karni ho ya server se koi file download karni ho.

* **SSM Tunneling (Modern Way):** 2026 ke standards ke mutabaq, aap ko SSH aur SCP chalane ke liye bhi Port 22 kholne ki zaroorat nahi hai. Aap apne local computer par **AWS CLI** aur **Session Manager Plugin** install kar ke SSM tunnel ke zariye direct SSH/SCP chala sakte hain. Yeh intehai secure aur professional tareeqa hai.

---

## Installing and running software manually

Ab hum apne asli maqsad ki taraf aate hain. Hamari virtual machine launch ho chuki hai aur hum terminal mein enter ho chuke hain. Humein **LinkChecker** tool install karna hai jise chalne ke liye **Python** environment chahiye.

> ⚠️ **Nihayat Ahem 2026 Technical Update (Amazon Linux 2 vs Amazon Linux 2023):**
> Book mein **Amazon Linux 2 (AL2)** ke commands diye gaye hain jo `amazon-linux-extras` tool use karte hain.
> Agar aap aaj ke modern standard operating system **Amazon Linux 2023 (AL2023)** par hain, toh wahan `amazon-linux-extras` ka tool khatam kar diya gaya hai. AL2023 mein package manager `yum` se upgrade ho kar `dnf` ban chuka hai aur Python 3 pehle se install aata hai ya direct standard repository se mil jata hai.
> Neeche hum ne **dono operating systems** (Book ka AL2 aur Modern AL2023) ke mutabaq codes likh diye hain taake aap ka practical kabhi fail na ho!

### Step 1: Install Python

#### Book Method (For Amazon Linux 2):

AL2 mein extra packages install karne ke liye `amazon-linux-extras` library ka use kiya jata hai. Python 3.8 install karne ke liye yeh command run karein:

```bash
sudo amazon-linux-extras install python3.8 -y
```

* **Code Explanation:**
* `sudo`: SuperUser Do (Yani administrator ki taqat se command chalana taake software install ho sake).
* `amazon-linux-extras`: Amazon ka ek tool jo curated (test shuda) software packages provide karta hai.
* `-y`: Jab installation ke doran system aap se pooche ke *"Kya aap waqai install karna chahte hain (y/n)?"*, toh yeh option usay pehle hi "Yes" bol deta hai taake process ruke nahi.



#### Modern Method (For Amazon Linux 2023 / 2026 Standard):

Agar aap modern AL2023 use kar rahe hain, toh simply standard package manager `dnf` ke zariye Python install karein:

```bash
sudo dnf install python3 -y
```

---

### Step 2: Install LinkChecker

Python install hone ke baad, hum Python ke package manager (`pip3`) ke zariye **LinkChecker** install karenge:

```bash
pip3 install linkchecker
```

* **Code Explanation:**
* `pip3`: Python ka official package manager hai jo online repository se python libraries aur tools dhoond kar download karta hai.
* `install linkchecker`: Yeh package manager ko batata hai ke "LinkChecker" naam ka tool download aur install karo.
* **Note:** Hum ne is command ke sath `sudo` use nahi kiya. Iska matlab hai ke yeh tool sirf hamare current user ke local folder (`~/.local/bin/`) mein install hoga, system-wide nahi. Yeh security ke lihaz se behtar practice hai.



---

### Step 3: Run LinkChecker to Scan a Website

Ab hamara tool bilkul tayar hai. Hum kisi bhi website ko scan kar sakte hain. Example ke tor par hum apni website par isay run karte hain:

```bash
~/.local/bin/linkchecker -r 2 https://cloudonaut.io
```

* **Code Explanation:**
* `~/.local/bin/linkchecker`: Yeh hamare local user ke bin folder mein mojood linkchecker tool ka mukammal rasta (path) hai.
* `-r 2`: Yeh recursion level hai. Iska matlab hai ke crawler website ke main page par jayega (Level 1) aur phir un pages par moojood links ke andar mazeed 1 step gehrayi tak jayega (Level 2). Agar hum recursion level set na karein, toh yeh poore internet par nikal jayega aur kabhi rukh nahi payega!
* `[https://cloudonaut.io](https://cloudonaut.io)`: Yeh target website ka URL hai jise hum check karna chahte hain. (Aap yahan apni marzi ki website ka link de sakte hain).



#### Output Ka Breakdown (Line by Line Analysis):

Jab tool run hota hai, toh terminal par is tarah ka output aata hai:

* **Error Example:**
```text
URL          `/images/2022/02/terminal.png'
Name         `Connect to your EC2 instance using SSH the modern way'
Parent URL   https://cloudonaut.io, line 379, col 1165
Real URL     https://cloudonaut.io/images/2022/02/terminal.png
Check time   2.959 seconds
Size         0B
Result       Error: 404 Not Found

10 threads active, 5 links queued, 72 links in 87 URLs checked
1 thread active, 0 links queued, 86 links in 87 URLs checked
```


* **Line 1 & 2:** Kis file/image ya text par toota hua link laga hua hai aur us ka naam kya hai.
* **Line 3 & 4:** Kis parent page par yeh link mojood hai (taake aap asani se use dhoond sakein) aur us link ka asli URL kya hai.
* **Line 7 (`Result Error: 404 Not Found`):** Sab se ahem line! Yeh batati hai ke target page internet par mojood nahi hai, yani link toota hua hai.


* **Overall Statistics (Aakhri Summary):**
```text
Statistics:
Downloaded: 66.01KB.
Content types: 26 image, 29 text, 0 video, 0 audio, 3 application, ...
URL lengths: min=21, max=160, avg=56.

That's it. 87 links in 87 URLs checked. 0 warnings found. 1 error found.
Stopped checking at 2022-04-04 09:02:55+000 (22 seconds)
```


* **Downloaded:** Tool ne total kitna data scan kiya.
* **Content types:** Scanner ko kitne images aur text pages mile.
* **Summary:** Total **87 URLs** check huay, jin mein se **0 warnings** aur **1 error** (broken link) mila. Poora process mukammal hone mein **22 seconds** lagay.



Website ke pages jitne zyada honge, crawler ko scan karne mein utna hi zyada waqt lagega. Lekin aakhir mein yeh aap ko tamam tootay hue links ki saaf list de dega taake aap unhein website par ja kar asani se theek kar sakein.

---


## Monitoring and debugging a virtual machine

Jab aap cloud par koi virtual machine (VM) chalate hain aur us par apni application run karte hain, toh hamesha sab kuch perfect nahi chalta. Kabhi koi application crash ho jati hai, toh kabhi server slow ho jata hai. Ek DevOps engineer ke tor par aap ka sab se ahem kaam yeh dhoondna hota hai ke *"Galti kahan hui?"*

Is galti (error) ko dhoondne aur system ki sehat ka andaza lagane ke liye AWS humein **Monitoring** (dekh-bhaal) aur **Debugging** (galtiyan dhoondne) ke behtareen tools deta hai. In mein se sab se asaan aur pehla tareeqa yeh hai ke hum virtual machine ke **System Logs** (diary/history) ko check karein.

---

## Showing logs from a virtual machine

Imagine karein ke aap ka computer on nahi ho raha aur screen bilkul kaali hai. Aap ko kaise pata chalega ke andar hardware ya operating system mein kya kharabi hai? Agar aap physical server par kaam kar rahe hote, toh aap monitor screen par chalne wali boot lines ko dekhte. AWS console par hum bina kisi SSH connection ke yeh sab dekh sakte hain.

### System Logs Dekhne Ka Step-by-Step Tareeqa (Figure 3.17 Reference)

<div align="center">
  <img src="./images/20.png" width="600"/>
</div>

Virtual machine ke system logs dekhne ke liye niche diye gaye steps ko follow karein:

1. AWS Management Console par ja kar **EC2 Dashboard** kholin: `[https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)`.
2. Left side par navigation options se **Instances** par click kar ke chalne wale servers ki list kholin.
3. Apni chalne wali machine par click kar ke use select karein.
4. Top right par **Actions** menu par click karein, phir **Monitor and troubleshoot** par jayein, aur wahan **Get system log** par click kar dein.
5. Aap ke samne ek screen khul jayegi jahan virtual machine ke chalne ke tamam logs nazar aayenge, bilkul waise hi jaise **Figure 3.17** mein dikhaya gaya hai.

#### Figure 3.17 Ka Detailed Breakdown (Logs Ki Explanation)

* **The Boot Output:** Figure 3.17 mein humein ek kaali screen par safaid text nazar aa raha hai. Yeh operating system ke boot hone ke messages hain (jaise `cloud-init` ka chalna, system services ka start hona, aur SSH host key fingerprints ka generate hona).
* **Copy and Download Buttons:** Is screen ke top right corner par **Copy log** aur **Download** ke buttons hote hain. Agar aap ke system mein koi bada masla aa raha hai, toh aap in logs ko download kar ke apne computer par aaram se analyze kar sakte hain ya kisi expert ko bhej sakte hain.

#### Troubleshooting Ki Real-World Strategy:

Agar operating system start hote hi kisi error par ruk jata hai, toh wo error aap ko in logs ke aakhir mein saaf nazar aa jayega.

* Agar aap ko error samajh na aaye, toh aap is operating system (AMI) ko banane wali company se raabta kar sakte hain, AWS Support ko ticket bhej sakte hain, ya phir AWS ki official community **AWS re:Post** (`[https://repost.aws](https://repost.aws)`) par apna sawal post kar sakte hain.
* **Ahem Note:** Jab aap virtual machine ko launch karte hain, toh system logs ko is console viewer mein load hone mein **kuch minutes** lagte hain, is liye sabar se kaam lena zaroori hai!

---

## Monitoring the load of a virtual machine

Logs dekhne ke baad doosra ahem sawal yeh hota hai ke: *"Kya hamari virtual machine par load bohot zyada hai? Kya CPU ya Network apni aakhri limit tak pahuch chuke hain?"*

AWS is sawaal ka jawab dene ke liye automatic metrics collect karta hai jise hum **Amazon CloudWatch** kehte hain.

### VM Ki Load Metrics Dekhne Ka Tareeqa (Figure 3.18 Reference)

<div align="center">
  <img src="./images/21.png" width="600"/>
</div>

1. Apne **EC2 Console** par jayein aur **Instances** ki list kholin.
2. Apni chalne wali machine par click karein.
3. Niche diye gaye panel mein se **Monitoring** tab par click karein.
4. Yahan aap ko CPU, Network, aur Disk ke graphs nazar aayenge. **Figure 3.18** ke mutabaq, agar aap **Network in (Bytes)** wale graph par jana chahte hain, toh us ke kone mein bane teen dots (...) par click karein aur **Enlarge** (bada) kar lein.

#### Figure 3.18 Ka Grafical Breakdown (The CloudWatch Metric)

* **The Graph:** Figure 3.18 mein hum ek graph dekh rahe hain jo graph par aane wali traffic (Bytes) ko waqt ke hisab se dikha raha hai. Shuru mein jab system start hua toh network traffic ka ek bada spike (uncha graph) aaya, jo baad mein kam ho kar normal line par aa gaya.
* **Time Range Selector:** Aap graph ke upar se waqt select kar sakte hain (jaise *1h, 3h, 12h, 1d, 3d, 1w*) taake aap dekh sakein ke pichle kuch ghanton ya dino mein machine par kitna load tha.

### Sab Se Ahem Concept: Memory (RAM) Ka Missing Hona (The Outside-In Rule)

> ⚠️ **Bacho Ki Tarah Asaan Explanation (The Inspector Analogy):**
> Imagine karein ke AWS ek bahar khara inspector hai jo aap ke ghar (Virtual Machine) ko bahar se dekh raha hai.
> * Inspector dekh sakta hai ke ghar se kitna dhuan nikal raha hai (CPU Usage).
> * Inspector dekh sakta hai ke ghar ke andar kitne log ja rahe hain aur kitne bahar aa rahe hain (Network In/Out).
> * Inspector dekh sakta hai ke ghar ke bahar kitna kachra phenka ja raha hai (Disk Read/Write).
> * **Lekin:** Inspector ghar ki deewaron ke par nahi dekh sakta! Use yeh nahi pata ke ghar ke andar ke cupboards (RAM/Memory) kitne bhare hue hain.
> 
> 
> Bilkul isi tarah, kyunke hypervisor machine ko bahar se monitor karta hai, is liye AWS ke paas default mein **Memory (RAM) usage** ki koi metric nahi hoti!
> If you need RAM metrics, aap ko virtual machine ke andar ek chota sa software (CloudWatch Agent) install karna parta hai jo andar se RAM ka data collect kar ke AWS CloudWatch ko bhejta hai.

#### Basic vs. Detailed Monitoring (2026 Price & Standard Update):

* **Basic Monitoring (Default & Free):** Yeh default mein har machine par on hoti hai. Is mein aap ke graphs har **5 minutes** baad naye data ke sath update hote hain. Iska koi kharcha nahi hota.
* **Detailed Monitoring (Paid):** Agar aap ko har **1 minute** ka bariki se data chahiye (jo production systems ke liye zaroori hota hai), toh aap isay enable kar sakte hain, lekin is ke alag se paise lagte hain.

---

## Shutting down a virtual machine

AWS par chalne wali har cheez ke paise lagte hain. Agar aap ne apni virtual machine ko chala kar chor diya, toh mahine ke aakhir mein aap ko ek bada shock lag sakta hai! Is liye jab kaam khatam ho jaye, toh machine ko band karna ya mita dena sab se behtareen DevOps practice hai.

AWS par virtual machine ki life-cycle (zindagi) ko chalane ke liye 4 mukhtalif actions hote hain:

1. **Start:** Agar aap ki machine pehle se **Stopped** state mein hai, toh aap isay dobara chalane ke liye "Start" karte hain. (Nayi machine banani ho toh "Launch" karte hain).
2. **Stop (Remote control se TV band karna):**
* Jab aap machine ko stop karte hain, toh computer shutdown ho jata hai.
* **Cost Control:** Sasti parne wali state! Jab machine stop hoti hai, toh aap se CPU aur RAM ka koi paisa nahi liya jata.
* **Storage Charges:** Lekin yaad rakhein! Jo storage (EBS disk/8 GB SSD) machine ke sath juri hui hai, wo data ko safe rakhne ke liye cloud par maujood rehti hai. Is liye storage ka bohot chota sa kharcha chalta rehta hai.
* **Different Host:** Jab aap stopped machine ko dobara start karenge, toh AWS background mein use kisi doosre physical computer (different host) par chala sakta hai, lekin aap ka data hard drive mein bilkul salamat rahega.


3. **Reboot:** Computer ko restart karna. Machine usi physical host par rehti hai, data salamat rehta hai, aur system bas jaldi se band ho kar dobara on ho jata hai.
4. **Terminate (Machine ko dustbin mein phenk dena):**
* Terminate ka matlab hai machine ko hamesha ke liye delete kar dena.
* Ek baar machine terminate ho jaye, toh aap isay dobara kabhi start nahi kar sakte.
* Is ke sath attach tamam storage (EBS disks) aur public/private IP addresses bhi mita diye jate hain.
* **No more charges:** Is state mein aate hi aap ka har qism ka kharcha (CPU, RAM, aur Storage) mukammal tor par khatam ho jata hai.



### Stopping vs. Terminating Ka Flowchart (Figure 3.19 Reference)

**Figure 3.19** humein in dono states ke darmeyan sab se bada farq samajhata hai:

<div align="center">
  <img src="./images/22.png" width="600"/>
</div>

* Upay wale flowchart mein aap dekh sakte hain ke running machine ko stop kar ke dobara start karna bilkul safe aur mumkin hai.
* Niche wale flowchart mein dikhaya gaya hai ke running machine ko jab hum terminate (delete) kar dete hain, toh us par ek bada **forbid/cross symbol** lag jata hai. Yani ab yeh machine hamesha ke liye khatam ho chuki hai.

#### Real-World Examples (Kahan kya use karna hai?):

* **Scenario A (Proof of Concept):** Aap ne sirf testing ke liye ek chota sa system banaya, check kiya ke code chal raha hai ya nahi. Kaam khatam hone par aap isay **Terminate** kar dein taake bilkul kharcha na ho.
* **Scenario B (Daily Development Work):** Aap ek testing server par kaam kar rahe hain. Aap raat ko 6 baje office se ghar chale jate hain aur subah 9 baje wapas aate hain. Raat ke 15 ghante server fazool chalega. Behtareen strategy yeh hai ke shaam ko server ko **Stop** kar dein aur subah aa kar **Start** kar lein. Is se aap ki company ke bohot saare paise bachenge!
* **Scenario C (Canceled Client):** Kisi client ka contract khatam ho gaya. Aap ne un ke database ka backup le kar save kar liya, aur ab un ke servers ko **Terminate** kar ke free kar diya.

---

## Cleaning up

Hum ne is chapter ke shuru mein jo **"mymachine"** naam ka EC2 server launch kiya tha, ab hamara us par practical kaam mukammal ho chuka hai. Free tier ke paise bachane aur account ko saaf rakhne ke liye ab hum isay **Terminate** karenge.

### Step-by-Step Cleanup Process:

1. Apne browser mein **EC2 Console** par jayein: `[https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)`.
2. Left navigation panel se **Instances** select karein taake saare servers ki list samne aa jaye.
3. Hamari machine **`mymachine`** ke row par click kar ke use select karein.
4. Top menu mein **Instance state** wale dropdown button par click karein aur wahan se **Terminate instance** select kar lein.
5. Ek warning message aayega, us par **Terminate** click kar ke confirm kar dein.

Kuch hi seconds mein machine ka status *Shutting down* aur phir *Terminated* ho jayega. Mubarak ho! Aap ne AWS EC2 par virtual machine launch karne, configure karne, run karne, debug karne, aur safely cleanup karne ka poora lifecycle behtareen tareeqe se seekh liya hai.

---

## Changing the size of a virtual machine

Cloud computing ka sab se bada aur dilchasp faida yehi hai ke aap kisi bhi waqt apni virtual machine ka size chota ya bada kar sakte hain. Is capability ko technical zaban mein **Vertical Scaling (Scale Up / Scale Down)** kehte hain.

### Vertical Scaling Kya Hoti Hai? (Bacho Wali Example)

Iski misal bilkul aisi hai ke shuru mein aap ne ek choti dukan (t2.micro) kholi kyunke aap ke paas customer kam the. Lekin jaise hi dukan par customer ka rush (workload) barh gaya, aap ne dukan ko bara kar ke ek bada mall (m5.large) bana diya taake zyada logon ko handle kiya ja sake. Cloud par hum CPU aur RAM ke sath bilkul yahi karte hain! Agar computing power kam paray toh machine bari kar do (Scale Up), aur agar kharcha bachana ho toh dukan phir choti kar do (Scale Down).

Chaliye, pehle hum ek choti virtual machine launch karte hain:

1. AWS Management Console mein **EC2 Dashboard** par jayein: `[https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)`.
2. Orange rang ka **Launch Instances** button click karein.
3. Virtual machine ka naam **`growingup`** rakhein.
4. Operating system ke liye **Amazon Linux 2 AMI** select karein (2026 standards ke mutabaq aap modern **Amazon Linux 2023** bhi chun sakte hain).
5. Instance type mein **`t2.micro`** select karein (jo ke free tier ke liye bilkul muft hai).
6. Connection settings mein **Proceed without a Key Pair** select karein.
7. Network aur Storage settings ko default par hi choren (yani 8 GB SSD disk).
8. **Advanced Details** ke andar ja kar **IAM instance profile** ke dropdown se hamara banaya hua secure badge **`ec2-ssm-core`** select karein.
9. Sab se niche ja kar **Launch instance** par click kar dein.

Ab aap ke paas AWS ki sab se choti aur sasti virtual machines mein se ek (`t2.micro`) chal rahi hai.

---

### Machine Ki Power Ko Check Karna (Linux Commands Deep Explanation)

Aap ke DevOps engineer banne ke safar aur Linux administration ko behtareen tareeqe se seekhne ke liye system ki specifications ko check karna aana bohot zaroori hai.

Session Manager ke zariye terminal se connect karein aur niche di gayi do technical commands run karein:

#### Command 1: `cat /proc/cpuinfo`

Linux operating system mein `/proc` ek aisi virtual (pseudo) directory hoti hai jo hard disk par real space nahi leti, balkey direct Linux Kernel se system hardware ka live data utha kar dikhati hai. Is command ke zariye hum CPU ki information dekhte hain.

```bash
$ cat /proc/cpuinfo
processor   : 0
vendor_id   : GenuineIntel
cpu family  : 6
model       : 63
model name  : Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz
stepping    : 2
microcode   : 0x46
cpu MHz     : 2399.915
cache size  : 30720 KB
...
```

**Output Breakdown:**

* **`processor : 0`**: Iska matlab hai hamari machine ke paas sirf **1 CPU Core** hai (Linux mein counting 0 se shuru hoti hai).
* **`vendor_id : GenuineIntel`**: Yeh processor Intel company ne banaya hai.
* **`model name : Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz`**: Yeh AWS data center ke physical server ka core model aur uski speed (2.40 Gigahertz) batata hai.
* **`cpu MHz : 2399.915`**: Processor ki live running frequency/speed.

#### Command 2: `free -m`

Yeh command hamari virtual machine ki RAM (Memory) ki details Megabytes (`-m`) mein dikhati hai taake hum dekh sakein ke kitni memory khali hai aur kitni use ho rahi hai.

```bash
$ free -m
              total   used   free   shared   buff/cache   available
Mem:            965     93    379        0          492         739
Swap:             0      0      0
```

**Output Breakdown (Table of Memory):**

| Column Name | Value (MB) | ELI5 Description (Bacho ki tarah asaan) |
| --- | --- | --- |
| **total** | `965` | Machine ke paas total takreeban **1 GB** (965 MB) RAM hai. |
| **used** | `93` | Abhi is waqt system sirf 93 MB RAM use kar raha hai. |
| **free** | `379` | Bilkul khali RAM jo abhi kisi bhi kaam mein nahi hai. |
| **buff/cache** | `492` | Linux system chalte waqt files ko tezi se load karne ke liye RAM ka ek hissa temporary cache mein rakh leta hai. |
| **available** | `739` | **Sab se ahem column!** Iska matlab hai agar aap koi naya software run karenge toh usay total 739 MB RAM mil sakti hai bina system ko slow kiye. |
| **Swap** | `0` | Virtual disk memory jo RAM khatam hone par storage ko use karti hai, wo abhi disabled (0) hai. |

---

### Machine Ka Size Kaise Barhein? (The Stop/Start Design Trade-Off)

Agar aap ki application ka load barh jaye, use zyada CPU ya RAM chahiye, toh hum machine ka size barha sakte hain. Lekin yahan ek nihayat hi ahem technical asool (trade-off) yaad rakhein: **Chalti gari ka engine tabdeel nahi kiya ja sakta!**

* **The Design Decision:** AWS par jab machine chal rahi hoti hai, toh wo physical hardware ke ek specific slot ke sath bandhi hoti hai. Instance type badalne ke liye humein machine ko **Stop** karna parta hai.
* **The Trade-Off:** Is ka matlab hai ke machine ko resize karte waqt thodi der ke liye **Downtime** (system ka band hona) hoga. Lekin iska faida yeh hai ke aap ka storage database aur files (jo EBS disk par hain) mukammal tor par mehfooz rehte hain aur naye server par asani se mount ho jate hain.

Machine ko stop karne ke steps:

1. EC2 Console par jayein aur **Instances** select karein.
2. Apni machine `growingup` par click karein.
3. **Instance State** dropdown par click kar ke **Stop Instance** select karein aur tab tak wait karein jab tak status "Stopped" na ho jaye.

> ⚠️ **Cost Warning:** Machine ka size barha kar `m5.large` ya is se upar karne par AWS aap se hourly charges leta hai (yeh free tier mein nahi aati). Is liye kaam khatam hote hi isay terminate karna mat bhooliye ga!

---

### Instance Type Change Karna (Figure 3.20 Breakdown)

<div align="center">
  <img src="./images/23.png" width="600"/>
</div>

Jab machine mukammal tor par stop ho jaye, toh hum naya engine (size) choose karenge:

1. Machine select rakhin, **Actions** button par click karein, aur **Instance Settings** par jayein.
2. Wahan **Change Instance Type** par click karein.

#### Figure 3.20 Ka Console Breakdown:

* **Figure 3.20** mein humein "Change instance type" ka dialogue box nazar aa raha hai.
* Is mein hamari machine ki ID (`i-0e1839ff9466a9c5e`) aur uska current type (`t2.micro`) likha hua hai.
* Hum ne dropdown menu par click kar ke **`m5.large`** select karna hai aur orange rang ke **Apply** button par click kar dena hai.

#### 2026 Modern Standard Update:

* Book mein `m5.large` use karne ka kaha gaya hai. Lekin 2026 mein m5 family purani ho chuki hai.
* Aaj ke dor mein is se kahin sasti aur behtareen option **`m6i.large`** (Intel Based) ya **`m7i.large`** hai. Agar aap AWS Graviton (ARM processor) use karna chahein, toh aap **`m6g.large`** ya **`m7g.large`** select kar sakte hain jo ke nihayat hi tez aur cost-effective hain!

Apply karne ke baad, machine ko select karein aur **Instance State** se **Start Instance** par click kar ke on kar lein.

---

### Naye Size Ko Verify Karna

Ab jab machine bade engine ke sath on ho chuki hai, toh dobara Session Manager se terminal connect karein aur unhi dono commands ko run kar ke dekhein ke kya farq aaya hai:

```bash
$ cat /proc/cpuinfo
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 85
model name      : Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz
stepping        : 7
microcode       : 0x500320a
cpu MHz         : 3117.531
cache size      : 36608 KB
...

processor       : 1
vendor_id       : GenuineIntel
cpu family      : 6
model           : 85
model name      : Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz
stepping        : 7
microcode       : 0x500320a

cpu MHz        : 3100.884
cache size     : 36608 KB
...
```

**New Output Analysis:**

* Ab aap ke terminal par ek ke badle do CPU cores ki list dikh rahi hogi: **`processor : 0`** aur **`processor : 1`**! Yani CPU power double ho chuki hai.
* Model name bhi update ho kar heavy server grade chip ban chuka hai: **`Intel(R) Xeon(R) Platinum 8259CL CPU @ 2.50GHz`**.

```bash
$ free -m
              total   used   free   shared   buff/cache   available
Mem:           7737    108   7427        0          202        7406
Swap:             0      0      0
```

**New Output Analysis:**

* **`total (7737 MB)`**: Hamari RAM ab **1 GB** se barh kar takreeban **8 GB** (7737 Megabytes) ho chuki hai!
* **`free (7427 MB)`**: System ke paas ab pehle se 20 guna zyada khali memory moojood hai. Hamari application ab bina ruke aur fast tareeqe se heavy tasks perform kar sakegi.

---

## Cleaning up

Kyunke `m5.large` (ya 2026 ke modern equivalent) instances par hourly charges lagte hain, is liye testing mukammal hone ke baad system ko mita dena hi ek behtareen DevOps rule hai taake fazool bills na banin.

1. AWS Console par **EC2 Instances** ki list mein jayein.
2. Apni machine **`growingup`** ke checkbox ko select karein.
3. Top menu mein **Instance State** par click kar ke **Terminate Instance** select karein.
4. Warning pop-up par **Terminate** daba kar confirm kar dein.

Kuch hi der mein machine completely delete ho jayegi aur aap ke billing charges foran ruk jayenge. Aap ne kamyabi se EC2 instances par vertical scaling ka poora concept step-by-step seekh liya hai!

----