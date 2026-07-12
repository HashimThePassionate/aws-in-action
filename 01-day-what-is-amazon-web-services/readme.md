# What is Amazon Web Services?

Aaj kal IT ki dunya mein har choti barri cheez ke sath **cloud computing** ya sirf **cloud** ka naam jor diya jata hai. Yeh "buzzwords" (mashhoor alfaz) marketing aur sales ke liye to bohot achhe hain, lekin jab koi inko seekhnay ya parhnay baithta hai, to yeh parshani paida karte hain. Is liye, cheezon ko bilkul saaf aur asaan samajhnay ke liye, hum pehle kuch basic alfaz ki defination se shuru karte hain.

**Cloud computing** ya **the cloud**, asal mein IT resources (jaise computers, storage, networks) ko haasil karne aur unhein istemaal karne ka ek asaan tareeqa hai.

* **Abstraction ki Layers:** Cloud ke andar jo IT resources hotay hain, woh aap ko seedhay nazar nahi aatay. Un ke darmiyan "abstraction" (parda ya asaan interface) ki layers hoti hain. Is ko asaan zaban mein yun samjhein ke jaise aap gaari chalate hain, to aap ko sirf steering aur pedals nazar aatay hain (high abstraction), aap ko andar ke complex engine ki fikar nahi karni parti. Cloud bhi aisa hi hai; yeh aap ko aik Bani-banai Virtual Machine (VM) se lekar aik mukammal software (Software as a Service - SaaS) tak kuch bhi de sakta hai, jahan pichli saari mushkil coding aur hardware aapse chupa hua hota hai.
* **On-demand aur Unlimited:** Yeh resources jab aap ko chahiye hon, bparri se barri tadaad mein foran mil jaate hain.
* **Pay-for-what-you-use:** Is ka sab se barra faida yeh hai ke jitna aap istemaal karenge, sirf utne hi paise dein gaey—bilkul bijli ya paani ke bill ki tarah.

NIST (National Institute of Standards and Technology) ki official defination ke mutabaq:

> Cloud computing aik aisa model hai jo hamein internet ke zariye har jagah se, bohot asani se, aur jab dil chahe computing resources (jaise networks, servers, storage, applications, aur services) ka aik shared pool (aik sath jama kiye huay resources) istemaal karne ki ijazat deta hai. In resources ko bohot jaldi se shuru (provision) aur khatam (release) kiya ja sakta hai, aur is mein management ki bohot kam mehnat ya service provider se baar baar baat karne ki zaroorat nahi parti.

NIST ne cloud computing ki **panch (5) zaroori khoobiyan (Essential Characteristics)** batai hain, jinhein hum bacho ki tarah asaan karke samajhte hain:

* **On-demand self-service:** Aap ko koi naya server chahiye? Aap ko kisi bande ko call karne ya lambi approvals lene ki zaroorat nahi. Aap ne sirf aik button click kiya ya aik API call ki, aur aap ka resource foran tayyar.
* **Broad network access:** Cloud ki saari sahuliyat internet ke zariye her jagah mojud hoti hain. Aap chahein laptop se chalaein, mobile se ya kisi bhi device se, bas internet hona chahiye.
* **Resource pooling:** Is ko **multitenant model** kehte hain. Is ki misaal aik hostel ya apartment building jaisi hai, jahan building aik hi hoti hai (physical hardware), lekin us mein alag alag log (consumers) apne apne kamron mein rehte hain aur aapas mein resources share karte hain, bina aik doosre ki privacy kharab kiye.
* **Rapid elasticity:** Yeh bilkul aik rubber band ki tarah hai. Jab aap ki website par bohot zyaada log (traffic) aa jaein, to cloud khud ko barha (expand) leta hai. Jab log chale jaein, to yeh wapas chota (shrink) ho jata hai. Is se resources zaya nahi hotay.
* **Measured service:** Cloud mein har cheez ka meter chal raha hota hai. Aap ko mukammal insights aur metrics (data) milte hain ke aap ne kitna resource istemaal kiya, taake aap ko pata ho ke kis cheez ke paise lag rahe hain.

