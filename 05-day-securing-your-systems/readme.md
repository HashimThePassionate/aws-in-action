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