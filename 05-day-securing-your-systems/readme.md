# Securing your system: IAM, security groups, and VPC

## Securing your systems on AWS

Deewar (wall) ki misaal se security ko samajhna sab se asaan hai. Agar aap ne apne ghar ki hifazat ke liye ek mazboot deewar khari karni hai, toh aapko bohot saari eenton (bricks) ki zaroorat hogi. Agar un mein se ek bhi eent kamzor hui ya apni jagah par na hui, toh chor (hackers) us kamzoori ka faida utha kar andar ghus sakte hain.

Aapko screen par di gayi image (**Figure 5.1**) mein yeh poora concept saaf nazar aa raha hoga. Chaliye pehle is figure ko break down karte hain ke writer is deewar ke zariye kya samjhana chahta hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

---

### Figure 5.1 Ka Breakdown: Security Ki Deewar

**Figure 5.1** ko agar aap ghaur se dekhein, toh isme security ko ek eenton ki deewar ki tarah dikhaya gaya hai:

* **Buniyadi Eentein (Bottom Layer):** Is deewar ki buniyaad mein do main cheezein hain—**"Installing software updates"** (software ko up-to-date rakhna) aur **"Restricting access to your AWS account"** (apne cloud account ki chaabi har kisi ko na dena).
* **Darmyan Wali Eentein (Middle Layer):** Beech mein sab se important brick hai **"Controlling network traffic to and from your EC2 instances"** (yeh tay karna ke kaunsa traffic aapke virtual computers ke andar aa sakta hai aur kaunsa bahar ja sakta hai).
* **Upar Wali Eentein (Top Layer):** Deewar ko mukammal karne ke liye upar **"Creating a private network in AWS"** (apna ek khusoosi khufia network banana) aur **"Securing your applications"** (apne software ko khud andar se secure rakhna) ki bricks lagayi gayi hain.

**Sabaq (Lesson):** Agar aap ne cloud infrastructure aur apni application ko bilkul safe rakhna hai, toh aapko in tamam building blocks (eenton) ko ek sath sahi jagah par fit karna hoga. Agar ek bhi brick missing hui, toh deewar nakara ho jayegi.

---

Chaliye ab is deewar ki sab se ahem char eenton (bricks) ko bilkul asaan lafzon mein samajhte hain:

### 1. Installing software updates (Software Updates Ko Install Karna)

* **Bachon Wali Example:** Jaise aapke mobile phone mein har thode din baad apps ya operating system ki update aati hai. Agar aap update nahi karenge, toh phone slow ho jata hai ya usme bugs aa jate hain jinse hacker aapka data chura sakte hain.
* **Asaan Technical Detail:** AWS par jab hum virtual servers (EC2 instances) chalate hain, toh unme chalne wale softwares (jaise Linux, Web Server, ya Databases) mein rozana nayi kamzoriyan (vulnerabilities) dhoondi jati hain. Software banane wali companies in kamzoriyon ko theek karne ke liye patches (updates) nikalti hain. Hamari sab se pehli zimmedari yeh hoti hai ke hum in updates ko bina kisi deri ke install karein. Agar hum susti karenge, toh automated hackers hamare system ko asani se apna shikar bana lenge.

### 2. Restricting access to your AWS account (AWS Account Ka Access Mehdood Karna)

* **Bachon Wali Example:** Jaise aap apne ghar ki locker ki chaabi har kisi ke hath mein nahi dete. Sirf usay dete hain jise waqti zoorat ho, aur kaam khatam hote hi chaabi wapas le lete hain.
* **Asaan Technical Detail:** Agar aap ki team mein aur bhi log (coworkers) hain ya kuch automatic scripts hain jo aapke AWS account ko chalati hain, toh sab ko full control (Administrator access) dena sab se bari be-waqufi hai. Agar kisi buggy script (galti se likhe gaye code) ya kisi ghalti se coworker ne galat command chala di, toh aapka poora infrastructure tabah ho sakta hai. Is liye hum **Least Privilege Principle** use karte hain—yani jis ko jitna kaam hai, usay sirf utni hi ijazat (permissions) di jaye.

### 3. Controlling network traffic to and from your EC2 instances (Traffic Ko Control Karna)