Is ke ilawa, cloud ke offers ko in **teen (3) barray types** mein banta jata hai:

* **Public Cloud:** Yeh aik aisa cloud hota hai jise koi barri organization chala rahi hoti hai aur yeh aam public (kisi bhi aam bande ya company) ke liye khula hota hai, jaise AWS.
* **Private Cloud:** Yeh kisi aik specific company ka apna zati cloud hota hai, jo sirf unhi ke infrastructure ko virtualize karke unhi ke andaroni istemaal ke liye hota hai.
* **Hybrid Cloud:** Yeh Public aur Private cloud ka mix hota hai. Jab aap apne ghar ya daftar ke zati servers (on-premises data center) ko AWS (Public Cloud) ke sath jor dete hain, to aap aik Hybrid Cloud bana rahe hotay hain.

Cloud computing services ko mazeed in **teen classifications** mein divide kiya jata hai:

* **Infrastructure as a Service (IaaS):** Is mein aap ko bilkul basic cheezon ka control milta hai jaise computing power, storage, aur networking. Aap ko aik khali Virtual Machine milti hai, baqi OS aur software aap ne khud manage karna hota hai. *Examples:* Amazon EC2, Google Compute Engine, Microsoft Azure Virtual Machines.
* **Platform as a Service (PaaS):** Is mein infrastructure (servers, OS) cloud khud sambhalta hai, aap ko sirf apna code deploy karna hota hai. *Examples:* AWS Lambda, AWS App Runner, Google App Engine, aur Heroku. *(Aaj 2026 ke daur mein AWS Lambda aur App Runner bohot zyada mature aur modern microservices ke liye standard ban chuke hain).*
* **Software as a Service (SaaS):** Yeh bilkul bana banaya software hota hai jo cloud par chal raha hota hai, aap ne bas login karna hai aur istemaal shuru karna hai. *Examples:* Amazon WorkSpaces, Google WorkSpace, aur Microsoft 365.

AWS aik cloud-computing provider hai jiske paas IaaS, PaaS, aur SaaS ki bohot barri variety mojud hai. Chalein ab isko mazeed detail se dekhte hain.

---

## What is Amazon Web Services (AWS)?

Amazon Web Services (AWS) web services ka aik aisa mukammal platform hai jo aap ko computing, storage, aur networking ke solutions alag alag **layers of abstraction** par deta hai.

* **Low level of abstraction (Zyada control, zyada mehnat):** Is ki misaal yeh hai ke aap aik virtual machine ke sath khud se storage volumes (hard disks) attach kar rahe hain. Yahan aap ko hardware ki choti bareekiyan khud set karni parti hain.
* **High level of abstraction (Kam mehnat, asaan kaam):** Is ki misaal yeh hai ke aap bina kisi server ki fikar kiye, aik simple REST API ke zariye apna data store aur retrieve kar rahe hain (jaise S3). Aap ko piche ki koi fikar nahi hoti ke server kaise chal raha hai.

Aap AWS ki services ko har barray kaam ke liye istemaal kar sakte hain—chahe websites host karni hon, barri companies ke enterprise applications chalane hon, ya bohot barray data (Big Data) par mining aur analysis karna ho. Yeh web services internet par aam web protocols (jaise **HTTP**) ke zariye kaam karti hain. Inhein machines (code ke zariye) ya insaan (ek Graphical User Interface ya Console ke zariye) aasani se access kar sakte hain.

AWS ki do sab se mashhoor aur bunyadi services hain:

1. **EC2 (Elastic Compute Cloud):** Jo aap ko Virtual Machines (servers) deti hai.
2. **S3 (Simple Storage Service):** Jo aap ko data store karne ke liye unlimited capacity deti hai.

