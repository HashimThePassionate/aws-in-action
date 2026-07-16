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