* **Bachon Wali Example:** Ghar ke darwaze aur khidkiyan. Agar aap ke ghar mein mehman aane hain, toh aap sirf baithak (drawing room) ka darwaza kholte hain, ghar ke saare pichle darwaze aur khidkiyan band rakhte hain taake koi chor peeche se na ghuse.
* **Asaan Technical Detail:** Jab aap cloud par koi web server chalate hain, toh bahar ki duniya (internet) ke liye sirf do darwaze khule hone chahiye:
* **Port 80:** Yeh HTTP traffic ke liye hota hai (unsecured web browsing).
* **Port 443:** Yeh HTTPS traffic ke liye hota hai (secured/encrypted browsing).
In do ports ke ilawa, internet se aane wale tamam raste (jaise Database port ya SSH port) band hone chahiye taake koi bahar ka banda direct aapke servers mein dakhil na ho sake.



### 4. Creating a private network in AWS (Private Network Banana)

* **Bachon Wali Example:** Ek aam park aur ek VIP private society. Park mein koi bhi aa ja sakta hai, lekin VIP society ke gate par security hoti hai aur har koi wahan bina ijazat nahi ja sakta.
* **Asaan Technical Detail:** AWS par hum **VPC (Virtual Private Cloud)** banate hain. Iske andar hum **Subnets** (network ke chhote hisse) aur **Routing Tables** (traffic ka rasta dikhane wale maps) define karte hain. Hum apne sensitive databases ko hamesha ek "Private Subnet" mein rakhte hain, jiska internet se direct koi rasta hi nahi hota. Agar internet se koi traffic aana bhi chahe, toh woh pehle public area mein aayega, direct private area mein nahi ghus sakta.

---

### One Important Brick is Missing: Securing Your Applications (Application Security)

Deewar toh aap ne mazboot bana di, lekin agar aapne ghar ke andar khane mein zeher mila diya, toh nuksaan andar se hi hoga.

AWS aapke network aur hardware ko toh secure kar deta hai, lekin jo **Application** (software code ya website) aap chala rahe hain, use secure rakhna aapka apna kaam hai. Writer batata hai ke is book mein application security cover nahi ki gayi, lekin aapko ye baatein lazmi follow karni chahiye:

1. **User Input Verification:** User jo bhi data website par likhe (jaise search bar mein ya login form mein), usay check karein ke usme koi kharab code ya virus toh nahi hai (SQL Injection se bachne ke liye).
2. **No Plain Text Passwords:** Passwords ko database mein seedha seedha (plain text) kabhi save nahi karna chahiye. Unhein hamesha "Hash" (encryption technique) kar ke save karein.
3. **TLS/SSL Encryption:** Apne users aur virtual machines ke darmiyan hone wali baatcheet ko encrypt karein (jaise HTTPS use karna), taake beech raste mein koi aapka data chura na sake.

---

### Not all examples are covered by Free Tier

> ⚠️ **Cost Warning (Paise Lagne Ka Khatra):**
> Is chapter mein jo hum practical kaam karenge, woh saare ke saare AWS Free Tier (muft scheme) mein cover nahi hote. Jab bhi koi aisi configuration aayegi jahan aapke paise lag sakte hain, wahan ek **Warning Message** diya jayega.

* **Design/Money Saving Decision:** Iska behtareen hal yeh hai ke jab aap koi practical seekh rahe hon, toh usay seekhne ke baad furan un resources ko **delete (clean up)** kar dein. Agar aap unhein 2-3 din se zyada chalta chorenge, toh billing start ho sakti hai. Koshish karein ke chapter ko kuch hi dinon mein mukammal kar ke account saaf kar dein.

---

### Chapter requirements

Is chapter ke concepts ko asani se hazam karne ke liye, aapko in networking ke buniyadi lafzon (terms) ka pata hona zaroori hai. Chaliye inhein bilkul 1-line mein asaan kar ke samajh lete hain:

* **Subnet:** Ek bare network ka chota sa tukra ya kamra (jaise poore ghar mein se ek bedroom).
* **Route tables:** Ek rasta dikhane wala board (road sign) jo batata hai ke network ka traffic kis taraf jayega.
* **Access control lists (ACLs):** Network ke darwaze par khara security guard jo aane aur jaane wale traffic ko rules ke mutabaq check karta hai.
* **Gateway:** Ghar ka main gate jo aapko bahar ki duniya (internet) se jorta hai.
* **Firewall:** Ek hifazati deewar jo kharab ya dangerous traffic ko rokti hai.
* **Port:** Ek specific khidki ya darwaza jo kisi khas service (jaise website ya email) ke liye khususi taur par khola jata hai.
* **Access management:** Yeh tay karna ke kis insaan ko ghar ke kis kamre mein jaane ki permission hai aur kis ki nahi.
* **Basics of the Internet Protocol (IP), including IP addresses:** Internet par chalne wali har device ka ek unique identity card number ya ghar ka pata (address), taake data sahi jagah pahunch sake.

---


## Who’s responsible for security?

Cloud mein security kisi ek bande ka kaam nahi hai. Yeh ek **Shared-Responsibility Environment** hai. Iska matlab hai ke cloud ko secure rakhne ki zimmedari AWS aur aap (customer) ke darmiyan aadhi-aadhi banti hui hai.

Isko bachon ki tarah samajhne ke liye ek **Kiraaye ke Flat (Rented Apartment)** ki misaal lete hain.

> 🏢 **Ghar Wali Misal:**
> Building ka jo maalik (Landlord yani **AWS**) hai, uski zimmedari hai ke woh building ka main gate mazboot banaye, wahan security guard bithaye, aur aag lagne se bachane ke liye fire alarms lagaye. Lekin aapke flat ke andar ka jo darwaza hai, usay tala lagana, apni tijori ki chaabi sambhal kar rakhna, aur flat ke andar kis ko aane dena hai—yeh sab aapki (**Customer**) zimmedari hai. Agar aap apne flat ka darwaza khula chor dein aur chori ho jaye, toh aap landlord ko zimmedar nahi thehra sakte.

Chaliye ab is poore concept ko detail mein step-by-step samajhte hain ke AWS kya karta hai aur aapko kya karna hoga.

---

### AWS Ki Zimmedariyan (Security "OF" the Cloud)

AWS is baat ka zimmedar hai ke jo physical infrastructure (hardware, data centers, cables) aap use kar rahe hain, woh har tarah se safe ho. AWS in char main cheezon ki hifazat karta hai:

* **Protecting the network (Network Ki Hifazat):** AWS apne network ko har waqt chalne wale automatic monitoring systems aur bohot hi heavy internet pipelines ke zariye secure rakhta hai. Iska sab se bara faida yeh hota hai ke yeh aapke systems ko **DDoS (Distributed Denial of Service) attacks** se bachata hai.
* *DDoS Kya Hai?* Farz karein aapki ek khilono ki dukan hai jahan aam log aate hain. Agar koi dushman 10,000 nakli bache aapki dukan ke samne khare kar de taake asli khareedar andar na aa sakein, toh use DDoS attack kehte hain. AWS ka automatic system in nakli logon (fake traffic) ko pehle hi rok deta hai.


* **Performing background checks on employees (Mulaazmeen Ki Janch):** Jo engineers ya staff AWS ke un ahem hisson (sensitive areas) mein kaam karte hain jahan physical servers pare hain, AWS unka mukammal criminal aur professional background check karta hai. Kisi bhi aam ya shakki bande ko un servers ke paas jaane ki ijazat nahi hoti.
* **Decommissioning storage devices (Purani Hard Drives Ko Tabah Karna):** Jab AWS ke data centers mein koi hard drive ya storage device purani ho jati hai ya kharab ho jati hai (End of Life), toh AWS use bahein phenkne ya bechne ke bajaye **physically destroy** (shredder mein daal kar ya hathoray se) tukre-tukre kar deta hai. Iska faida yeh hota hai ke koi badmaash us purani hard drive ko dhoodh kar aapka data wapas nikal nahi sakta.
* **Ensuring the physical and environmental security of data centers (Data Centers Ki Hifazat):** Jahan AWS ke hazaron servers lage hain, un buildings ki hifazat bohot sakht hoti hai. Wahan har waqt security staff maujood hota hai, biometric locks hote hain, aur agar khuda-na-khwasta aag lag jaye toh us se bachne ke liye advance **Fire Protection Systems** lage hote hain jo pani ke bajaye special gas use karte hain taake computers kharab na hon.