AWS ki khoobi yeh hai ke is ki saari services aapas mein bohot achhe tarike se mil kar kaam karti hain. Aap inhein use karke apne purane on-premises data center ko cloud par shift (migrate) bhi kar sakte hain, ya bilkul scartch se aik naya system bhi kharra kar sakte hain. Is ka pricing model **pay-per-use** hai, yani jitna chalao gay, utna bhariye.

Aik AWS customer hone ke naate, aap apni marzi se duniya ke mukhtalif data centers chun sakte hain kyunke AWS ke data centers poori dunya mein phailey huay hain. Agar aap Japan mein baith kar koi virtual machine chalana chahte hain, to us ka tareeqa bilkul waisa hi asaan hoga jaise Ireland mein chalane ka hai. Is se aap poori dunya ke customers ko un ke qareeb tareen location se minto mein serve kar sakte hain.

---

### Figure 1.1 Ka Breakdown (AWS data center locations)

<div align="center">
  <img src="./images/01.png" width="700"/>
</div>

Agar hum diye gaye **Figure 1.1** ke map ko ghaur se dekhein, to yeh dunya ke mukhtalif hisson mein AWS ke data centers (Regions) ko dikhata hai:

* **Global Distribution:** Map mein flag icons se dikhaya gaya hai ke US, Canada, Brazil, Ireland, UK, France, Sweden, Germany, Italy, South Africa, Bahrain, India, China, Hong Kong, South Korea, Japan, Singapore, Indonesia, aur Australia tak AWS ke data centers phailey huay hain.
* **Limited Access (*):** Map par kuch jagah star (`*`) laga hai, jaise US aur China mein. Is ka matlab hai ke in data centers tak har kisi ki pahunch nahi hoti. US Government ke liye alag se "GovCloud" hota hai taake security tight rahe, aur China ke data centers ke liye wahan ke local qawaneen ke mutabaq special conditions hoti hain.
* **2026 Context:** Book mein jin data centers (Canada, Spain, Switzerland, Israel, UAE, India, Australia, New Zealand) ke liye "announced" likha gaya tha, aaj **2026** mein yeh saare regions mukammal taur par active, mature, aur production-ready ho chuke hain, jo dunya bhar mein behtareen speed aur low latency faraham kar rahe hain.

---

Ab jab hum ne zaroori alfaz samajh liye hain, to sawaal yeh paida hota hai ke aap AWS ke sath aakhir kar kya sakte hain?

---

## What can you do with AWS?

Aap AWS par aik ya aik se zyaada services ko aapas mein jor kar har kism ki application chala sakte hain. Agla section aap ko is ki aik behtareen real-world misaal daita hai.

---

## Hosting a web shop

Chalein **John** ki kahani se samajhte hain. John aik medium e-commerce business ka CIO (Chief Information Officer) hai. Woh apni company ke liye aik aisi online shop (web shop) banana chahta hai jo bohot fast ho, reliable ho (kabhi band na ho), aur scalable ho (jitna traffic aaye, bardasht kar sakay).

**John Ka Purana Setup (On-Premises):**
John ne teen saal pehle aik physical data center mein kuch machines rent par li thin. Us setup mein:

* Aik **Web Server (WWW)** tha jo customers ki requests ko handle karta tha.
* Aik **Database** tha jo products ki detail aur orders ka data store karta bha.

John ab yeh dekh raha hai ke agar woh isi setup ko AWS par le jaye, to us ki company ko kya fayda hoga.

---

### Figure 1.2 Ka Breakdown (Running a web shop on-premises vs. on AWS)

<div align="center">
  <img src="./images/02.png" width="700"/>
</div>

Agar aap **Figure 1.2** ko dekhein, to is mein do hissay dikhaaye gaye hain jo John ke purane aur naye setup ka muwazna (comparison) karte hain:

