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