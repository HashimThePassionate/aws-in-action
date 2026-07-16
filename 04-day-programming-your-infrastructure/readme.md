# Programming your infrastructure: The command line, SDKs, and CloudFormation

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

Ab hum seekhte hain ke CLI ko practical use kaise karna hai. Farz karein aap check karna chahte hain ke aap ke account mein is waqt kaun se `t2.micro` size ke virtual computers (EC2 instances) chal rahe hain.

Aap apne terminal mein yeh command run karenge:

```bash
$ aws ec2 describe-instances --filters "Name=instance-type,Values=t2.micro"
```

**Output:**

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