* **Left Side (On-premises server):** Yahan aik box ke andar WWW (Web server) aur Database dono mojud hain. Lekin dono ke upar aik **Kala Gear (Requires maintenance)** ka icon bana hua hai. Is ka matlab hai ke hardware kharab ho, OS update karna ho, ya security patch lagana ho—yeh saara sar-dard John aur us ki team ka hai.
* **Right Side (AWS):** Jab John isi setup ko AWS par lata hai, to WWW abhi bhi aik EC2 instance (Virtual Machine) par chal raha hai jis par gear icon hai (yani OS level tak ki maintenance John ko karni hai). Lekin, database ke liye us ne **Amazon RDS** chun liya hai, jis par **Checkmark (Maintenance free / Fully managed by AWS)** laga hua hai. Is ka matlab hai ke database ka backup lena, updates karna ab AWS ki zimmedari hai, John ki nahi!

---

John sirf apne purane setup ko "Lift-and-shift" (jaisa hai waisa hi utha kar cloud par rakh dena) nahi karna chahta, balkay woh cloud ke asli faide uthana chahta hai. Is liye, kuch mazeed AWS services use karke John ne apne setup ko mazeed behtar banaya, jo ke in points mein tafseel se bayan kiya gaya hai:

* **Static aur Dynamic Content Ko Alag Karna:** Web shop par do tarah ka content hota hai. Aik hota hai **Dynamic Content** (jaise products ki badalti hui qeemtein) aur doosra hota hai **Static Content** (jaise company ka logo, purani images, CSS files). John ne in dono ko alag kar diya. Static content ko aik Content Delivery Network (CDN) ke zariye deliver kiya gaya, jis se web servers par se load bohot kam ho gaya aur website ki speed bohot barh gayi.
* **Maintenance-Free Services Par Shift Hona:** Database, Object Store, aur DNS system ke liye fully managed services chunne se John ki team infrastructure sambhalne ke jhanjhat se azaad ho gayi. Is se operational costs (chalane ka kharcha) kam ho gaya aur system ki quality behtar ho gayi.
* **Multi-VM Architecture aur Load Balancer:** Purane setup mein aik barra server tha. AWS par John ne usay utne hi kharche mein **chote chote teen virtual machines** mein divide kar diya. Ab fayda yeh hua ke agar un teen servers mein se koi aik server kharab (fail) bhi ho jaye, to samnay kharra **Load Balancer** automatic tarike se customers ki traffic ko baqi do chalte huay servers par bhej dega. Website band nahi hogi! Is se reliability (bhrose-mandi) bohot barh gayi.

---

### Figure 1.3 Ka Breakdown (Enhanced Web Shop Setup)

**Figure 1.3** mein John ka aik mukammal, modern aur behtareen cloud architecture dikhaya gaya hai. Chalein is ke har component ko step-by-step aur bacho ki tarah asaan karke samajhte hain ke request kaise flow karti hai:

<div align="center">
  <img src="./images/03.png" width="700"/>
</div>


1. **User aur Internet:** Jab koi user browser mein website open karta hai, to request internet ke zariye sab se pehle **DNS (Domain Name System)** ke paas jati hai (jis par Checkmark hai, yani AWS isay bina rukawat ke manage kar raha hai).
2. **Traffic Ka Batwara (Split):** DNS request ko do hisson mein baant deta hai:
* **Static Content (Right Side):** Logo aur images ke liye request seedhi **CDN (Content Delivery Network)** ke paas jati hai. CDN yeh static files peeche mojud **Object Store** (S3) se uthata hai. Kyunke CDN user ke shahar ya mulk ke qareeb tareen server se file de deta hai, is liye website "Improve performance" (bohot tezi se) load hoti hai. Is par bhi checkmark hai, yani yeh maintenance-free hai.
* **Dynamic Content (Left Side):** HTML aur coding wali requests seedhi **Load Balancer** ke paas aati hain. Load balancer aik traffic police wale ki tarah kaam karta hai jo anay wali traffic ko barabar tarike se peeche mojud do alag alag **WWW (Web servers)** par baant deta hai. Is se "Improve reliability" hoti hai ke agar aik server crash bhi ho jaye, to doosra zinda rehta hai. (Servers par gear icon hai, yani coding aur scaling policy aap ki hai, lekin load balancer managed hai).


