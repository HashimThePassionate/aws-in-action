# Programming your infrastructure: The command line, SDKs, and CloudFormation

<details>
<summary><strong>Table of Contents</strong></summary>

- [Room lighting as a service](#room-lighting-as-a-service)
- [Virtual machines on AWS](#virtual-machines-on-aws)
- [AWS Tools on top of the HTTP API](#aws-tools-on-top-of-the-http-api)
- [AWS Control via API](#aws-control-via-api)
  - [How the API works](#how-the-api-works)
  - [Automation and the DevOps movement](#automation-and-the-devops-movement)
  - [Why should you automate?](#why-should-you-automate)
- [Using the command-line interface](#using-the-command-line-interface)
  - [Installing the CLI](#installing-the-cli)
  - [Configuring the CLI](#configuring-the-cli)
  - [Using the CLI](#using-the-cli)
  - [Dealing with long output](#dealing-with-long-output)
  - [Automating with the CLI](#automating-with-the-cli)
  - [Programming with the SDK](#programming-with-the-sdk)
  - [Installing and getting started with Node.js](#installing-and-getting-started-with-nodejs)
  - [Controlling virtual machines with SDK: nodecc](#controlling-virtual-machines-with-sdk-nodecc)
  - [Infrastructure as Code](#infrastructure-as-code)
  - [Inventing an infrastructure language: JIML](#inventing-an-infrastructure-language-jiml)
  - [Using AWS CloudFormation to start a virtual machine](#using-aws-cloudformation-to-start-a-virtual-machine)
  - [Anatomy of a CloudFormation template](#anatomy-of-a-cloudformation-template)
  - [Creating your first template](#creating-your-first-template)
  - [Updating infrastructure using CloudFormation](#updating-infrastructure-using-cloudformation)
  - [Alternatives to CloudFormation](#alternatives-to-cloudformation)
  - [Summary](#summary)
</details>

## Room lighting as a service

Farz karein hum kamre ki lights ko ek "service" ke taur par bechna chahte hain. Agar hum chahein ke koi software ke zariye kamre ki light ko off kar sake, toh hamein ek hardware device ki zaroorat hogi jise hum **relay** kehte hain. Yeh relay light ke electric circuit ke sath connect hota hai.

* **Relay kya karta hai?** Yeh ek aisa physical switch hota hai jo software ke ishare par on ya off ho sakta hai.
* **Interface ki zaroorat:** Is hardware device (relay) ke paas koi na koi rasta (interface) hona chahiye jis se hamara software usay commands (jaise "switch off" ya "switch on") bhej sake.
* **Aasaan Lafzon Mein:** Jab hamare paas ek relay aur usay control karne wala interface hoga, tabhi hum logon ko "room lighting as a service" offer kar sakenge. Client ko khud uth kar button dabane ki zaroorat nahi padegi, sab kuch software ke zariye background mein ho jayega.

---

## Virtual machines on AWS

Ek virtual machine (VM) ko chalane ke liye bohot saare hardware aur software components ki zaroorat hoti hai. Agar hum khud apna setup lagana chahein, toh hamein yeh sab cheezein chahiye hongi:

* **Power supply:** Lagatar chalne wali bijli.
* **Networking gear:** Routers, switches, aur cables taake internet se connectivity rahe.
* **Host machine:** Ek heavy physical computer ya server.
* **Operating system (OS):** Server ko chalane ke liye bunyadi operating system.
* **Virtualization layer (Hypervisor):** Ek khas software jo is physical server ko chhote chhote virtual hisson (VMs) mein divide karta hai.

Khushkismati se, **AWS (Amazon Web Services)** yeh saara physical jhanjhat (hardware aur software) hamare liye khud chalaata aur manage karta hai. Is se bhi behtareen baat yeh hai ke hum is poore heavy system ko software ke zariye control kar sakte hain.

AWS humein ek **API (Application Programming Interface)** deta hai. Is API ki madad se hum **HTTPS requests** bhej kar AWS ke har hissay ko control kar sakte hain. Iska sab se bada faida yeh hota hai ke hum aisa software likh sakte hain jo khud-ba-khud AWS par:

* Virtual Machines (VMs) ko chalaye (spin up kare).
* In-memory caches (bohot tez chalne wali temporary memory) banaye.
* Data warehouses (bohot bade databases) khara kare, aur is tarah ki bohot si cheezein automatic set up kar de.

---

## AWS Tools on top of the HTTP API

Seedha HTTP API ko call karna ek bohot **low level task** (mushkil aur mitti se juda kaam) hai. Is mein bohot saara repetitive (bar bar hone wala thaka dene wala) kaam karna padta hai, jaise:

* **Authentication (Tasdeeq):** Har request ke sath yeh sabit karna ke hum waqai authorized user hain (cryptographic signatures banana).
* **Data (de)serialization:** Apne data ko code se JSON ya XML format mein tabdeel karna (serialize) aur wahan se aane wale raw data ko wapis samajhne ke qabil banana (deserialize).

Isi mushkil se bachne ke liye, AWS ne is basic HTTP API ke upar kuch asaan tools banaye hain jo hamare kaam ko asaan karte hain:

* **Command-line interface (CLI):** Is tool ke zariye aap apne computer ke terminal ya command prompt se direct commands likh kar AWS API ko call kar sakte hain.
* **Software development kit (SDK):** Yeh coding libraries hoti hain jo lagbhag har bari programming language (jaise Python, JavaScript, Java) ke liye available hain. Yeh code likhte waqt AWS API calls ko bohot asaan bana deti hain.
* **AWS CloudFormation:** Yeh ek kamaal ka tool hai. Is mein hum templates (YAML ya JSON files) likhte hain jo hamare poore system ki banawat (state of infrastructure) ko bayan karti hain. AWS CloudFormation in templates ko khud-ba-khud parhta hai aur unhein background mein sahi API calls mein tabdeel (translate) kar deta hai.

---

## AWS Control via API

AWS par har ek cheez ko control karne ka rasta sirf aur sirf API se guzarta hai. Hum HTTPS protocol ka istemal karte hue REST API ko call karte hain, jaisa ke **Figure 4.1** mein dikhaya gaya hai.

### Figure 4.1 ka Breakdown (System Architecture):

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

Agar hum di gayi tasveer (**Figure 4.1**) ko ghaur se dekhein, toh is system ke mukhtalif layers niche diye gaye tareeqay se kaam karte hain:

* **Administrator (User):** Sab se upar ya bahar hamara administrator khara hai jo "Manage services" ki request bhejta hai.
* **The API (The Gatekeeper):** Administrator direct physical servers ko hath nahi laga sakta. Us ki request sab se pehle **API** ke ek funnel (cone-shaped interface) par takraati hai. Yeh API ek darwaze ki tarah hai jo har request ko filter, check aur authenticate karta hai.
* **Services Layer (The Middle Core):** API ke andar hamari saari AWS services majood hoti hain:
* *Compute:* Virtual machines (EC2).
* *App:* Message queues aur search capabilities.
* *Enterprise:* Directory services aur email management.
* *Deployment:* Access control (users ki permissions) aur monitoring.
* *Storage:* Object store (S3) aur long-term archiving.
* *Database:* Relational databases (RDS) aur NoSQL databases.
* *Networking:* Domain Name System (Route 53) aur Virtual Networks (VPC).


* **Software & Hardware Layer (The Bottom Foundation):** Yeh saari services sab se niche chalne wale physical aur virtual infrastructure par baithi hain. Is foundation ko teen major pillars mein baanta gaya hai:
* **Compute** (Processors aur Memory).
* **Network** (Routers, Switches, aur Internet Cables).
* **Storage** (Hard drives aur Solid State drives).



Is poore diagram se humein yeh samajh aata hai ke hum sirf ek API call ke zariye virtual machine start kar sakte hain, 1 Terabyte (TB) storage bana sakte hain, ya poora Hadoop cluster khara kar sakte hain. AWS par "everything" ka matlab waqai har ek cheez hai jo API ke zariye chalti hai.

---

## How the API works

Aayein ab practical taur par dekhte hain ke yeh API background mein kaise kaam karta hai.

Farz karein aap ne Amazon S3 (jo ke ek online file storage/object store hai) par kuch files upload keen. S3 ke baare mein hum mazeed details chapter 7 mein parhenge.

Ab aap check karna chahte hain ke kya upload kamyabi se ho gaya hai. Is ke liye aap ko S3 bucket ke andar majood files ki list dekhni padegi. Agar aap bilkul "raw" (direct bina kisi tool ke) HTTP API use karein, toh aap ko ek **GET request** bhejni paregi.

### HTTP Request Code

```http
GET / HTTP/1.1
Host: BucketName.s3.amazonaws.com
Authorization: [...]

```

**Is Request ki Step-by-Step Detail:**

* `GET / HTTP/1.1`: `GET` ek HTTP method hai jiska matlab hai ke hum server se data mangwa rahe hain. `/` (slash) ka matlab root path hai, yani poori bucket ki details. `HTTP/1.1` protocol ka standard version hai jo use ho raha hai.
* `Host: BucketName.s3.amazonaws.com`: Yeh us server ka address (hostname) hai jahan hamara data para hai. Yaad rahe ke internet ke basic network protocols (TCP/IP) ko domain names ka nahi pata hota, woh sirf IP addresses aur ports ko samajhte hain. DNS (Domain Name System) is hostname ko IP address mein translate karta hai.
* `Authorization: [...]`: Yeh security ke liye sab se ahem hissa hai. Is mein hamare secret signatures hote hain taake AWS ko pata chale ke yeh request waqai aap ki taraf se aayi hai aur aap ke paas is bucket ko dekhne ki ijazat hai.

### HTTP Response Code

AWS is request ko check karne ke baad humein yeh response bhejta hai:

```http
HTTP/1.1 200 OK
x-amz-id-2: [...]
x-amz-request-id: [...]
Date: Mon, 09 Feb 2015 10:32:16 GMT
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
[...]
</ListBucketResult>

```

**Is Response ki Step-by-Step Detail:**

* `HTTP/1.1 200 OK`: `200 OK` ka matlab hai ke hamari request bilkul safal (successful) rahi aur server ne hamara kaam kar diya hai.
* `x-amz-id-2` aur `x-amz-request-id`: Yeh AWS ke apne khas tracking keys hain (headers). Agar koi masla pesh aaye, toh in IDs ke zariye AWS engineers debug karte hain ke request mein kya kharabi aayi thi.
* `Date: Mon, 09 Feb 2015 10:32:16 GMT`: Yeh response generate hone ki exact date aur time hai.
* `Content-Type: application/xml`: Yeh batata hai ke jo data niche aa raha hai woh XML language ke format mein hai.
* `<?xml ...> <ListBucketResult ...>`: Yeh response ki main body hai. Is XML document ke andar hamari files ki poori list aur un ki details hoti hain (jise yahan `[...]` se zahir kiya gaya hai).

### Summary & Modern Perspective (2026)

Direct is tarah raw HTTPS requests likhna aur XML ko parse karna bohot mushkil aur thaka dene wala kaam hai. Isi liye hum CLI aur SDKs ka istemal karte hain jo back-end par yeh saare XML formatting aur signatures ke mushkil kaam hamare liye khud ba khud kar dene hain.

Aaj ke dor (2026) mein, hum modern SDKs aur IAM (Identity and Access Management) roles ka use karte hain jo authorization ko mazeed secure aur bilkul automated bana dete hain, lekin un ke piche chalne wali asal bunyad yahi HTTP API hai.

---

## Automation and the DevOps movement

DevOps movement ka asal maqsad software development (Dev) aur operations (Ops) ko aapas mein ek sath milana hai.

Pehle zamane mein kya hota tha? Code likhne wale (Developers) bilkul alag thalag apna kaam karte the aur jab code tayyar ho jata tha, toh woh operations team (Operators) ki jholi mein phenk dete the ke "Ab isay server par chalao aur manage karo." Is se dono teams ke beech ladaaiyan aur masle paida hote the. DevOps ne is deewar ko gira diya.

Writer ke mutabaq, is deewar ko girane ke **do bundeadi tareeqay** hain:

* **Using mixed teams (Mili-juli teams):** Is tareeqay mein ek hi team banayi jaati hai jis mein developers aur operators dono sath baithte hain.
* **Developers ki nayi zimmedari:** Ab developers sirf code likh kar farigh nahi ho jate, balkay unhein operational tasks bhi dekhne hote hain, jaise ke **on-call** rehna (agar aadhi raat ko system baith jaye, toh developer ki neend kharab hogi aur woh usay theek karega).
* **Operators ka shuruati role:** Operators ko software development ke bilkul shuruati phase (start) se hi shamil kiya jata hai. Iska faida yeh hota hai ke woh developers ko pehle hi bata dete hain ke software ko server par chalana kis tarah aasaan banaya jaye.


* **Introducing a new role (Ek naya darmiyani kirdar):** Is tareeqay mein ek naya role (jaise DevOps Engineer ya SRE) laya jata hai jo developers aur operators ke beech ke faaslay (gap) ko khatam karta hai.
* Yeh naya banda dono teams se bohot zyada baat-cheet (communicate) karta hai aur un tamam masail ka khayal rakhta hai jo dono worlds (code likhne aur server chalane) ko aapas mein jodte hain.



---

### Asal Maqsad (The Goal)

DevOps ka sab se bada goal yeh hai ke software ko **bohot tezi se (rapidly)** customer tak pahunchaya jaye, lekin is tezi ke chakkar mein software ki **quality kharab nahi honi chahiye**. Is maqsad ko paane ke liye dono teams ke darmiyan behtareen communication aur collaboration (mil kar kaam karna) bohot zaroori hai.

---

### Automation aur DevOps ka Gehrayi se Talluq (How Automation Helps)

Automation (kaam ko khud-kar banana) ne DevOps culture ko phalne phoolne mein sab se zyada madad di hai. Iski wajah yeh hai ke automation ke zariye hum development aur operations ke beech ke agreement ko **code ki shakal (codify)** de dete hain.

Agar aap din mein **kayi baar (multiple times a day)** naya code live (deploy) karna chahte hain, toh aap yeh hath se (manually) kabhi nahi kar sakte. Aap ko poora process automate karna padega jise hum **CI/CD Pipeline** kehte hain.

Aayein is poore pipeline ke process ko **bachon ki tarah step-by-step** samajhte hain:

1. **Code Commit (Code Jama Karwana):** Developer jaise hi apna naya code repository (jaise GitHub) mein daalta (commit karta) hai, pipeline active ho jati hai.
2. **Automated Build & Test (Khud-kar tayaari aur testing):** System khud-ba-khud us naye code ko uthata hai, usay build karta hai (compile karta hai), aur pehle se likhe gaye automated tests ke zariye check karta hai ke kahin koi bug toh nahi hai.
3. **Deployment to Testing (Testing server par bhejna):** Agar code tests paas kar leta hai, toh system usay khud hi ek testing environment (staging area) mein install kar deta hai.
4. **Acceptance Tests (Aakhri tasdeeq):** Testing environment mein pahunchte hi kuch mazeed bare tests (acceptance tests) shuru ho jate hain jo yeh check karte hain ke kya poora system users ke liye sahi chal raha hai ya nahi.
5. **Production (Live karna):** Jaise hi acceptance tests paas hote hain, naya code bina kisi insani madad ke khud-ba-khud **Production (asal customers ke paas)** live ho jata hai.
6. **Real-time Monitoring & Logs (Nazar rakhna):** Kaam yahan khatam nahi hota! Code live hone ke baad, system ko lagatar ghaur se monitor karna padta hai aur real-time mein logs ko analyze karna padta hai taake furan pata chal sake ke naye badlao (change) se koi masla toh nahi khara hua.

---

### Ek Kamaal ka Architectural Concept: Isolated Infrastructure

Writer yahan ek bohot hi behtareen aur modern tarika samjha raha hai: **Agar aap ki infrastructure automated hai (yani aap code ke zariye servers, networks aur databases bana sakte hain), toh aap har naye code change ke liye ek naya alag-thalag (isolated) system khara kar sakte hain.**

* **Pehle kya hota tha?** Sab log ek hi testing server par apna apna code bhejte the, jis se sab ka code aapas mein takra jata tha aur masla dhoondna mushkil ho jata tha.
* **Ab modern tarika kya hai?** Jab bhi koi developer code change karega, automation background mein ek **bilkul naya aur fresh system** (naya virtual machine, naya database, naya network) khara kar degi.
* Us naye isolated system mein tests run honge. Tests khatam hone ke baad, us pure system ko delete (destroy) kar diya jayega. Is se kisi aur ke kaam par koi asar nahi parta.

---

## Why should you automate?

Ab sawal yeh paida hota hai ke hum itni mehnat kar ke automation kyun karein? Hum direct AWS Management Console (jo ke browser mein chalne wala khoobsurat button-wala portal hai) par click click kar ke bhi toh kaam kar sakte hain?

Writer iska bohot hi thos aur practical jawab deta hai:

* **Reusability (Dobara istemal):** Ek script ya blueprint (jaise CloudFormation template) ko aap jitni baar chahein dobara use kar sakte hain. Shuru mein script likhne mein thoda waqt zaroor lagta hai, lekin lambe arsay (long run) mein yeh aap ka ghanton ka kaam seconds mein kar ke bohot saara waqt bachata hai.
* **Speed (Tezi):** Agar aap ne pehle kisi project ke liye koi module (jaise network setup ya database setup) banaya tha, toh aap us purane module ko naye project mein copy-paste kar ke jhatpat naya infrastructure khara kar sakte hain.
* **Repetitive Tasks (Roz-marrah ke thaka dene wale kaam):** Jo kaam aap ko roz ya har hafte baar baar karne parte hain, unhein script ke hawale kar dein taake aap ka dimaag faltu kaamon ke bajaye behtar cheezon par focus kar sake.
* **Pipeline Integration:** Jab aap ka infrastructure automated hota hai, toh aap usay deployment pipeline (CI/CD) ke sath jor sakte hain, jis se software ka poora safar (development se production tak) smooth ho jata hai.
* **The Ultimate Documentation (Sab se behtareen dastawez):** Ek script ya blueprint se behtar koi documentation nahi ho sakti. Iski wajah yeh hai ke:
* **Computer bhi samajhta hai:** Is code ko computer bina kisi galti ke samajhta aur chala sakta hai.
* **Insaan bhi samajhta hai:** Agar aap ne Friday ko koi kaam kiya aur Monday ko aap bhool gaye ke aap ne kya kiya tha, toh aap apni likhi hui script ko dekh kar 1 minute mein sab yaad kar sakte hain.
* **Team ka Backup:** Agar aap bimar ho jayein ya chutti par chale jayein, aur aap ke kisi saathi (coworker) ko aap ka kaam sambhalna pare, toh usay aap se bar bar puchne ki zaroorat nahi padegi. Woh aap ke blueprints (scripts) dekh kar sab samajh jayega aur wahi kaam dobara chala sakega.

---


## Using the command-line interface

AWS CLI (Command-Line Interface) ek bohot hi behtareen aur aasaan tarika hai jis se aap apne computer ki terminal (kaali screen) se direct AWS ke sath baat-cheet kar sakte hain.

* **Har OS Par Chalta Hai:** Yeh tool Linux, macOS, aur Windows teeno operating systems par smoothly chalta hai.
* **Unified Interface:** Iska matlab hai ke aap ko alag-alag services ke liye alag-alag tools install nahi karne parte. Sirf is ek akele tool se aap AWS ki tamam services ko control kar sakte hain.
* **Default JSON Output:** Jab bhi aap CLI ko koi request bhejenge, toh AWS aap ko response mein data **JSON (JavaScript Object Notation)** format mein dega. JSON bilkul ek aasaan list ki tarah hota hai jo curly brackets `{}` mein likha hota hai taake insaan aur computer dono usay aasaani se samajh sakein.

---

## Installing the CLI

AWS CLI ko install karne ka tarika aap ke operating system (OS) par depend karta hai. Agar aap ko installation mein koi bhi mushkil pesh aaye, toh aap guide ke liye is link `[http://mng.bz/AVng](http://mng.bz/AVng)` par ja kar mazeed tareeqay dekh sakte hain.

### LINUX X86 (64-BIT)

Agar aap ke paas standard Intel ya AMD processor wala Linux system hai, toh aap apne terminal mein niche diye gaye commands ko ek-ek kar ke run karein:

```bash
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
$ unzip awscliv2.zip
$ sudo ./aws/install

```

**Commands ka Aasaan Breakdown:**

* `curl "..." -o "awscliv2.zip"`: Yeh command internet se AWS CLI ki setup file (zip format mein) download karti hai aur usay `awscliv2.zip` ke naam se aap ke computer mein save kar deti hai.
* `unzip awscliv2.zip`: Yeh downloaded zip file ko kholti hai (jaise hum kisi gift box ka dabba kholte hain) taake andar majood files nikal sakein.
* `sudo ./aws/install`: Yeh command superuser (system ke boss/administrator) ki ijazat le kar installation script ko chalati hai aur AWS CLI ko aap ke system mein fit kar deti hai.

Apni installation ko verify (check) karne ke liye terminal mein `aws --version` likhein. Aap ka version kam az kam **2.4.0** ya is se naya hona chahiye.

### LINUX ARM

Agar aap ke paas ARM architecture wala Linux computer hai (jaise Raspberry Pi ya AWS Graviton instances), toh un ke liye commands thode badal jate hain kyunke un ka processor alag hota hai:

```bash
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip"
$ unzip awscliv2.zip
$ sudo ./aws/install

```

Yahan hum `x86_64` ki jagah `aarch64` (ARM) package download kar rahe hain. Baqi unzip aur install karne ka tarika bilkul pehle jaisa hi hai. Installation check karne ke liye dobara `aws --version` chalayein.

### MACOS

macOS par AWS CLI install karne ke liye in simple steps ko follow karein:

1. Apne browser mein is link se installer download karein: `[https://awscli.amazonaws.com/AWSCLIV2.pkg](https://awscli.amazonaws.com/AWSCLIV2.pkg)`.
2. Download hone ke baad, is `.pkg` file par double-click karein. Ek asaan sa installation wizard khul jayega (jaise normal software install hota hai), "Next" aur "Continue" daba kar sab users ke liye install kar dein.
3. Apna terminal kholiye aur check karne ke liye `aws --version` run karein. Version kam az kam **2.4.0** hona zaroori hai.

### WINDOWS

Windows par installation ke liye hum Microsoft Installer (MSI) ka use karenge:

1. Sab se pehle is link se installer download karein: `[https://awscli.amazonaws.com/AWSCLIV2.msi](https://awscli.amazonaws.com/AWSCLIV2.msi)`.
2. Downloaded file ko run karein aur installation wizard ke steps ko follow kar ke install kar dein.
3. Windows ke Start menu mein "PowerShell" search karein, us par right-click karein aur **Run as Administrator** select karein (yeh step system ke system-level policies ko set karne ke liye zaroori hai).
4. PowerShell mein niche di gayi command likhein aur Enter dabayein:
```powershell
Set-ExecutionPolicy Unrestricted
```


Yeh command humein is liye chalani padti hai taake hum aage chal kar jo custom scripts chalayenge, Windows unhein security ke naam par block na kare.
5. Ab is administrator wale PowerShell window ko band kar dein, kyunke hamara admin level ka kaam khatam ho chuka hai.
6. Ab dobara normal tarike se PowerShell ko kholiye (bina run as admin ke).
7. Check karne ke liye `aws --version` likhein. Version kam az kam **2.4.0** hona chahiye.

> **WARNING (Khabardar!):** Execution Policy ko `Unrestricted` par set karne se computer par koi bhi bina signature wali script chal sakti hai, jis se malicious (nuksan-deh) scripts ka khatra barh jata hai. Is liye is policy ka use sirf unhi scripts ko chalane ke liye karein jo trusted hon (jaise is book ki scripts). Agar aap is ke baare mein mazeed seekhna chahte hain toh `[http://mng.bz/1MK1](http://mng.bz/1MK1)` par parh sakte hain.

---

## Configuring the CLI

AWS CLI install toh ho gaya, lekin abhi CLI ko yeh nahi pata ke kis AWS account ke sath connect hona hai aur kaun si keys use karni hain. Is kaam ko hum **Configuration** kehte hain.

Abhi tak aap shayad AWS ka "root account" (jo sab se pehla email-password wala account hota hai) use kar rahe the. **Root account ko kabhi bhi aam kaamon ke liye use nahi karna chahiye**, kyunke is ke paas hadd se zyada powers hoti hain. Agar is ki keys chori ho jayein, toh aap ka poora account barbad ho sakta hai.

Isi liye hum ek naya limited user banayenge. Is poore process ko hum niche diye gaye steps aur images ke zariye samajhte hain:

### Step-by-Step User Creation & IAM Setup:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

* **Figure 4.2 (IAM Service Search):** Sab se pehle AWS Management Console (`[https://console.aws.amazon.com](https://console.aws.amazon.com)`) par login karein. Sab se upar search bar mein **"IAM"** (Identity and Access Management) search karein aur pehle option par click kar ke IAM console khol lein. IAM asal mein AWS ka security guard register hai jahan se hum users aur un ki permissions ko manage karte hain.

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

* **Figure 4.3 (IAM Users Page):** Jab IAM page khulega, toh left side ke menu mein se **"Users"** par click karein. Agar aap ke account mein pehle se koi user nahi bana hua, toh yeh list khali hogi. Yahan par right side mein **"Add users"** ke blue button par click karein.

<div align="center">
  <img src="./images/04.png" width="600"/>
</div>

* **Figure 4.4 (User Details & Access Type):** Naye khulne wale page par:
1. *User name* mein likhein: `mycli`.
2. *Select AWS credential type* ke andar **"Access Key - Programmatic Access"** ke check box ko tick karein. Iska matlab hai ke hum is user ko terminal aur code (CLI/SDK) ke zariye access de rahe hain, browser login ke liye nahi.
3. Niche **"Next: Permissions"** button par click karein.


<div align="center">
  <img src="./images/05.png" width="600"/>
</div>

* **Figure 4.5 (Permissions Setup):** Is step mein hum ne is user ko powers deni hain:
1. **"Attach existing policies directly"** wale box par click karein.
2. Niche di gayi list mein se **"AdministratorAccess"** ko dhoond kar select kar lein. (Yeh is user ko full power de dega).
3. **"Next: Tags"** par click karein. Tags lagana zaroori nahi hai, is liye direct **"Next: Review"** par click karein aur aakhri page par **"Create user"** daba dein.

<div align="center">
  <img src="./images/06.png" width="600"/>
</div>

* **Figure 4.6 (Credentials Show & Save):** User kamyabi se ban chuka hai! Ab aap ke samne ek screen aayegi jahan aap ko do bohot hi sensitive keys dikhayi dengi:
1. **Access key ID:** Yeh aap ke user ki "username" ki tarah hai.
2. **Secret access key:** Yeh aap ka "password" hai jo "Show" par click karne se dikhega.


> **WARNING (Intehai Khufia!):** In dono keys ko bilkul safe jagah par copy kar ke rakh lein. Agar yeh keys kisi aur ke hath lag gayeen, toh usay aap ke pooray AWS account ka administrator access mil jayega aur woh aap ke account par bhari bills generate kar sakta hai.



### CLI Configure Karna:

Ab apne computer ka terminal ya PowerShell kholiye (AWS Console nahi, aap ka apna terminal) aur yeh command likhein:

```bash
$ aws configure
```

Jaise hi aap Enter dabayenge, terminal aap se 4 sawal poochega. Aap ne ek-ek kar ke browser se values copy kar ke yahan paste karni hain:

```text
AWS Access Key ID [None]: AKIAIRUR3YLPOSVD72CA
AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1
Default output format [None]: json
```

*(Yaad rahe: Upar di gayi keys sirf misal ke liye hain, aap ne browser mein aane wali apni keys paste karni hain!)*

* **Default region name:** Hum ne `us-east-1` (N. Virginia) likha hai, jo ke standard region hai.
* **Default output format:** Hum ne `json` likha hai taake sara data structured format mein mile.

### CLI ko Test Karna:

Ab check karte hain ke kya hamara CLI sahi chal raha hai. Terminal mein yeh command likhein:

```bash
$ aws ec2 describe-regions
```

Yeh command AWS se kehti hai ke "Mujhe un tamam locations (regions) ki list do jahan tumhare data centers majood hain." Agar sab kuch theek chal raha hai, toh response mein aap ko JSON format mein saare regions ki list mil jayegi:

```json
{
    "Regions": [
        {
            "Endpoint": "ec2.eu-north-1.amazonaws.com",
            "RegionName": "eu-north-1",
            "OptInStatus": "opt-in-not-required"
        },
        ...
        {
            "Endpoint": "ec2.us-west-2.amazonaws.com",
            "RegionName": "us-west-2",
            "OptInStatus": "opt-in-not-required"
        }
    ]
}
```

Mubarak ho! Aap ka AWS CLI bilkul sahi kaam kar raha hai.

---

## Using the CLI

Ab hum seekhte hain ke CLI ko practical use kaise karna hai. Farz karein aap check karna chahte hain ke aap ke account mein is waqt kaun se `t3.micro` size ke virtual computers (EC2 instances) chal rahe hain.

Aap apne terminal mein yeh command run karenge:

```bash
$ aws ec2 describe-instances --filters "Name=instance-type,Values=t3.micro"
```

### Explanations:
* `aws` command line tool ko start karta hai taake aap terminal ya command prompt ke zariye directly AWS services ko control aur manage kar sakein.
* `ec2` batata hai ke aap AWS ki **Elastic Compute Cloud** (yani virtual servers/machines) service ke sath kaam karna chahte hain.
* `describe-instances` ek aisi command hai jo AWS se kehti hai ke mere account mein mojood virtual servers ki mukammal maloomat (jaise status, IP, ID wagera) la kar do.
* `--filters` ek aisi parameter option hai jo poori list nikalne ke bajaye sirf aapki marzi ki specific cheezein dhoondne ke liye use hoti hai.
* `"Name=instance-type,Values=t3.micro"` yeh filter ki condition hai; ismein `Name=instance-type` ka matlab hai ke hum server ke type ya size par shart laga rahe hain, aur `Values=t3.micro` ka matlab hai ke hamein sirf wahi instances chahiye jo `t3.micro` size ki hon.
* Is poori command ka nateeja yeh hota hai ke AWS aapke poore account ko check karta hai aur sirf unhi servers ki details deta hai jo `t3.micro` hardware par chal rahe hain, baaki sab ko ignore kar deta hai.

**Output 1:**
```json
{
    "Reservations": [
        {
            "Groups": [],
            "Instances": [
                {
                    "InstanceId": "i-0123456789abcdef0",
                    "InstanceType": "t3.micro",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    }
                },
                {
                    "InstanceId": "i-0987654321fedcba0",
                    "InstanceType": "t3.medium",
                    "State": {
                        "Code": 16,
                        "Name": "running"
                    }
                }
            ],
            "OwnerId": "123456789012",
            "ReservationId": "r-0123456789abcdef0"
        }
    ]
}
```

agar t3 hai toh yeh ayega

**Output 2:**
```json
{
    "Reservations": []
}
```

Yahan output bilkul khali `[]` aa rahi hai kyunke hum ne abhi tak koi virtual machine (EC2 instance) banayi hi nahi hai.

---

## Dealing with long output

Jab aap AWS CLI chalate hain aur output bohot lambi hoti hai (jaise saari services ya instances ki list), toh aap ka terminal screen flood hone se bachane ke liye system ek default **pager program** use karta hai (aam taur par isay `less` kehte hain).

* **Lambi Output Kaise Dekhein?** Yeh screen par data ko thoda thoda kar ke dikhata hai. Aap Arrow Keys (Up/Down) se upar niche scroll kar sakte hain.
* **Bahar Kaise Niklein?** Agar aap is screen se bahar nikal kar wapis apne normal terminal prompt par aana chahte hain, toh apne keyboard se simply **`q`** key press karein. Aap furan bahar aa jayenge.

### AWS Command ka Basic Dhancha (Syntax):

AWS CLI par har command ka ek standard pattern hota hai:

```bash
$ aws [service] [action] [--options]
```

* `aws`: Main program ka naam.
* `service`: Jis service par kaam karna hai (jaise `ec2`, `s3`, `iam`).
* `action`: Jo kaam aap ne karwana hai (jaise `describe-instances`, `create-bucket`).
* `--options`: Extra settings ya filters jo aap apply karna chahte hain (jaise `--filters` ya `--output`).

### Help Feature:

Agar aap kisi command par phans jayein, toh CLI ke paas teen levels par help majood hoti hai:

1. `aws help`: Yeh aap ko saari available AWS services ki list dikhayega.
2. `aws <service> help`: Yeh batayega ke is specific service ke sath aap kya kya kaam (actions) kar sakte hain (e.g., `aws ec2 help`).
3. `aws <service> <action> help`: Yeh us specific action ko chalane ka poora tarika aur us ke options samjhata hai (e.g., `aws ec2 describe-instances help`).

---

## Automating with the CLI

Ab hum real automation ki taraf barhte hain. Hum ek script likhenge jo hamare liye ek temporary Linux computer banayegi, humein us se connect karei, aur jab hamara kaam khatam ho jaye toh usay delete (terminate) kar degi taake fuzool paise na kharch hon.

### IAM role ec2-ssm-core

Is script ko chalane ke liye ek IAM role ki zaroorat hoti hai jiska naam **`ec2-ssm-core`** hai (jo hum ne pehle banaya tha). Yeh role hamari virtual machine ko yeh taqat deta hai ke woh AWS Systems Manager (SSM) ke sath safely communicate kar sake, jis se hum bina kisi SSH key ke direct terminal se connect ho sakte hain.

### Script Ke Kaam Karne Ka Flow:

1. Naya virtual computer (VM) start karna.
2. Session Manager ke zariye us machine se connect hone mein hamari madad karna.
3. Hamare kaam khatam karne ka intezar karna.
4. Kaam khatam hote hi machine ko hamesha ke liye delete (terminate) kar dena.

---

### JMESPath `--query` Option (JSON se Deemi-Keemat Data Chun-na)

Jab hum AWS CLI chalate hain, toh response mein bohot bada JSON data milta hai jo dekhne mein dimaag ghumaye daita hai. Lekin script likhte waqt humein sirf ek khas cheez chahiye hoti hai (jaise sirf machine ki `ImageId` ya `InstanceId`).

Is bade database se kaam ki cheez nikalne ke liye hum **JMESPath** query language ka use karte hain jo `--query` option ke zariye kaam karti hai.

Aayein isko practical samjhein. Agar hum Amazon Linux 2 ki images list karein:

```bash
$ aws ec2 describe-images --filters "Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2"
```

### Explanations:
* `aws` command line tool ko start karta hai taake aap terminal ya command prompt se directly AWS services ko manage aur control kar sakein.
* `ec2` yeh batata hai ke aap AWS ki **Elastic Compute Cloud** (yani virtual servers/machines) service ke sath kaam karna chahte hain.
* `describe-images` AWS se kehti hai ke hamare liye **Amazon Machine Images (AMIs)** ki maloomat talash karke laao (AMI ek tarah ki template hoti hai jisse naye servers banaye jate hain).
* `--filters` ek aisi option hai jo be-shumaar images ki list mein se sirf wahi image dhoondne ke liye use hoti hai jo aapki zaroorat ke mutabiq ho.
* `"Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2"` poori filter ki shart (condition) hai jo ek specific pattern wale naam ki image search karti hai.
* Is shart ke andar `Name=name` yeh wazeh karta hai ke hum image ke naam (title) ke hisaab se search kar rahe hain.
* Ismein `Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2` woh specific format ya pattern hai jisse image ka naam match hona chahiye.
* `amzn2-ami-hvm` yeh batata hai ke yeh **Amazon Linux 2** ki HVM (Hardware Virtual Machine) type image hai.
* `2.0.202*` mein `*` ek wildcard hai, jiska matlab hai ke yeh saal 2020 se lekar aage ke kisi bhi version (jaise 2021, 2024, 2026 etc.) wali matching image ko utha lega.
* `-x86_64` yeh batata hai ke processor ka architecture 64-bit standard (Intel ya AMD) hona chahiye.
* `-gp2` yeh batata hai ke image ke sath jo storage (disk) hai woh General Purpose SSD (gp2) type ki hai.
* Is poori command ka nateeja yeh hota hai ke AWS aapke account ya region mein mojood Amazon Linux 2 ki wahi specific images dhoond kar la deta hai jo is pattern se milti hain, jisse aapko instance banate waqt sahi AMI ID mil jati hai.

Iska output bohot bada hota hai jis mein dheron details hoti hain, jaise:

```json
{
  "Images": [
    {
      "ImageId": "ami-0ce1e3f77cd41957e",
      "State": "available"
    }
  ]
}
```

Humein machine chalane ke liye sirf `"ami-0ce1e3f77cd41957e"` chahiye, baqi ka kachra nahi chahiye. Toh hum `--query` ka use karenge:

```bash
$ aws ec2 describe-images --filters "Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2" --query "Images[0].ImageId"
```

### Explanations:
* `aws` command line tool ko start karta hai taake aap terminal ya command prompt se directly AWS services ko manage aur control kar sakein.
* `ec2` yeh batata hai ke aap AWS ki Elastic Compute Cloud (virtual servers) service ke sath kaam karna chahte hain.
* `describe-images` AWS se kehti hai ke hamare liye Amazon Machine Images (AMIs) ki maloomat talash karke laao.
* `--filters` ek aisi option hai jo be-shumaar images ki list mein se sirf wahi image dhoondne ke liye use hoti hai jo aapki zaroorat ke mutabiq ho.
* `"Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2"` poori filter ki shart hai jo Amazon Linux 2 ki 64-bit gp2 storage wali images ko match karti hai jismein saal ka pattern (`202*`) wildcard ke sath ho.
* `--query` AWS CLI ka ek ahem feature hai jo output data ko apni marzi ke mutabiq chhota ya specific banane ke liye istemal hota hai, taake poora lamba JSON data dekhne ke bajaye sirf kaam ki cheez mile.
* `"Images[0].ImageId"` is query ka exact rule hai; ismein `Images` list ko represent karta hai, `[0]` ka matlab hai ke jo pehli image search result mein aaye sirf usay uthaao, aur `.ImageId` ka matlab hai ke us image ka sirf ID number (jaise `ami-061ac2e015473fbe2`) screen par dikhaao.
* Is poori command ka nateeja yeh hota hai ke terminal par lambi-chauri details aane ke bajaye sirf ek clean AMI ID print ho kar milti hai, jise aap direct CloudFormation templates ya scripts mein use kar sakte hain.

**Output:**

```text
"ami-146e2a7c"
```

Yahan hamare paas direct Image ID aa gayi, lekin yeh abhi bhi double quotes `""` ke andar hai kyunke output standard JSON format mein hai.

Quotes ko hatane ke liye aur bilkul saaf-suthra raw text hasil karne ke liye hum `--output text` ka option lagate hain:

```bash
$ aws ec2 describe-images --filters "Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2" --query "Images[0].ImageId" --output text
```

**Output:**

```text
ami-146e2a7c
```

Ab quotes gayab ho chuke hain, aur is output ko hum aasaani se apni scripts ke variables mein use kar sakte hain!

---

### Where is the code located?

Is book ka tamam code GitHub par is repository mein majood hai: `[https://github.com/AWSinAction/code3](https://github.com/AWSinAction/code3)`. Aap is link se poore code ka snapshot download kar sakte hain: `[https://github.com/AWSinAction/code3/archive/main.zip](https://github.com/AWSinAction/code3/archive/main.zip)`.

Yahan hum do alag scripts dekhenge:

1. **Bash Script** (Linux aur macOS ke liye).
2. **PowerShell Script** (Windows ke liye).

---

## LINUX AND MACOS

Linux aur macOS par chalne wali script ka path `/chapter04/virtualmachine.sh` hai. Isay chalane ke liye pehle terminal mein permission set karni padti hai taake system ko pata chale ke yeh ek executable file hai:

```bash
chmod +x virtualmachine.sh
./virtualmachine.sh
```

### Listing 4.1 Creating and terminating a virtual machine from the CLI (Bash)

Niche di gayi script ko dhyan se parhein, hum ne isay bilkul modern tarike se likha hai aur is ke har ek step ko aasaan kar ke samjhaya hai:

```bash
#!/bin/bash -e
# '-e' flag ka matlab hai ke agar script mein koi bhi command fail ho jaye, toh script aage chalne ke bajaye furan ruk jaye.

# Step 1: Sab se latest Amazon Linux 2 image ki ID dhoond kar variable mein store karna.
AMIID="$(aws ec2 describe-images --filters \
"Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2" \
--query 'Images[0].ImageId' --output text)"

# Step 2: Hamare account ka default network (VPC ID) dhoondna.
VPCID="$(aws ec2 describe-vpcs --filter "Name=isDefault, Values=true" \
--query 'Vpcs[0].VpcId' --output text)"

# Step 3: Us VPC ke andar default subnet ID nikalna jahan machine deploy hogi.
SUBNETID="$(aws ec2 describe-subnets --filters \
"Name=vpc-id, Values=$VPCID" --query 'Subnets[0].SubnetId' \
--output text)"

# Step 4: Virtual Machine (EC2 Instance) ko create aur run karna.
# Is mein hum size 't2.micro' aur safety permission profile 'ec2-ssm-core' de rahe hain.
INSTANCEID="$(aws ec2 run-instances --image-id "$AMIID" \
--instance-type t2.micro --subnet-id "$SUBNETID" \
--iam-instance-profile "Name=ec2-ssm-core" \
--query 'Instances[0].InstanceId' --output text)"

echo "Waiting for $INSTANCEID to boot up..."

# Step 5: AWS ko kehna ke jab tak machine fully active na ho jaye, wait karein.
aws ec2 wait instance-running --instance-ids "$INSTANCEID"

echo "Instance is up and running!"
echo "Connect to this instance using AWS Session Manager by visiting this link:"
echo "https://console.aws.amazon.com/systems-manager/session-manager/$INSTANCEID"

# Step 6: Script ko yahan rok dena jab tak user Enter nahi dabata.
read -p "Press [Enter] key to terminate and delete $INSTANCEID ..."

# Step 7: Jaise hi enter dabega, machine ko delete karne ki command chalegi.
aws ec2 terminate-instances --instance-ids "$INSTANCEID" > /dev/null
echo "Terminating $INSTANCEID ..."

# Step 8: Tab tak wait karna jab tak machine mukammal tor par delete (terminate) na ho jaye.
aws ec2 wait instance-terminated --instance-ids "$INSTANCEID"

echo "done. Everything cleaned up successfully!"
```

### Explanations:
* `#!/bin/bash -e` batata hai ke yeh ek Bash script hai, aur `-e` flag ka matlab hai ke agar script ke andar koi bhi command fail ho jaye, to script wahin foran ruk jaye gi aur aage nahi chalegi.
* `AMIID="$(aws ec2 describe-images ...)"` ek command hai jo dynamically sab se latest Amazon Linux 2 image ki ID dhoondti hai aur usay `AMIID` naam ke variable mein save kar leti hai.
* `VPCID="$(aws ec2 describe-vpcs ...)"` aapke AWS account ka default VPC ID dhoond kar `VPCID` variable mein store karti hai taake resource sahi network mein bane.
* `SUBNETID="$(aws ec2 describe-subnets ...)"` us default VPC ke andar mojood subnet ki ID nikal kar `SUBNETID` variable mein save karti hai jahan server deploy hoga.
* `INSTANCEID="$(aws ec2 run-instances ...)"` sab variables (`AMIID`, `SUBNETID`) ko use karke ek naya `t2.micro` server launch karti hai aur `ec2-ssm-core` profile lagakar naye banne wale server ki ID ko `INSTANCEID` variable mein store kar leti hai.
* `echo "Waiting for $INSTANCEID to boot up..."` terminal par ek message print karta hai ke server start ho raha hai, bara-e-karam intezar karein.
* `aws ec2 wait instance-running --instance-ids "$INSTANCEID"` ek aisi AWS CLI command hai jo tab tak script ko roke rakhti hai jab tak woh specific instance mukammal tor par "running" state mein na aa jaye.
* `echo "Instance is up and running!"` screen par message dikhata hai ke server ab puri tarah chal pada hai.
* `echo "Connect to this instance..."` aur uske sath wala `echo` link wala line user ko AWS Session Manager ke zariye server se connect karne ka direct URL console par print karke deta hai.
* `read -p "Press [Enter] key to terminate..."` script ko pause (rok) deta hai aur user ke Enter key dabane ka intezar karta hai, taake jab tak aap kaam karna chahein server chalta rahe.
* `aws ec2 terminate-instances ... > /dev/null` jaise hi user Enter dabata hai, yeh command us server ko delete (terminate) karne ke liye bhej deti hai, aur `> /dev/null` uske aane wale extra text output ko chhipa deta hai.
* `echo "Terminating $INSTANCEID ..."` screen par print karta hai ke server ab delete ho raha hai.
* `aws ec2 wait instance-terminated --instance-ids "$INSTANCEID"` tab tak intezar karta hai jab tak AWS se server puri tarah khatam (terminate) na ho jaye.
* `echo "done. Everything cleaned up successfully!"` aakhri message print karta hai ke kaam mukammal ho gaya aur sab kuch safai se band ho gaya hai.

---

## Cleaning up

> **Zaroori Baat:** Agay barhne se pehle terminal par **Enter** key dabana mat bhooliyega! Agar aap ne enter nahi dabaya toh virtual machine chalti rahegi aur AWS aap ko fuzool ka bill bhejta rahega.

---

## WINDOWS

Windows users ke liye hum ne yahi same kaam karne wali script PowerShell mein likhi hai jo `/chapter04/virtualmachine.ps1` par majood hai. Is file par right-click kar ke **"Run with PowerShell"** select karein.

### Listing 4.2 Creating and terminating a virtual machine from the CLI (PowerShell)

```powershell
# ErrorActionPreference = "Stop" ka matlab hai ke agar koi bhi error aaye toh script furan ruk jaye.
$ErrorActionPreference = "Stop"

# Step 1: Amazon Linux 2 ki Image ID dhoondna.
$AMIID = aws ec2 describe-images --filters `
"Name=name,Values=amzn2-ami-hvm-2.0.202*-x86_64-gp2" `
--query "Images[0].ImageId" --output text

# Step 2: Default VPC ID nikalna.
$VPCID = aws ec2 describe-vpcs --filter `
"Name=isDefault, Values=true" `
--query "Vpcs[0].VpcId" --output text

# Step 3: Default Subnet ID nikalna.
$SUBNETID = aws ec2 describe-subnets `
--filters "Name=vpc-id, Values=$VPCID" --query "Subnets[0].SubnetId" `
--output text

# Step 4: Machine launch karna.
$INSTANCEID = aws ec2 run-instances --image-id$AMIID `
--instance-type t2.micro --subnet-id $SUBNETID `
--iam-instance-profile "Name=ec2-ssm-core" `
--query "Instances[0].InstanceId" --output text

Write-Host "Waiting for virtual machine $INSTANCEID ..."

# Step 5: Machine ke ready hone ka wait karna.
aws ec2 wait instance-running --instance-ids $INSTANCEID
Write-Host "$INSTANCEID is up and running!"
Write-Host "Connect to the instance using Session Manager via this link:"
Write-Host "https://console.aws.amazon.com/systems-manager/session-manager/$INSTANCEID"

# Step 6: User ke Enter press karne ka wait karna.
Write-Host "Press [Enter] key to terminate $INSTANCEID ..."
Read-Host

# Step 7: Machine delete karna.
aws ec2 terminate-instances --instance-ids $INSTANCEID
Write-Host "Terminating $INSTANCEID ..."

# Step 8: Complete deletion ka wait karna.
aws ec2 wait instance-terminated --instance-ids $INSTANCEID
Write-Host "done. Everything cleaned up successfully!"
```

*(Note: PowerShell mein long commands ko toray rakhne ke liye line ke aakhir mein backtick ``` ka sign use hota hai).*

---

## Cleaning up

> **Zaroori Baat:** Windows users bhi dhyan dein ke script chala kar enter dabayein taake virtual machine delete ho jaye aur koi extra charge na lagay.

### CLI Solution Ke Limitations (Kamiyan):

Agarchay CLI se kaam automate ho jata hai, lekin is mein kuch baray trade-offs aur maslay hain:

* **Ek waqt mein ek machine:** Yeh script sirf ek hi virtual machine ko manage kar sakti hai.
* **Operating System dependency:** Windows ke liye alag script chalani par rahi hai aur Mac/Linux ke liye alag. Koi ek common platform nahi hai.
* **Text-based application:** Yeh sirf ek command-line tool hai, is mein koi programming-level logic ya proper web integration aasaani se nahi ki ja sakti.

Inhi kamiyon ko door karne ke liye, hum agle step mein seekhenge ke kaise **AWS SDK (Software Development Kit)** ka use kar ke is process ko mazeed behtareen banaya jaye!

---

## Programming with the SDK

AWS humein mukhtalif programming languages aur platforms ke liye SDKs (Software Development Kits) provide karta hai, jin mein shamil hain:

* JavaScript/Node.js
* Java
* .NET
* PHP
* Python
* Ruby
* Go
* C++

### SDK Kya Hai Aur Yeh Hamari Zindagi Kaise Aasaan Banata Hai?

> **Bachon Wali Misal (Easy Analogy):**
> Farz karein aap ne kisi doosre mulk (AWS) mein apna ek kaam karwana hai. Agar aap direct HTTP API call use karte hain, toh iska matlab hai ke aap ko khud us mulk ki mushkil zubaan seekhni paregi, lifafay par sahi stamp lagani paregi, post office ke chakkar kaatne parenge, aur agar khat raastay mein kho jaye toh khud hi dhoondna paregi.
> Lekin **SDK** aap ka ek **Akalmand Personal Assistant** hai. Aap sirf apni zubaan (Python ya JavaScript) mein is assistant ko batate hain ke *"Mujhe ek virtual machine chahiye"*. Yeh assistant khud hi:
> 1. Aap ke signature check karta hai (Authentication).
> 2. Khat ko sahi dhabay mein pack karta hai (Data Serialization into JSON/XML).
> 3. Internet ke zariye AWS tak safely lekar jata hai (HTTPS Communication).
> 4. Agar rasta block ho ya koi temporary error aaye, toh khud hi dobara koshish karta hai (Retry on error).
> 
> 

Is book mein zyadatar examples **JavaScript** mein likhi gayi hain aur unhein chalane ke liye **Node.js** runtime environment ka istemal kiya gaya hai.

---

## Installing and getting started with Node.js

**Node.js** ek aisa platform hai jo JavaScript code ko aap ke computer par direct chalane ki taqat deta hai. Is ki madad se hum aasaani se network applications bana sakte hain.

### Node.js Ko Install Karne Ka Tarika:

1. Sab se pehle `[https://nodejs.org](https://nodejs.org)` website par jayein aur apne Operating System ke mutabaq setup download kar ke install kar lein.
2. *Note:* Book ke examples Node.js version 14 par test kiye gaye hain. Lekin aaj ke daur (2026) ke hisab se, aap Node.js ka koi bhi latest LTS version (jaise v20+ ya v22+) aasaani se install kar sakte hain, kyunke hamara modern code un par bilkul fit chalega.
3. Installation check karne ke liye apne terminal mein yeh command likhein:
```bash
node --version
```


Aap ko terminal par version ka naam (jaise `v20.x.x`) dikhayi dega.
4. Node.js ke sath ek bohot hi ahem tool install hota hai jise **npm (Node Package Manager)** kehte hain. Yeh bilkul ek play store ki tarah hai jahan se hum bani-banayi code libraries (dependencies) download karte hain. Isay check karne ka tarika yeh hai:
```bash
npm --version
```



### JS Code Ko Kaise Chalana Hai?

Agar aap ke paas koi file hai jiska naam `script.js` hai, toh usay chalane ke liye terminal mein simply yeh command likhein:

```bash
node script.js
```

> **Ek Zaroori Farq (Concept):**
> **JavaScript** ek programming language (zubaan) hai, jabke **Node.js** us zubaan ko aap ke system par chalane ka engine/environment (runtime) hai. Hum is book mein Node.js is liye use kar rahe hain kyunke isay install karna asaan hai, kisi bhari IDE (software) ki zaroorat nahi parti, aur iska syntax zyadatar programmers ko pehle se pata hota hai.

Hum ab ek application banayenge jiska naam hai **Node Control Center for AWS** (ya short mein **nodecc**) jo AWS ke virtual machines ko control karegi.

---

## Controlling virtual machines with SDK: nodecc

**nodecc** ek aisi application hai jo terminal ke andar ek simple text-based screen (UI) banati hai, jis se hum ek se zyada temporary virtual machines ko aasaani se manage kar sakte hain.

### nodecc Ke Khaas Features:

* Yeh ek hi waqt mein **multiple virtual machines** ko handle kar sakti hai.
* JavaScript mein likhi hone ki wajah se yeh kisi bhi computer (Windows, Mac, Linux) par chal sakti hai.
* Iska interface graphical nahi hai, balkay terminal ke andar chalne wala text interface hai.

### Figure 4.7 Ka Breakdown (Start Screen):

<div align="center">
  <img src="./images/07.png" width="600"/>
</div>

Jab aap `nodecc` ko start karte hain, toh terminal par aap ko **Figure 4.7** ke mutabaq ek screen dikhayi deti hai:

* **Left Side (Actions Panel):** Yahan aap ko teen options milte hain:
1. `list virtual machines` (VMs dekhne ke liye)
2. `create virtual machine` (Nayi VM banane ke liye)
3. `terminate virtual machine` (VM ko delete karne ke liye)


* **Right Side (Console/Info Panel):** Yahan par instructions likhi hoti hain ke aap arrow keys se upar-niche scroll kar sakte hain, selection ke liye **Enter** daba sakte hain, aur application se bahar nikalne ke liye **ESC** ya **q** press kar sakte hain.

### nodecc Ko Chalane Ka Tarika:

1. Is application ko chalane ke liye aap ke paas ek IAM role hona chahiye jiska naam **`ec2-ssm-core`** hai (jo hum ne banaya tha).
2. Apne terminal par book ke code folder mein is path par jayein: `/chapter04/nodecc/`.
3. Saari zaroori dependency libraries install karne ke liye yeh command chalayein:
```bash
npm install
```


4. Application ko start karne ke liye run karein:
```bash
node index.js
```


*Yeh program wahi settings use karega jo aap ne `aws configure` ke zariye `mycli` user ke liye set ki thi.*

---

## How nodecc creates a virtual machine

Virtual machine banane ke liye pehla step yeh hota hai ke hum ek **AMI (Amazon Machine Image)** select karein, jo ke hamare server ka operating system template hota hai.

### Figure 4.8 Ka Breakdown (AMI Selection):

<div align="center">
  <img src="./images/08.png" width="600"/>
</div>

Jab aap `create virtual machine` select karte hain, toh **Figure 4.8** ke mutabaq right side par Amazon Linux AMIs ki ek list khul jati hai. Aap arrow keys ka use kar ke apni pasand ki image select karte hain.

Aayein dekhte hain ke background mein SDK kis tarah is list ko AWS se nikal kar lata hai.

### Listing 4.3 Fetching the list of available AMIs: /lib/listAMIs.js

#### Modern JavaScript (AWS SDK v3 - ES Modules)

```javascript
import { EC2Client, DescribeImagesCommand } from "@aws-sdk/client-ec2";

// AWS SDK v3 mein client ko alag se initialize kiya jata hai
const ec2 = new EC2Client({ region: 'us-east-1' });

export default async function listAMIs() {
  const params = {
    Filters: [{
      Name: 'name',
      Values: ['amzn2-ami-hvm-2.0.202*-x86_64-gp2']
    }]
  };

  try {
    // Command object banaya aur client ko send kiya
    const command = new DescribeImagesCommand(params);
    const data = await ec2.send(command);

    // Images ka data map kar ke nikalna
    const amiIds = data.Images.map(image => image.ImageId);
    const descriptions = data.Images.map(image => image.Description);

    return { amiIds, descriptions };
  } catch (err) {
    throw err;
  }
}
```

#### Modern Python 3.11+ (Boto3 equivalent)

```python
import boto3
from typing import Dict, List, Tuple, Any

# EC2 Client ko initialize karna
ec2_client = boto3.client('ec2', region_name='us-east-1')

def list_amis() -> Tuple[List[str], List[str]]:
    """
    AWS se available Amazon Linux 2 AMIs ki list nikalta hai.
    """
    try:
        # describe_images API call filter ke sath
        response = ec2_client.describe_images(
            Filters=[
                {
                    'Name': 'name',
                    'Values': ['amzn2-ami-hvm-2.0.202*-x86_64-gp2']
                }
            ]
        )
        
        # Responses se ImageId aur Description nikalna
        ami_ids: List[str] = [img['ImageId'] for img in response.get('Images', [])]
        descriptions: List[str] = [img.get('Description', 'No Description') for img in response.get('Images', [])]
        
        return ami_ids, descriptions
        
    except Exception as e:
        print(f"Error fetching AMIs: {e}")
        raise e

```

#### Code Ki Gehrai Se Tafseel (Dono Languages Ke Liye)

* **Initialization:** JavaScript mein hum `@aws-sdk/client-ec2` se client banate hain, jabke Python mein hum `boto3.client('ec2')` use karte hain.
* **Filtering (Talash):** Hum AWS ko batate hain ke humein saari images nahi chahiye. Hum ne filter lagaya ke jin images ka naam `amzn2-ami-hvm-2.0.202*-x86_64-gp2` se match karta ho, sirf wahi dikhao. Yeh wild card `*` dynamic dates ko search karne mein madad karta hai.
* **Data Extraction:** Dono codes response mein se loop chala kar (`map` in JS, `list comprehension` in Python) har image ki ahem details (`ImageId` aur `Description`) ko alag alag arrays mein save kar ke return kar dete hain.

---

### Figure 4.9 Ka Breakdown (Subnet Selection):

<div align="center">
  <img src="./images/09.png" width="600"/>
</div>

AMI choose karne ke baad, hamare samne **Figure 4.9** ke mutabaq subnets ki list aati hai. Subnet asal mein hamare network ka ek hissa hota hai jahan hamari machine rakhi jayegi. Aap in mein se kisi bhi ek subnet ko select karte hain.

Aayein dekhte hain ke subnet ki list nikalne ka code kaise chal raha hai.

### Listing 4.4 Fetching the list of available default subnets: /lib/listSubnets.js

#### Modern JavaScript (AWS SDK v3 - ES Modules)

```javascript
import { EC2Client, DescribeVpcsCommand, DescribeSubnetsCommand } from "@aws-sdk/client-ec2";
const ec2 = new EC2Client({ region: 'us-east-1' });

export default async function listSubnets() {
  try {
    // Step 1: Default VPC (main network) dhoondna
    const vpcCommand = new DescribeVpcsCommand({
      Filters: [{ Name: 'isDefault', Values: ['true'] }]
    });
    const vpcData = await ec2.send(vpcCommand);
    
    if (!vpcData.Vpcs || vpcData.Vpcs.length === 0) {
      throw new Error("No default VPC found!");
    }
    const vpcId = vpcData.Vpcs[0].VpcId;

    // Step 2: Us VPC ke subnets dhoondna
    const subnetCommand = new DescribeSubnetsCommand({
      Filters: [{ Name: 'vpc-id', Values: [vpcId] }]
    });
    const subnetData = await ec2.send(subnetCommand);

    // Subnet IDs ki list return karna
    return subnetData.Subnets.map(subnet => subnet.SubnetId);
  } catch (err) {
    throw err;
  }
}
```

#### Modern Python 3.11+ (Boto3 equivalent)

```python
import boto3
from typing import List

ec2_client = boto3.client('ec2', region_name='us-east-1')

def list_subnets() -> List[str]:
    """
    Pehle default VPC dhoondta hai, phir us ke andar majood subnets ki list lata hai.
    """
    try:
        # Step 1: Default VPC ki detail nikalna
        vpc_response = ec2_client.describe_vpcs(
            Filters=[{'Name': 'isDefault', 'Values': ['true']}]
        )
        
        vpcs = vpc_response.get('Vpcs', [])
        if not vpcs:
            raise Exception("No default VPC found!")
            
        vpc_id = vpcs[0]['VpcId']
        
        # Step 2: Is VPC ID ke mutabaq subnets nikalna
        subnet_response = ec2_client.describe_subnets(
            Filters=[{'Name': 'vpc-id', 'Values': [vpc_id]}]
        )
        
        subnet_ids: List[str] = [sub['SubnetId'] for sub in subnet_response.get('Subnets', [])]
        return subnet_ids
        
    except Exception as e:
        print(f"Error fetching subnets: {e}")
        raise e
```

#### Code Ki Gehrai Se Tafseel (Dono Languages Ke Liye)

* **Do-Marhala Maqsad (Two-step process):** Hum seedha subnets nahi dhoond sakte jab tak humein network (VPC) ka pata na ho. Is liye dono codes pehle AWS se poochte hain: *"Mere account ka default VPC (main network) kaunsa hai?"*
* **Linking (Jor):** Jab AWS VPC ID (jaise `vpc-12345`) deta hai, toh hum us ID ko use kar ke doosri call karte hain: *"Is specific VPC ke andar jitne subnets hain, un ki list de do"*.
* **Result:** Aakhir mein dono scripts sirf subnet ki unique IDs (jaise `subnet-abc12`) filter kar ke user ke screen par show karne ke liye bhej deti hain.

---

Subnet select hone ke baad, hamara script virtual machine ko launch karne ka hukum bhejta hai.

### Listing 4.5 Launching an EC2 instance: /lib/createVM.js

#### Modern JavaScript (AWS SDK v3 - ES Modules)

```javascript
import { EC2Client, RunInstancesCommand } from "@aws-sdk/client-ec2";
const ec2 = new EC2Client({ region: 'us-east-1' });

export default async function createVM(amiId, subnetId) {
  const params = {
    IamInstanceProfile: {
      Name: 'ec2-ssm-core' // System Manager se connect hone ke liye zaroori role
    },
    ImageId: amiId,
    MinCount: 1,
    MaxCount: 1,
    InstanceType: 't2.micro',
    SubnetId: subnetId
  };

  try {
    const command = new RunInstancesCommand(params);
    const data = await ec2.send(command);
    
    // Nayi banne wali VM ki InstanceId return karna
    return data.Instances[0].InstanceId;
  } catch (err) {
    throw err;
  }
}
```

#### Modern Python 3.11+ (Boto3 equivalent)

```python
import boto3

ec2_client = boto3.client('ec2', region_name='us-east-1')

def create_vm(ami_id: str, subnet_id: str) -> str:
    """
    Diyay gaye AMI aur Subnet ID ko use kar ke ek t2.micro instance launch karta hai.
    """
    try:
        response = ec2_client.run_instances(
            IamInstanceProfile={
                'Name': 'ec2-ssm-core'
            },
            ImageId=ami_id,
            MinCount=1,
            MaxCount=1,
            InstanceType='t2.micro',
            SubnetId=subnet_id
        )
        
        # Nayi instance ki ID return karna
        instance_id: str = response['Instances'][0]['InstanceId']
        return instance_id
        
    except Exception as e:
        print(f"Error creating virtual machine: {e}")
        raise e
```

#### Code Ki Gehrai Se Tafseel (Dono Languages Ke Liye)

* **`run_instances` / `RunInstancesCommand`:** Yeh asli core command hai jo AWS par naya server launch karti hai.
* **MinCount & MaxCount:** Dono values `1` hain, iska matlab hai humein sirf ek hi server launch karna hai.
* **IamInstanceProfile:** Hum ne yahan `ec2-ssm-core` role pass kiya hai taake security safely configure ho sake aur hum browser console ke bina terminal se direct connection kar sakein.
* **Return:** Jaise hi request accept hoti hai, AWS humein ek confirmation data bhejta hai jis mein se hum naye server ki unique `InstanceId` nikal kar save kar lete hain.

---

## How nodecc lists virtual machines and shows virtual machine details

Jaise hi virtual machine ban jati hai, humein usay manage karne ke liye chalne wale servers ki list dekhni hoti hai taake hum connection details dekh sakein.

### Figure 4.10 Ka Breakdown (Listing VMs):

<div align="center">
  <img src="./images/10.png" width="600"/>
</div>

Jab aap `list virtual machines` select karte hain, toh **Figure 4.10** ke mutabaq right side par chalne wali virtual machines ki IDs (jaise `i-04e525075fa80fcfd`) list ho jati hain.

### Listing 4.6 Listing EC2 instances: /lib/listVMs.js

#### Modern JavaScript (AWS SDK v3 - ES Modules)

```javascript
import { EC2Client, DescribeInstancesCommand } from "@aws-sdk/client-ec2";
const ec2 = new EC2Client({ region: 'us-east-1' });

export default async function listVMs() {
  const params = {
    Filters: [{
      Name: 'instance-state-name',
      Values: ['pending', 'running'] // Sirf chalne wali ya start hone wali machines dikhao
    }],
    MaxResults: 10 // Ek waqt mein zyada se zyada 10 results
  };

  try {
    const command = new DescribeInstancesCommand(params);
    const data = await ec2.send(command);

    // Reservations ke andar se saari Instance IDs ko flatten (ek hi list mein) karna
    const instanceIds = data.Reservations
      .flatMap(r => r.Instances.map(i => i.InstanceId));

    return instanceIds;
  } catch (err) {
    throw err;
  }
}
```

#### Modern Python 3.11+ (Boto3 equivalent)

```python
import boto3
from typing import List

ec2_client = boto3.client('ec2', region_name='us-east-1')

def list_vms() -> List[str]:
    """
    Pending aur Running states mein chalne wali instances ki IDs filter kar ke lata hai.
    """
    try:
        response = ec2_client.describe_instances(
            Filters=[
                {
                    'Name': 'instance-state-name',
                    'Values': ['pending', 'running']
                }
            ],
            MaxResults=10
        )
        
        instance_ids: List[str] = []
        # DescribeInstances ka response nested hota hai: Reservations -> Instances -> Details
        for reservation in response.get('Reservations', []):
            for instance in reservation.get('Instances', []):
                instance_ids.append(instance['InstanceId'])
                
        return instance_ids
        
    except Exception as e:
        print(f"Error listing VMs: {e}")
        raise e
```

#### Code Ki Gehrai Se Tafseel (Dono Languages Ke Liye)

* **State Filtering:** Hum ne AWS ko bola ke jo machines band ho chuki hain (terminated), unhein dikhane ki zaroorat nahi hai. Sirf wohi dikhao jo `pending` (chalne ki tayyari mein hain) ya `running` (bilkul theek chal rahi hain) hain.
* **MaxResults:** Hum ne limit `10` set ki hai taake terminal screen par dher saara data ek sath na aa jaye.
* **Data Structure Challenge (Flattening):** AWS EC2 humein data direct list mein nahi deta. Woh use `Reservations` ke andar nested structures mein deta hai.
* JS mein hum ne functional programming ka feature `flatMap` use kiya jo arrays ke andar se arrays nikal kar unhein ek single plain array mein badal deta hai.
* Python mein hum ne double nested loops (`for` loops) use kiye taake har `reservation` ke andar se `Instances` ki plain list nikaali ja sake.



---

### Figure 4.11 Ka Breakdown (Showing VM Details):

<div align="center">
  <img src="./images/11.png" width="600"/>
</div>

Jab aap chalne wali kisi machine ID par click karte hain, toh **Figure 4.11** ke mutabaq us machine ka poora kacha-chitha (details) khul jata hai:

* **InstanceId:** Machine ka unique naam.
* **InstanceType:** Size (t2.micro).
* **LaunchTime:** Machine kab start hui thi. Is se humein yeh pata chalta hai ke kahin koi machine bohot purani aur fuzool toh nahi chal rahi, taake hum usay band kar ke paise bacha sakein.
* **PublicDnsName:** Server ka internet address jis se hum connection bana sakte hain.

---

## How nodecc terminates a virtual machine

Jab hamara temporary testing ka kaam mukammal ho jata hai, toh bill se bachne ke liye virtual machine ko hamesha ke liye delete (terminate) karna zaroori hota hai.

### Listing 4.7 Terminating an EC2 instance: /lib/terminateVM.js

#### Modern JavaScript (AWS SDK v3 - ES Modules)

```javascript
import { EC2Client, TerminateInstancesCommand } from "@aws-sdk/client-ec2";
const ec2 = new EC2Client({ region: 'us-east-1' });

export default async function terminateVM(instanceId) {
  const params = {
    InstanceIds: [instanceId] // Is array mein hum multiple IDs bhi de sakte hain
  };

  try {
    const command = new TerminateInstancesCommand(params);
    await ec2.send(command);
    return true;
  } catch (err) {
    throw err;
  }
}
```

#### Modern Python 3.11+ (Boto3 equivalent)

```python
import boto3

ec2_client = boto3.client('ec2', region_name='us-east-1')

def terminate_vm(instance_id: str) -> bool:
    """
    Diyay gaye Instance ID ko terminate/delete karne ki request AWS ko bhejta hai.
    """
    try:
        ec2_client.terminate_instances(
            InstanceIds=[instance_id]
        )
        return True
    except Exception as e:
        print(f"Error terminating VM {instance_id}: {e}")
        raise e
```

#### Code Ki Gehrai Se Tafseel (Dono Languages Ke Liye)

* **`terminate_instances` / `TerminateInstancesCommand`:** Yeh command server ko stop karti hai aur AWS ke physical server se us virtual machine ke saare data ko hamesha ke liye mita (delete) deti hai.
* **Array Parameter:** Dono languages mein yeh call ek array input accept karti hai `[instanceId]`, jiska matlab hai ke agar hum chahein toh ek hi click mein 5-10 virtual machines ko bhi ek sath terminate kar sakte hain.

---

## Cleaning up

> **Zaroori Baat:** Agay barhne se pehle `nodecc` ke menu mein ja kar apni banayi hui saari virtual machines ko **terminate** karna mat bhooliyega! Agar koi bhi machine active reh gayi toh AWS ka bill chalu rahega.

---

### SDK Ke Major Trade-offs Aur Mushkilat (The Hard Parts of SDK):

Agarchay SDK hamare liye kaafi kaam aasaan karta hai, lekin is ke sath kaam karte hue kuch mushkilat pesh aati hain jin par ghaur karna zaroori hai:

1. **Imperative Approach (Hukum-dar-hukum tarika):**
SDK ya Node.js mein humein computer ko ek-ek step batana padta hai. Maslan: "Pehle default VPC dhoondo, phir uske subnets nikalo, phir un subnets mein machine run karo, phir machine ke start hone ka wait karo". Agar beech mein ek bhi step ghalti se miss ho jaye ya crash kar jaye, toh poora system kharab ho jata hai.
2. **Dependencies Ko Manage Karna:**
Humein khud dhyan rakhna padta hai ke jab tak virtual machine fully boot ho kar active (running state) na ho jaye, hum Session Manager se connect karne ka link show nahi kar sakte. Is synchronization aur wait ko khud code mein handle karna bohot complex kaam hai.
3. **Updates Ka Mushkil Hona (No Change Management):**
Agar hamare paas koi virtual machine chal rahi hai aur hum uski settings badalna chahein (jaise us ka size `t2.micro` se barha kar `t2.medium` karna), toh SDK ke zariye chalte hue server par direct yeh tabdeeli karna aur automatic manage karna intehai mushkil hai. Hum ne naya server create toh kar liya, par update ka rasta aasaan nahi hai.

Isi liye, ab hum is **Imperative World** (jahan hum har step khud likhte hain) ko chhor kar **Declarative World** (jahan hum sirf aakhri state likhte hain aur AWS khud sab kuch manage karta hai) ki taraf barhenge, jo ke **AWS CloudFormation** aur modern Infrastructure as Code (IaC) ka bunyadi concept hai!

---


## Infrastructure as Code

**Infrastructure as Code (IaC)** ka matlab hai ek high-level programming language ya code file (jaise JSON ya YAML) ka istemal kar ke apne poore computer network aur servers ko control aur setup karna.

* **Infrastructure Kya Hota Hai?** Infrastructure ka matlab hai AWS ka koi bhi resource, jaise network ki settings (network topology), load balancer (traffic manage karne wala), DNS entry (website ka naam), ya storage buckets.
* **Software Development Se Seekh:** Software development mein hamare paas kaam ko behtar banane ke liye behtareen tools hote hain, jaise automated tests (jo code check karte hain), code repositories (jahan code save hota hai jaise GitHub), aur build servers.
* **IaC Ka Faida:** Agar hamara infrastructure bhi code ki shakal mein likha hoga, toh hum software development wale saare ache tools (tests, version control, automation) apne infrastructure par bhi apply kar sakenge, jis se galti ki gunjaish bilkul khatam ho jayegi aur system ki quality bohot barh jayegi.

> **WARNING (Ek Zaroori Farq):** **Infrastructure as Code (IaC)** aur **Infrastructure as a Service (IaaS)** ko aapas mein mix mat kijiyega!
> * **IaaS** ka matlab hai internet par virtual machines, storage, aur networks ko rent (kiraye) par lena jahan aap jitna use karte hain sirf us ka pay karte hain (pay-per-use model).
> * **IaC** us kiraye par liye gaye infrastructure ko code ke zariye automatic chalane aur setup karne ka tarika hai.

---

## Inventing an infrastructure language: JIML

Infrastructure as Code ke gehre concepts ko aasaani se samajhne ke liye, hum khud se ek farzi (imaginary) language ijad karte hain jiska naam hum rakhte hain **JSON Infrastructure Markup Language (JIML)**.

### Figure 4.12 Ka Breakdown (Blueprint Se Asli Setup Tak):

<div align="center">
  <img src="./images/12.png" width="600"/>
</div>

Agar aap **Figure 4.12** ko ghaur se dekhein, toh yeh humein dikhata hai ke hamara IaC tool kis tarah kaam karega:

* **Left Side (The Blueprint):** Yahan hamari likhi hui code file hai (JIML code) jo ke ek text file hai. Is mein hum ne likha hai ke humein kya kya chahiye.
* **The Translator (IaC Tool):** Darmiyan mein hamara IaC tool baitha hai jo is file ko parhta hai aur usay real-world running running setup mein convert karta hai.
* **Right Side (The Running Infrastructure):** Tool ki madad se hamara poora physical ya virtual setup AWS par khara ho jata hai, jis mein niche likhe tamam components aapas mein miley hote hain:
* **DNS:** Website ka domains handle karne ke liye.
* **CDN (Content Delivery Network):** Website ko users ke liye tez banane ke liye.
* **Load Balancer (LB):** Aane wale users ke load ko divide karne ke liye.
* **Static Files Storage (Bucket):** Tasveerein aur static files save karne ke liye.
* **Virtual Machines (VMs):** Hamare application servers.
* **Database (Amazon RDS):** Jahan data save hoga.



JIML ko likhne ke liye hum **JSON** ka syntax use karenge taake brackets ya spelling ki galti na ho. Is language mein jab bhi hum kisi resource ke aage dollar sign **`$`** lagayenge, toh iska matlab hoga ke hum us resource ki ID ka reference (ishara) de rahe hain.

### Listing 4.8 Infrastructure

Niche hum ne exact wahi JSON template likha hai jo hamare poore system ka naksha (blueprint) hai:

```json
{
  "region": "us-east-1",
  "resources": [{
    "type": "loadbalancer", // Load balancer ki zaroorat hai.
    "id": "LB",
    "config": {
      "virtualmachines": 2, // Do VMs ki zaroorat hai.
      "virtualmachine": {
        "cpu": 2,
        "ram": 4,
        "os": "ubuntu" // VMs Ubuntu Linux hain (4 GB memory, 2 cores ke sath).
      }
    },
    "waitFor": "$DB" // LB tabhi ban sakta hai agar database ready ho.
  },
  {
    "type": "cdn", // CDN ka istemaal hota hai jo LB ki requests ko cache karta hai ya static assets (images, CSS files, ...) bucket se fetch karta hai.
    "id": "CDN",
    "config": {
      "defaultSource": "$LB",
      "sources": [{
        "path": "/static/*",
        "source": "$BUCKET"
      }]
    }
  },
  {
    "type": "database", // Data MySQL database mein store hota hai.
    "id": "DB",
    "config": {
      "password": "****",
      "engine": "MySQL"
    }
  },
  {
    "type": "dns", // DNS entry CDN ki taraf point karti hai.
    "config": {
      "from": "www.mydomain.com",
      "to": "$CDN"
    }
  },
  {
    "type": "bucket", // Bucket ka istemaal static assets (images, CSS files, ...) ko store karne ke liye hota hai.
    "id": "BUCKET"
  }]}
```

#### JSON Ki Step-by-Step Aasaan Detail:

* `"region": "us-east-1"`: Yeh batata hai ke hamara poora system AWS ke America (N. Virginia) wale data center mein banega.
* `"resources"`: Is array list ke andar hum ne apne saare khilaune (components) rakh diye hain jo humein chahiye:
1. **Load Balancer (`LB`):** Is ki configuration mein likha hai ke is ke piche 2 virtual machines chalanian hain jo Ubuntu operating system par chalengi, jin mein 2 CPU aur 4 GB RAM hogi. Sab se zaroori baat yahan `"waitFor": "$DB"` likha hai, jiska matlab hai ke load balancer tab tak nahi ban sakta jab tak hamara database (`DB`) ban kar tayyar na ho jaye.
2. **CDN (`CDN`):** Yeh content delivery network hai. Is ka main source `$LB` (Load Balancer) hai, aur static files (`/static/*`) uthane ke liye yeh `$BUCKET` ka use karega.
3. **Database (`DB`):** Yeh database MySQL engine par chalega aur iska password set kiya gaya hai.
4. **DNS:** Yeh internet par hamari website `[www.mydomain.com](https://www.mydomain.com)` ko `$CDN` ke sath jor dega.
5. **Bucket (`BUCKET`):** Yeh files store karne ke liye ek khali dabba (storage bucket) banayega.



---

## How can we turn this JSON into AWS API calls?

Hum is raw JSON text file ko AWS ke chalte hue system mein kaise badlein? Hum direct servers ko order nahi bhej sakte, is ke liye hamare JIML tool ko **chaar ahem steps** karne parenge:

1. **Parse the JSON input:** Sab se pehle tool is JSON file ko parh kar computer ki memory mein load karega taake computer har ek line ka matlab samajh sake.
2. **Create a dependency graph:** Tool yeh dekhega ke kaun sa component kis par depend karta hai (kis ke bina kaam nahi chal sakta) aur un ko aapas mein lino ke zariye jor kar ek naksha (graph) banayega.
3. **Traverse the dependency graph:** Tool is nakshe ko niche se upar (leaf nodes se root tak) parhega aur kaam karne ki ek sahi line-by-line sequence (linear flow) banayega.
4. **Translate into AWS API calls:** Aakhir mein, tool un commands ko real AWS API calls mein badal kar internet ke zariye AWS ko bhej dega.

> **Bachon Wali Misal (The House Analogy):**
> Farz karein aap ne ghar banana hai. Aap ghar ki chhat (DNS) tab tak nahi dal sakte jab tak deewarein (Load Balancer/CDN) na khari hon, aur deewarein tab tak nahi ban sakti jab tak bundeadi khambay aur zameen ki tayyari (Database/VMs) na ho jaye.
> IaC tool bilkul isi tarah dhyan rakhta hai ke pehle foundation bane, phir deewarein, aur aakhir mein chhat!

### Figure 4.13 Ka Breakdown (Dependency Graph):

<div align="center">
  <img src="./images/13.png" width="600"/>
</div>

Agar aap **Figure 4.13** ko dekhein, toh left side par hamara JIML code hai aur right side par tool ka banaya hua **Dependency Graph** hai. Tool ne automatic sab ka number aur order set kar diya hai:

* **Nodes at the bottom (No Dependencies):** Sab se niche **DB (1)**, **VM (2)**, aur **Bucket (3)** hain. In ke niche koi aur component nahi hai, is liye yeh sab se pehle bina kisi ka wait kiye banaye ja sakte hain.
* **Load Balancer (4):** Yeh beech mein khara hai kyunke yeh tab banega jab niche wale **DB** aur **VMs** ban kar ready ho jayenge.
* **CDN (5):** Yeh tab banega jab usay peeche se **LB (4)** aur **Bucket (3)** milenge.
* **DNS (6):** Yeh sab se top par khara hai kyunke isay chalne ke liye **CDN (5)** ka active hona zaroori hai.

Is nakshe ko niche se upar aur left se right parhte hue, JIML tool commands ki ek seedhi list tayyar karega.

---

### Listing 4.9 Linear flow of commands in pseudo language

Niche di gayi list hamare tool ki banayi hui seedhi instructions (commands) hain jo bilkul sahi order mein chalengi:

```text
$DB = database create {"password": "****", "engine": "MySQL"} // Database create karta hai.
$VM1 = virtualmachine create {"cpu": 2, "ram": 4, "os": "ubuntu"} // Virtual machine create karta hai.
$VM2 = virtualmachine create {"cpu": 2, "ram": 4, "os": "ubuntu"} // Virtual machine create karta hai.
$BUCKET = bucket create {} // Bucket create karta hai.

await [$DB, $VM1, $VM2] // Dependencies ka wait karta hai.
$LB = loadbalancer create {"virtualmachines": [$VM1, $VM2]} // Load balancer create karta hai.

await [$LB, $BUCKET] // Dependencies ka wait karta hai.
$CDN = cdn create {...} // CDN create karta hai.

await $CDN
$DNS = dns create {...} // DNS entry create karta hai.

await $DNS
```

#### Pseudo Language Ki Step-by-Step Detail:

* `$DB = database create ...` aur `$VM1`, `$VM2`, `$BUCKET`: Tool sab se pehle database, dono virtual machines, aur storage bucket banane ki request bhej dega kyunke in ka koi dependency wait nahi hai.
* `await [$DB, $VM1, $VM2]`: Yahan system ruk jayega! Yeh tab tak aage nahi barhega jab tak database aur dono machines poori tarah ban kar active nahi ho jatein.
* `$LB = loadbalancer create ...`: Jaise hi wait khatam hoga, load balancer banaya jayega aur usay bataya jayega ke us ne traffic ko `$VM1` aur `$VM2` par bhejni hai.
* `await [$LB, $BUCKET]`: Ab dobara system wait karega jab tak load balancer aur bucket active nahi ho jate.
* `$CDN = cdn create {...}`: Dono ke ready hote hi CDN network create kiya jayega.
* `await $CDN`: CDN ke fully ready hone ka wait hoga.
* `$DNS = dns create {...}`: Aur aakhir mein DNS entry create ho kar domain map ho jayegi.

### Modern 2026 Perspective (Real-World IaC):

Aaj ke daur (2026) mein hum apna koi farzi tool ya pseudocode interpreter nahi likhte. AWS par is kaam ke liye hum do zaroori aur behtareen options use karte hain:

1. **AWS CloudFormation:** Yeh AWS ki apni service hai jo JSON ya YAML templates ko parhti hai, automatic aisa hi dependency graph (DAG - Directed Acyclic Graph) banati hai, aur sahi order mein resources deploy karti hai.
2. **AWS CDK (Cloud Development Kit) & Terraform:** Aaj kal hum raw JSON likhne ke bajaye modern programming languages (jaise **Python 3.11+** ya **TypeScript**) mein code likhte hain. Hum classes aur objects banate hain, aur background mein yeh tools automatic CloudFormation templates generate kar ke hamara poora system khara kar dete hain.

---


## Using AWS CloudFormation to start a virtual machine

Pehle section mein hum ne khud se ek farzi language (JIML) banayi thi taake hum Infrastructure as Code (IaC) ke bunyadi concept ko samajh sakein. Khushkismati se, AWS ke paas pehle se hi ek bohot behtareen aur powerful tool majood hai jo hamare banaye gaye JIML se kahin zyada behtar kaam karta hai: **AWS CloudFormation**.

> **Ek Choti Sauda (Term) Samajhna:**
> Jis nakshay ya blueprint ka zikr hum pehle kar rahe many, CloudFormation ki duniya mein usay **CloudFormation template** kaha jata hai.

Template asal mein aap ke poore network aur servers (infrastructure) ka ek naksha hota hai jo JSON ya YAML format mein likha jata hai. CloudFormation is template ko khud-ba-khud parh kar samajh leta hai.

### Declarative Approach (Kya Chahiye, Kaise Nahi!)

CloudFormation ek **declarative approach** (bayanati tarika) istemal karta hai.

* **Imperative (Hukmiya) vs Declarative (Bayanati):** Imperative ka matlab hai computer ko ek-ek step batana ke *"Pehle zameen khodo, phir eentein lao, phir deewar banao"* (jaise hum CLI ya SDK scripts mein kar rahe the).
* **Declarative** ka matlab hai computer ko sirf aakhri result batana ke *"Mujhe yahan 2 kamron ka ghar chahiye"*. Aap ko CloudFormation ko yeh batane ki zaroorat nahi parti ke kaun sa kaam pehle karna hai aur kaun sa baad mein. CloudFormation khud hi saare steps aur un ka order (sequence) tay karta hai.

---

### CloudFormation Ke Bemisaal Fawaid (Benefits):

Aayein samajhte hain ke CloudFormation ko use karne ke kya kya bade faide hain:

* **It’s a consistent way to describe infrastructure (Ek jaisa standard tarika):** Agar har banda apni marzi ke custom scripts likhne lag jaye, toh har koi ek hi maslay ko alag tareeqay se hal karega. Is se naye developers aur operators ke liye code ko samajhna ek azab ban jata hai. CloudFormation sab ke liye ek hi saaf-suthri aur standard zubaan deta hai.
* **It handles dependencies (Dependencies ko khud sambhalta hai):** Kya aap ne kabhi aisi website live karne ki koshish ki hai jiska database abhi piche se tayyar hi na hua ho? Jab aap hath se scripts likhte hain, toh aap aksar aisi cheezein bhool jate hain aur system crash ho jata hai. CloudFormation dhyan rakhta hai ke pehle database banay, aur phir website ko launch kare taake aap **dependency hell** (mushkilat ki daldal) mein na phansein.
* **It’s reproducible (Dobara asaani se banana):** Kya aap ka testing environment bilkul aap ke production environment ki carbon copy hai? CloudFormation ke zariye aap ek hi template ko chala kar do bilkul ek jaise identical setups bana sakte hain aur dono mein sath hi badlao (changes) bhi apply kar sakte hain.
* **It’s customizable (Tabdeel karne ke qabil):** Aap is mein apni marzi ke custom parameters (jaise website ka naam, size, ya database password) insert kar ke template ko jab chahein customize kar sakte hain.
* **It’s testable (Testing ke qabil):** Agar aap ne template banayi hui hai, toh testing bohot aasaan hai. Naya system khara karein, automatic tests run karein, aur tests pass hone par usay furan delete (shut down) kar dein.
* **It’s updatable (Asaani se update hone wala):** CloudFormation aap ke infrastructure ko smooth tareeqay se update karne ki ijazat deta hai. Yeh khud hi dhoond leta hai ke template mein kya tabdeeli aayi hai aur sirf usi hissay ko update karta hai.
* **It minimizes human failure (Insani galti ka khatma):** Insaan thak jata hai aur raat ke 3 baje galti kar sakta hai, lekin CloudFormation kabhi nahi thakta.
* **It’s the documentation for your infrastructure (Asli dastawez):** Aap ki template khud hi aap ke system ki mukammal book/document hoti hai. Aap isay Git par rakh kar changes ka track record rakh sakte hain.
* **It’s free (Bilkul muft):** AWS CloudFormation ko use karne ka koi extra charge nahi leta. Agar aap ke paas AWS support plan hai, toh aap ko is ke liye free help bhi milti hai.

---

## Anatomy of a CloudFormation template

Ek basic CloudFormation template ke **paanch (5) bade hissay** hote hain:

1. **Format version:** Yeh batata hai ke template kis version ke rules ke mutabaq likhi gayi hai. Is ki valid value filhal `2010-09-09` hai.
2. **Description:** Yeh batata hai ke yeh template kis maqsad ke liye banayi gayi hai.
3. **Parameters:** Yeh user se input values (jaise database password ya machine size) lene ke liye use hote hain.
4. **Resources:** Yeh sab se ahem hissa hai jahan hum batate hain ke humein kaun sa AWS component (jaise virtual machine, load balancer) chahiye.
5. **Outputs:** Yeh kaam khatam hone ke baad humein koi detail return karta hai (jaise bani hui machine ka public IP address).

Aayein is basic dhanchay (structure) ko YAML code mein dekhte hain:

### Listing 4.10 CloudFormation template structure

```yaml
--- # Document shuru ho raha hai
AWSTemplateFormatVersion: '2010-09-09' # Yeh wahid valid version hai
Description: 'CloudFormation template structure' # Yeh template kis baare mein hai?
Parameters:
  # [...] # Parameters ko define karta hai
Resources:
  # [...] # Resources ko define karta hai
Outputs:
  # [...] # Outputs ko define karta hai
```

---

### FORMAT VERSION AND DESCRIPTION

Hamesha apni template mein `AWSTemplateFormatVersion: '2010-09-09'` zaroor likhein. Agar aap nahi likhenge, toh CloudFormation automatic sab se naya version apply kar dega, jis se aage chal kar agar naye rules aaye toh aap ka purana code chalna band ho sakta hai.

Description likhna lazmi nahi hai, lekin isay likhne se aap ko aur aap ke doston ko baad mein samajhne mein bohot aasaani hoti hai ke yeh template kis liye banayi gayi thi.

---

### PARAMETERS

Parameter ka kam az kam ek **Name** aur ek **Type** hona zaroori hai. Saath mein description likhna achhi practice hai.

### Listing 4.11 CloudFormation parameter structure

```yaml
Parameters:
  Demo: # Aap parameter ka naam khud chun sakte hain.
    Type: Number # Yeh parameter ek number ko represent karta hai.
    Description: 'This parameter is for demonstration' # Parameter ki description.
```

Is parameter ka naam `Demo` hai aur is ki type `Number` hai.

Aayein ab parameters ki mukhtalif types ko samajhte hain jo niche table mein di gayi hain:

### Table 4.1 CloudFormation parameter types

| Type | Description (Aasaan Urdu Explanation) |
| --- | --- |
| **String** / **CommaDelimitedList** | Ek plain text (jaise `my-website`) ya comma se separate ki gayi texts ki list (jaise `a,b,c`). |
| **Number** / **List<Number>** | Ek complete number ya float (jaise `10` ya `3.14`), ya un numbers ki list. |
| **AWS::EC2::AvailabilityZone::Name** / **`List<AWS::EC2::AvailabilityZone::Name>`** | AWS ke data center ki exact location ka naam (jaise `us-east-1a`). |
| **AWS::EC2::Image::Id** / **`List<AWS::EC2::Image::Id>`** | Amazon Machine Image (AMI) ki ID (jaise `ami-12345`). |
| **AWS::EC2::Instance::Id** / **`List<AWS::EC2::Instance::Id>`** | Pehle se chalne wali virtual machine ki unique ID. |
| **AWS::EC2::KeyPair::KeyName** | Server login karne ke liye Security Key Pair ka naam. |
| **AWS::EC2::SecurityGroup::Id** / **`List<AWS::EC2::SecurityGroup::Id>`** | Virtual firewall (Security Group) ki ID. |
| **AWS::EC2::Subnet::Id** / **`List<AWS::EC2::Subnet::Id>`** | Network ke specific area (Subnet) ki ID. |
| **AWS::EC2::Volume::Id** / **`List<AWS::EC2::Volume::Id>`** | Hard drive (EBS volume) ki ID. |
| **AWS::EC2::VPC::Id** / **`List<AWS::EC2::VPC::Id>`** | Poore virtual network network (VPC) ki ID. |
| **AWS::Route53::HostedZone::Id** / **`List<AWS::Route53::HostedZone::Id>`** | Domain Name System (DNS) zone ki ID. |

In types ke alawa, hum parameters ko mazeed secure aur bound karne ke liye mukhtalif properties de sakte hain:

### Table 4.2 CloudFormation parameter properties

| Property | Description (Aasaan Urdu Explanation) | Example (Misal) |
| --- | --- | --- |
| **Default** | Agar user koi value na de, toh automatic yeh value select ho jaye. | `Default: 'm5.large'` |
| **NoEcho** | Yeh password ya secrets ko chupane ke liye hai taake screen par stars `***` dikhein. | `NoEcho: true` |
| **AllowedValues** | User sirf inhi di gayi options mein se koi select kar sakta hai. | `AllowedValues: [1, 2, 3]` |
| **AllowedPattern** | Input ko validation rule (Regular Expression) ke mutabaq check karna. | `AllowedPattern: '[a-zA-Z0-9]*'` |
| **MinLength** / **MaxLength** | Batata hai ke text kam az kam kitni lambi ya zyada se zyada kitni badi ho sakti hai. | `MinLength: 12` |
| **MinValue** / **MaxValue** | Numbers ke liye choti aur badi hadd (limit) set karna. | `MaxValue: 10` |
| **ConstraintDescription** | Agar user ghalat value likhe, toh usay screen par kya error message show karna hai. | `ConstraintDescription: 'Maximum value is 10.'` |

Aayein ek comprehensive parameters block ki misal dekhte hain:

```yaml
Parameters:
  KeyName:
    Description: 'Key Pair name'
    Type: 'AWS::EC2::KeyPair::KeyName' # Sirf key-pair names ki ijazat hai.
  NumberOfVirtualMachines:
    Description: 'How many virtual machine do you like?'
    Type: Number
    Default: 1 # Default ek virtual machine hai.
    MinValue: 1
    MaxValue: 5 # Zyada kharche se bachne ke liye upper limit set ki gayi hai.
  WordPressVersion:
    Description: 'Which version of WordPress do you want?'
    Type: String
    AllowedValues: ['4.1.1', '4.0.1'] # Sirf kuch specific versions tak mehdood hai.
```

---

### RESOURCES

Resources block mein hum batate hain ke humein AWS ka kaun sa purza banana hai. Is mein kam az kam ek **Logical Name** (jo hum khud rakhte hain), ek **Type** (jo AWS ne set ki hoti hai), aur uski **Properties** hoti hain.

### Listing 4.12 CloudFormation resources structure

```yaml
Resources:
  VM: # Yeh resource ka naam ya logical ID hai jise aap select kar sakte hain.
    Type: 'AWS::EC2::Instance' # AWS::EC2::Instance type ka resource ek virtual machine ko define karta hai.
    Properties:
      # [...] # Is resource type ke liye zaroori properties.
```

### Listing 4.13 CloudFormation EC2 instance resource

```yaml
Resources:
  VM: # Resource ka naam ya logical ID jo aap chun sakte hain.
    Type: 'AWS::EC2::Instance' # AWS::EC2::Instance type ka resource ek virtual machine ko define karta hai.

Properties:
  ImageId: 'ami-6057e21a' # AMI, vm ka operating system define karti hai.
  InstanceType: 't2.micro' # Instance type vCPUs, memory, aur mazeed cheezein define karta hai.
  SecurityGroupIds: # Iske baare mein aap Chapter 5 mein seekhenge.
    - 'sg-123456'
  SubnetId: 'subnet-123456' # Iske baare mein bhi aap Chapter 5 mein seekhenge.
```

---

### OUTPUTS

Outputs tab use hota hai jab hum chahein ke CloudFormation hamare liye resource banane ke baad koi zaroori detail screen par show kare. Is mein ek **Name** aur ek **Value** zaroor hoti hai.

### Listing 4.14 CloudFormation outputs structure

```yaml
Outputs:
  NameOfOutput: # Output ka naam jo aap choose kar sakte hain.
    Value: '1' # Output ki value.
    Description: 'This output is always 1'
```

Aise static outputs ka koi khas faida nahi hota. Hum aam tor par dynamic values ko dhoondne ke liye **Intrinsic Functions** ka use karte hain:

* `!Ref`: Yeh ek placeholder ki tarah hai jo di gayi resource ki unique ID ya basic value nikal lata hai.
* `!GetAtt`: Yeh kisi resource ki specific property (attribute) ko nikalne ke liye use hota hai (jaise server ka public DNS URL).

### Listing 4.15 CloudFormation outputs example

```yaml
Outputs:
  ID:
    Value: !Ref Server # EC2 instance ko reference karta hai.
    Description: 'ID of the EC2 instance'
  PublicName:
    Value: !GetAtt 'Server.PublicDnsName' # EC2 instance ka PublicDnsName attribute hasil karta hai.
    Description: 'Public name of the EC2 instance'
```

---

## Creating your first template

CloudFormation template banane ke mukhtalif raste hain:

* Aap scratch se khud ek text editor mein likhna shuru karein.
* Kisi public repository se bani-banayi template uthayein aur usay modify kar lein.
* AWS Partner Solutions (`[https://aws.amazon.com/quickstart/](https://aws.amazon.com/quickstart/)`) ya book ke writers ke open-source templates (`[https://github.com/widdix/aws-cf-templates](https://github.com/widdix/aws-cf-templates)`) ka use karein.

### Real-World Example (Developer Team Ki Request)

Farz karein developer team ne aapse ek virtual machine mangi. Kuch mahino baad unhein ehsaas hua ke unhein zyada CPU power chahiye.

* **Agar aap CLI ya SDK use kar rahe hote:** Aap ko complex script likh kar pehle server ko **Stop** karna parta, wait karna parta, phir instance type change karte, phir machine **Start** karte, aur phir ready hone ka wait karte.
* **CloudFormation (Declarative) Approach:** Yeh bohot simple hai! Aap ne sirf template mein ja kar `InstanceType` ki value ko change karna hai aur stack ko update kar dena hai. Baqi ka saara jhanjhat CloudFormation background mein khud hi handle kar lega!

Aayein aisi hi ek complete template dekhte hain:

### Listing 4.16 A template to create an EC2 instance with CloudFormation

```yaml
---
AWSTemplateFormatVersion: '2010-09-09'
Description: 'AWS in Action: chapter 4'
Parameters:
  VPC:
    Type: 'AWS::EC2::VPC::Id' # Iske baare mein aap Chapter 5 mein seekhenge.
  Subnet:
    Type: 'AWS::EC2::Subnet::Id' # Iske baare mein aap Chapter 5 mein seekhenge.
  InstanceType: # User instance type ko define karta hai.
    Description: 'Select one of the possible instance types'
    Type: String
    Default: 't2.micro'
    AllowedValues: ['t2.micro', 't2.small', 't2.medium']
Resources:
  SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup' # Iske baare mein aap Chapter 5 mein seekhenge.
    Properties:
      # [...]
  VM: # Ek minimal EC2 instance ko define karta hai.
    Type: 'AWS::EC2::Instance'
    Properties:
      ImageId: 'ami-061ac2e015473fbe2'
      InstanceType: !Ref InstanceType
      IamInstanceProfile: 'ec2-ssm-core'
      SecurityGroupIds: [!Ref SecurityGroup] # Security group ko reference karke, ek implicit dependency declare ho jati hai.
      SubnetId: !Ref Subnet
Outputs:
  InstanceId: # EC2 instance ki ID return karta hai.
    Value: !Ref VM
    Description: 'Instance id (connect via Session Manager)'
```

#### Code Ki Gehrai Se Tafseel:
### 1. Structure Overview

Yeh template 3 main hisson mein divide hai:

* **Parameters:** Yeh "Input" section hai. Jab aap yeh template run karenge, AWS aapse kuch values maangega (jaise VPC ID, Subnet ID).
* **Resources:** Yeh "Execution" section hai. Yahan define hota hai ke kya banana hai (EC2 instance aur Security Group).
* **Outputs:** Yeh "Result" section hai. Jab infrastructure ban jayega, toh yeh humein kya info return karega.

---

### 2. Line-by-Line Breakdown

#### **Parameters (Inputs)**

* `Parameters`: Yeh section user ko freedom deta hai ke har baar code edit na karna pade.
* `VPC` & `Subnet`: Ye identifiers hain. CloudFormation jab run hoga, yeh aapse puchega ke "Kaunsi VPC aur Subnet mein server banana hai?"
* `InstanceType`: Yahan `AllowedValues` ka use hua hai. Yeh validation hai—aap sirf `t2.micro`, `small`, ya `medium` hi select kar sakte hain. Agar kuch aur daala, toh code error de dega.

#### **Resources (Infrastructure)**

Yeh template ka dil (heart) hai.

* `SecurityGroup`: Yahan firewall rules define honge (jo abhi comment mein hidden hain).
* `VM`: Yeh aapka EC2 instance hai.
* `ImageId`: `ami-061ac2e015473fbe2`. Yeh specific OS image hai jo load hogi.
* `InstanceType`: `!Ref InstanceType`.
* **Note:** `!Ref` ek intrinsic function hai. Yeh upar diye gaye `Parameters` section se us value ko uthata hai jo aapne select ki thi.


* `IamInstanceProfile`: `ec2-ssm-core`. Yeh instance ko permissions deta hai ke wo AWS Systems Manager (SSM) se connect ho sake, taake aap baghair SSH key ke login kar saken.
* `SecurityGroupIds`: `[!Ref SecurityGroup]`. Yahan `!Ref` use karke humne `VM` aur `SecurityGroup` ke darmiyan **dependency** create ki hai. Iska matlab hai: "Pehle Security Group banao, phir VM banao."



#### **Outputs (Results)**

* `InstanceId`: Deployment complete hone ke baad, AWS aapko us server ki ID dikha dega, taake aap usay `Session Manager` ke zariye access kar saken.

---

### 3. Key Concepts jo aapko samajhne chahiye

| Concept | Kya Matlab Hai? |
| --- | --- |
| **!Ref** | Yeh ek pointer hai. Yeh dusre section ki value ko yahan "refer" (copy) kar deta hai. |
| **Declarative** | Aap bata rahe hain "mujhe 1 VM chahiye," na ke "pehle EC2 console kholo, phir region select karo, phir..." |
| **Dependency** | `SecurityGroupIds: [!Ref SecurityGroup]` likhne se AWS samajh jata hai ke VM ko Security Group ki zaroorat hai, isliye wo pehle Group banayega. |

---

* **Where is the template located?** Yeh template GitHub repository par `chapter04/virtualmachine.yaml` ke naam se majood hai. Aap isay download kar sakte hain ya Amazon S3 par majood direct link use kar sakte hain: `[https://s3.amazonaws.com/awsinaction-code3/chapter04/virtualmachine.yaml](https://s3.amazonaws.com/awsinaction-code3/chapter04/virtualmachine.yaml)`.

---

### Object-Oriented analogy: Template vs Stack

* **Template = Class:** Template sirf ek naksha hai jo ek hi dafa likha jata hai.
* **Stack = Object:** Jab hum us nakshe (template) ko chalate hain aur AWS actual resources create karta hai, toh us chalte hue system ko **Stack** kaha jata hai. Ek template se aap 100 mukhtalif stacks bana sakte hain.

Aayein is stack ko AWS console par banane ke steps aur un ki tasveeri details (figures) ko samajhte hain:

1. **AWS Console Khulein:** AWS Management Console par login kar ke **"CloudFormation"** search karein aur service open kar lein. Left side par **"Stacks"** select karein.

<div align="center">
  <img src="./images/14.png" width="600"/>
</div>

2. **Figure 4.14 (Overview of CloudFormation stacks):** Pehli baar kholne par aap ke samne ek khali screen aayegi jahan "No stacks" likha hoga. Aap ne upar right side par **"Create stack"** ke button par click kar ke **"With new resources (Standard)"** select karna hai.

<div align="center">
  <img src="./images/15.png" width="600"/>
</div>

3. **Figure 4.15 (Selecting a template):** Agli screen par aap ne **"Template is ready"** select karna hai. Phir template source mein **"Amazon S3 URL"** select kar ke niche box mein widdix code link paste kar dena hai: `[https://s3.amazonaws.com/awsinaction-code3/chapter04/virtualmachine.yaml](https://s3.amazonaws.com/awsinaction-code3/chapter04/virtualmachine.yaml)` aur **"Next"** par click karna hai.

<div align="center">
  <img src="./images/16.png" width="600"/>
</div>

4. **Figure 4.16 (Defining parameters):** Is screen par:
* *Stack name* mein likhein: `myvm`.
* *InstanceType* mein: `t2.micro` select karein.
* *Subnet* aur *VPC* mein drop-down list se pehle options select kar lein aur **"Next"** daba dein.


5. **Advanced Config (Step 3):** Tags aur advanced options ko filhal skip kar dein, CloudFormation default tags khud hi de dega. **"Next"** click karein.

<div align="center">
  <img src="./images/17.png" width="600"/>
</div>

6. **Figure 4.17 (Summary & Capabilities):** Sab se aakhir mein, screen ke bilkul end par aap ko ek check box milega: **"I acknowledge that AWS CloudFormation might create IAM resources"**. Isay tick mark kar ke **"Create stack"** par click kar dein.

<div align="center">
  <img src="./images/18.png" width="600"/>
</div>

7. **Figure 4.18 (Stack created successfully):** Ab aap ka stack banna shuru ho jayega. Jab tak status **CREATE_IN_PROGRESS** ho, aap ne thoda sabar rakhna hai aur reload button dabate rehna hai. Jaise hi status **CREATE_COMPLETE** ho jaye, toh right side par **"Outputs"** tab par click karein. Wahan aap ko aap ki virtual machine ki dynamic `InstanceId` (jaise `i-0b4b3d327ec204f7b`) show ho jayegi!

---

## Updating infrastructure using CloudFormation

Ab hum check karenge ke kya hum bina kisi lambi script ke is chalte hue server ka size badal sakte hain?

1. CloudFormation console par apne stack (`myvm`) ko select karein aur upar **"Update"** button par click karein.

<div align="center">
  <img src="./images/19.png" width="600"/>
</div>

2. **Figure 4.19 (Updating stack):** Sab se pehle step mein **"Use current template"** select kar ke **"Next"** daba dein.
3. **Step 2 (Parameters Change):** Ab parameters screen par `InstanceType` ko `t2.micro` se badal kar `t2.small` ya `t2.medium` kar dein.
> **WARNING (Pesay Lagenge!):** `t2.small` ya `t2.medium` use karne par charges lagte hain. In ke rates dekhne ke liye aap `[https://aws.amazon.com/ec2/pricing/](https://aws.amazon.com/ec2/pricing/)` par ja sakte hain.


4. Step 3 ko skip karein aur Step 4 (Summary) mein dobara IAM acknowledge box ko tick kar ke **"Update stack"** par click kar dein.

### Background Magic:

Ab aap ka stack **UPDATE_IN_PROGRESS** state mein chala jayega. Agar aap jaldi se EC2 console khol kar dekhein, toh aap ko nazar aayega ke CloudFormation ne:

* Pehle chalte hue server ko safely **Stop** kiya.
* Uski setting badal kar `t2.small` ya `t2.medium` ki.
* Server ko dobara **Start** kar diya.

Yeh sab kuch bina kisi galti ke automatic ho gaya aur thodi der mein status **UPDATE_COMPLETE** ho jayega!

---

## Alternatives to CloudFormation

Agar aap ko YAML ya JSON file hath se likhna pasand nahi hai, toh modern industry (2026 ke daur) mein do behtareen alternatives majood hain:

* **AWS Cloud Development Kit (CDK):** Yeh aap ko apni pasand ki programming language (jaise Python 3.11+, TypeScript, Java) mein code likh kar infrastructure define karne ki taqat deta hai. Back-end par CDK khud hi is programming code ko CloudFormation templates mein translate karta hai.
* **Terraform:** Yeh ek intehai mashhoor third-party tool hai jo na sirf AWS balkay baqi saare clouds (Azure, Google Cloud) ko bhi single configuration se control karne ki sahulat deta hai.

---

## Cleaning up

> **ZAROORI NOTE:** Kaam khatam hone ke baad apne stack ko select karein aur **"Delete"** button par click kar dein. CloudFormation automatic virtual machine aur us ke sath banne wale saare components ko mita dega taake aap ke account par fuzool billing na ho!

---

## Summary

* **Automation Tools:** AWS par automation ke liye hamare paas teen bare raste hain: CLI, SDKs, aur CloudFormation.
* **Infrastructure as Code (IaC):** Yeh hamare poore system (virtual machines, storage, networks) ko programming code ke zariye generate aur modify karne ka modern approach hai.
* **CLI Automation:** Hum Bash (Linux/Mac) ya PowerShell (Windows) scripts likh kar complex routines ko automate kar sakte hain.
* **SDK Applications:** Hum mukhtalif programming languages ke SDKs use kar ke custom platforms (jaise `nodecc`) bana sakte hain.
* **Declarative CloudFormation:** CloudFormation hum se sirf system ki aakhri state (End State) mangta hai aur use kaise banana hai woh khud decide karta hai. Is ke teen basic blocks hote hain: **Parameters**, **Resources**, aur **Outputs**.

---