> 📋 **Compliance Note:** AWS ke in tamam hifazati intezamaat ko bar-bar **Third-Party Auditors** (bahar ki top companies) check karti hain aur certificate deti hain. Agar aap dekhna chahein ke AWS standard par poora utar raha hai ya nahi, toh aap har waqt unki up-to-date report is website par check kar sakte hain: [aws.amazon.com/compliance/](https://aws.amazon.com/compliance/).

---

### What are your responsibilities? See the following:

AWS ne aapko ek bilkul safe aur clean ghar bana kar de diya hai, lekin us ghar ke andar ka nizaam chalana aapki zimmedari hai. **2026** ke modern cloud dor mein aapko in panch cheezon ka khususi khayal rakhna hota hai:

* **Configuring access management using AWS IAM (Chaabiyan Aur Permission Set Karna):** Aapki sab se pehli zimmedari yeh hai ke aap **AWS IAM (Identity and Access Management)** ka sahi use karein. Iska matlab yeh hai ke agar aapke paas **S3** (data save karne ki jagah) aur **EC2** (virtual servers) hain, toh aap har bande ya script ko sirf utna hi access dein jitna uske kaam ke liye zaroori ho (**Minimum Access**). Kisi ko bhi faltu ijazat nahi honi chahiye.
* **Encrypting network traffic using HTTPS (Chitthi Ko Lock Box Mein Bhejna):** Jab aapka data internet par chal raha ho, toh aapki zimmedari hai ke aap use **Encrypt** (code word mein tabdeel) karein, jaise **HTTPS (SSL/TLS)** use karna. Agar aap aisa nahi karenge, toh internet ke raste mein betha koi bhi hacker aapka data parh sakta hai ya usme her-pher kar sakta hai.
* **Configuring a firewall with security groups and NACLs (Chowkidar Khare Karna):** Aapko apne virtual network ki hifazat ke liye firewall set karni hoti hai. AWS mein iske liye do main cheezein hoti hain:
* **Security Groups:** Yeh aapke server (EC2) ka apna personal chowkidar hota hai.
* **NACLs (Network Access Control Lists):** Yeh aapke poore sub-network (subnets) ka main gate ka chowkidar hota hai.
In dono ko sahi tarah configure karna aapka kaam hai taake sirf sahi traffic andar aa sakay aur faltu traffic bahar hi ruk jaye.


* **Encrypting data at rest (Sote Hue Data Ko Tala Lagana):** Jab aapka data database mein ya kisi storage system (jaise EBS ya S3) mein chup-chap para hua ho (At Rest), toh use bhi encrypt karna aapki zimmedari hai. Modern AWS mein aap ek click se encryption on kar sakte hain taake agar koi data chura bhi le, toh use parh na sakay.
* **Managing patches for the OS and additional software (Software Ko Update Rakhna):** AWS aapko virtual machine (EC2) bana kar de deta hai, lekin us machine ke andar jo Operating System (jaise Linux ya Windows) chal raha hai, aur jo softwares aapne khud install kiye hain, unko update (patch) rakhna aapka kaam hai. Agar unme koi security vulnerability aati hai, toh unka patch aapne khud lagana hai.

---

### Khulasa (Summary)

Security hamesha **AWS aur Aapke darmiyan ek teamwork** ka naam hai. Agar AWS apna kaam perfect kare aur aap upar diye gaye rules ke mutabaq apna kaam sahi se karein, toh aap cloud par aala tareen security (High Security Standards) haasil kar sakte hain.

Yad rakhein, agar aap ne flat ka darwaza khula chora (yani firewall ghalt set ki), toh AWS building ka guard hone ke bawajood aapko chori se nahi bacha sakega.

Agar aap is model ko mazeed gehrai se aur tasweeron ke sath samjhana chahte hain, toh aap unki is official link ko dekh sakte hain: [aws.amazon.com/compliance/shared-responsibility-model/](https://aws.amazon.com/compliance/shared-responsibility-model/).

---