3. **Managed Database (Amazon RDS):** Dono web servers niche mojud aik hi **Database (Amazon RDS)** se connect hotay hain jo orders aur data save karta hai. Is par checkmark laga hai, jo "Decrease maintenance costs" (John ka kharcha aur pareshani kam) karta hai kyunke database ko chalana ab AWS ka kaam hai.

John AWS par apna web shop chala kar bohot khush hai. Apni company ka infrastructure cloud par migrate karne ke baad, us ki website ki reliability (uptime) aur performance (speed) dono mein zameen-asman ka faraq aa chuka hai!

---

## Running a Java EE application in your private network

**Maureen** aik bari global corporation (dunya bhar mein pheli hui company) mein senior system architect hain. Un ki company ka apna jo physical data center tha, us ka rent ya contract kuch mahino mein khatam hone wala hai. Maureen chahti hain ke woh apni company ke enterprise business applications (jaise **Java Enterprise Edition [EE]** applications) ko AWS par shift kar dein, taake company ka kharcha kam ho (reduce costs) aur unhein kaam karne mein asani aur azaadi mile (gain flexibility).

In applications ke do barray hissay hain: aik **Application Server** (jo coding ko chalata hai) aur aik **SQL Database** (jo saara data save karta hai).

Maureen ne is poore setup ko AWS par chalane ke liye aik zabardast aur secure tareeqa apnaya:

* **Virtual Network (VPC) Ki Tyari:** Unho ne AWS ke cloud mein apna aik mukammal zati aur secure virtual network design kiya.
* **Corporate Network Se Connection (VPN):** Apne daftar ke chalte huay network (corporate network) ko cloud wale network ke sath jorne ke liye unho ne aik secure **VPN (Virtual Private Network)** connection ka istemaal kiya. Is ka faida yeh hai ke daftar ke log bina kisi rukawat ke cloud par mojud servers ko aam clients ki tarah access kar sakte hain.
* **Application aur Database Setup:** Unho ne AWS ki virtual machines par application servers install kiye taake Java EE app chal sakay, aur sath hi data store karne ke liye aik behtareen SQL database service (jaise Oracle Database EE ya Microsoft SQL Server EE) chun li.

### Security Aur Traffic Ka Control

Maureen ne security ko itna tight banaya ke koi aam banda ya hacker database tak na pohnch sakay. Is ke liye unho ne do bareekiyon par kaam kiya:

* **Subnets Ka Istemaal:** Unho ne apne virtual network ko chote chote hisson mein baant diya, jinhein **subnets** kehte hain. Har subnet ka security level alag rakha gaya. Yeh bilkul aisa hi hai jaise aik bank ke andar aam public ke liye alag kamra ho aur khazane (cash vault) ke liye alag sab se mehfooz kamra.
* **Access-Control Lists (ACLs):** Har subnet ke darwaze par aik security guard bitha diya jise ACLs kehte hain. Yeh guard tay karta hai ke kaun si traffic andar aa sakti hai aur kaun si baahir ja sakti hai (ingoing and outgoing traffic). *Example:* Unho ne rule banaya ke database wale subnet ko sirf aur sirf Java EE server wale subnet ka banda touch kar sakta hai, baqi poori dunya ke liye yeh rasta band hai. Is se company ka sab se important data (mission-critical data) bilkul safe ho gaya.
* **NAT aur Firewall:** Jo servers private subnets mein hain, unhein internet par bhejney ya internet se zaroori software updates lane ke liye unho ne **NAT (Network Address Translation)** aur firewall rules ka behtareen use kiya.

