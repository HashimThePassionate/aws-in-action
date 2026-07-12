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