---

### Figure 1.4 Ka Breakdown (Running a Java EE application with enterprise networking)

<div align="center">
  <img src="./images/04.png" width="700"/>
</div>

Chalein hum **Figure 1.4** ke pooray network flow ko step-by-step aur bacho ki tarah asaan kar ke samajhte hain ke data kaise travel kar raha hai:

* **Corporate Network (`10.20.0.0/16`):** Yeh Maureen ka apna zati office ya company ka data center hai.
* **VPN Connection & VPN Gateway:** Office se AWS cloud ke andar safe rasta banane ke liye aik **VPN tunnel** bani hui hai jo AWS ke **VPN Gateway** par aakar milti hai. Is raste se office ka network seedha **JEE server** (Private Subnet) se baat karta hai.
* **AWS Virtual Network (`10.10.0.0/16`):** Yeh AWS ke andar Maureen ka banaya hua poora secured ilaqa (VPC) hai, jise teen floor ya subnets mein divide kiya gaya hai:
1. **Public Subnet (`10.10.0.0/24`):** Is floor par **NAT** baitha hua hai. Yeh floor **Internet Gateway** ke zariye baahir ki dunya (Internet) se seedha jura hua hai.
2. **Private Subnet (`10.10.1.0/24`):** Is ke andar **JEE server** (Java Enterprise Edition) chal raha hai. Is server ko internet se koi seedha nahi dekh sakta, lekin agar is server ne internet par koi request bhejni ho, to yeh upar mojud **NAT** ke paas jata hai, aur NAT is ki request Internet Gateway ke zariye baahir bhejta hai.
3. **Private Subnet (`10.10.2.0/24`):** Yeh sab se nichla aur locked floor hai jahan **SQL Database (Amazon RDS)** mojud hai. Is ka arrow sirf upar wale JEE server ke sath jura hai. Yani database tak pohnchne ka koi aur rasta dunya mein mojud nahi hai.



---

### Future Plans Aur Project Ki Kamyabi

Maureen ne abhi shuruat ke liye VPN connection lagaya hai, lekin unho ne aage ka plan (trade-off) pehle se soch rakha hai. Woh aane wale waqt mein aik **dedicated network connection** (jise AWS Direct Connect kehte hain) lagane ka soch rahi hain.

* *Design Decision:* VPN internet ke zariye chalta hai to speeds up-down ho sakti hain, lekin dedicated connection lagane se network ka kharcha (network costs) mazeed kam ho jayega aur data transfer ki speed (network throughput) bohot fast aur pakki ho jayegi.

**Project Ka Result:** Maureen ke liye yeh project aik bohot bari kamyabi raha! Pehle jis enterprise application ko set karne mein **mahino (months)** lag jaate thay, ab AWS par on-demand virtual machines, databases, aur networking infrastructure minto mein milne ki wajah se woh kaam **kuch ghanton (hours)** mein mukammal ho jata hai. Aur sab se bari baat, un ki company ka infrastructure cost on-premises ke muwazne bohot kam ho gaya.

---

## Implementing a highly available system

**Alexa** aik bohot tezi se barhne wale startup mein software engineer hain. Woh IT dunya ka aik mashhoor asool achhi tarah janti hain jise **Murphy’s Law** kehte hain: *"IT infrastructure mein jo cheez kharab ho sakti hai, woh aik na aik din zaroor kharab hogi!"*

Agar system band (outage) ho jaye, to startup ka poora business tabah ho sakta hai. Is liye Alexa aik aisa system design kar rahi hain jo **Highly Available (HA)** ho, yaani agar piche koi machine jal bhi jaye, to samnay customer ko pata bhi na chale aur website chalti rahe.

AWS ki khubsurti yeh hai ke is ki saari services ya to pehle se highly available hoti hain, ya unhein bohot asani se HA tarike se configure kiya ja sakta hai. Alexa ne aik aala qism ka high availability architecture taiyar kiya:

* **Database Ki Replication (Copy):** Database service ko Alexa ne automatic **replication** (aik database ka data sath ke sath doosre backup database mein copy hona) aur **fail-over handling** ke sath lagaya. Is ka faida yeh hai ke agar aap ka **Primary Database instance** kisi wajah se crash ya fail ho jaye, to jo **Standby Database** (backup database) hota hai, woh automatic tarike se khud ko naya primary database bana leta hai (promote ho jata hai). Is dauran website band nahi hoti.
* **Virtual Machines (Web Servers):** AWS par jo virtual machines (EC2 instances) hoti hain, woh khud se highly available nahi hotin (yani agar aik VM band hui to game over). Lekin Alexa ne dmaagh ladaya! Unho ne aik VM chalane ki bajaye **multiple virtual machines mukhtalif data centers (Availability Zones)** mein chala dein.
* **Load Balancer Ka Role:** In saare web servers ke aage unho ne aik **Load Balancer** kharra kar diya. Yeh load balancer lagatar servers ki sehat check karta rehta hai (Health Checks). Agar koi aik server kharab ho jaye, to load balancer chalaki se requests ko sirf un servers par bhejta hai jo bilkul theek aur sehatmand (healthy) chal rahe hotay hain.

---

### Figure 1.5 Ka Breakdown (Building a highly available system on AWS)

<div align="center">
  <img src="./images/05.png" width="700"/>
</div>

Chalein hum **Figure 1.5** ke diagram ko bacho ki tarah asaan breakdown ke sath samajhte hain ke yeh "Failure-proof" system kaise kaam karta hai:

* **User & Internet:** User jab bhi internet se website par aata hai, to us ki request seedhi servers par nahi jati.
* **Load Balancer (Checkmark - Highly available by default):** Request sab se pehle is load balancer par aati hai. Is par checkmark laga hai kyunke yeh AWS ki apni zimmewari hai ke yeh kabhi band nahi hoga. Yeh traffic ko do alag alag buildings (Data centers) mein barabar baant raha hai.
* **Data Center A aur Data Center B:** Yeh AWS ke do alag alag physical data centers (Availability Zones) hain. Agar aik building mein aag lag jaye ya bijli chali jaye, to doosri building bilkul safe rehti hai.
* **WWW Servers (Kala Cross - Single point of failure):** Dono data centers mein aik aik virtual machine (WWW) chal rahi hai. In par cross ka nishan is liye hai kyunke aik akeli VM crash ho sakti hai. Lekin chunke Alexa ne dono jagah VMs rakhi hain, is liye agar Data Center A ki VM ghalti se band bhi ho jaye, to Load Balancer traffic ko Data Center B ki VM par bhej dega.
* **Amazon RDS (Database Primary vs Standby):** Data Center A mein **Database (primary)** chal raha hai jo likhne parhne ka saara kaam sambhalta hai. Data Center B mein **Database (standby)** chup-chap baitha hai aur primary se saara data sath ke sath copy kar raha hai (cross arrows dikha rahe hain ke dono servers dono databases se connect ho sakte hain). Agar Data Center A mukammal doob gaya, to Data Center B ka standby database automatic active ho kar kaam shuru kar dega.



---

Alexa ne is behtareen planning se apne startup ko kisi bhi barrey nuksan ya outage se bacha liya hai. Lekin cloud engineering ka asool yahi hai ke aap kabhi sukoon se nahi baithte; Alexa aur un ki team hamesha agle kisi failure ke liye pehle se plan karti rehti hai aur apne systems ki mazbooti (resilience) ko lagatar behtar bana rahi hai. *(Aaj ke modern cloud era mein yeh Multi-AZ deployment har barri production application ka standard asool hai).*

