# sing a relational database service: RDS

## This chapter covers

* **Launching and initializing relational databases with RDS:** Amazon RDS (Relational Database Service) ka istemal karte hue ek nayi relational database ko shuru (launch) aur tayar (initialize) karna.
* **Creating and restoring database snapshots:** Database ka aik point-in-time photo ya backup (Snapshot) lena aur agar kabhi data kharab ho jaye ya delete ho jaye toh us snapshot se database ko wapis pehle jaisa (restore) karna.
* **Setting up a highly available database:** Aisa database system tayar karna jo kabhi bhi band (down) na ho. Agar aik jagah koi masla aaye bhi, toh doosri jagah se database baghair rukaawat ke chalta rahe.
* **Monitoring database metrics:** Database ki sehat, speed, memory, aur CPU ka lagatar jaiza lena taake pata chal sake ke system sahi tarah kaam kar raha hai ya nahi.
* **Tweaking database performance:** Database ki setting aur speed ko behtar banana taake queries tezi se run hon aur application fast chale.

---

### Relational Databases aur AWS ka Ta'aruf

**WordPress** aik aisi mashhoor website banana wali application (Content Management System ya CMS) hai jo pure internet ki aik bohot bari taaddad ko chalati hai. Internet par maujood bohot sari doosri applications ki tarah, WordPress bhi apne tamam articles, comments, user accounts, aur doosri information ko mehfooz rakhne ke liye ek **Relational Database** ka istemal karta hai.

Relational databases ko structured data (yaani aisa data jo rows aur columns ki shakal mein saaf suthra rakha gaya ho) ko store karne aur dhoondne (query karne) ke liye sab se ziada reliable aur standard zariya mana jata hai. Bohot sari mashhoor applications **MySQL** jaise relational database systems par chalti hain.

#### Relational Databases kis cheez par focus karte hain? (ACID Property)

Relational databases ka sab se bada maqsad **Data Consistency** (data ka har halat mein sahi aur mehfooz rehna) hota hai. Ye databases **ACID** guarantees dete hain:

* **Atomicity (A - Aisa kaam jo poora ho ya bilkul na ho):** Transaction ya toh poori tarah kamyab hogi ya phir bilkul nahi hogi. *Misaal ke taur par:* Agar aap bank app se apne dost ko Rs. 100 bhejte hain, toh aap ke account se paise katna aur dost ke account mein paise jama hona—dono kaam hone chahiye. Agar beech mein net kat jaye, toh paise wapis aap ke account mein hi rahenge, adhe raste mein ghayab nahi honge.
* **Consistency (C - Rules ka hamesha follow hona):** Database mein sirf wahi data save hoga jo us ke banaye gaye qawaneen (rules) par poora utarta ho.
* **Isolation (I - Aik doosre se alag rehna):** Agar aik waqt mein hazaron log database ko istemal kar rahe hain, toh aik bande ki transaction doosre bande ke kaam mein rukawat ya garbar paida nahi karegi.
* **Durability (D - Data ka hamesha ke liye mehfooz hona):** Ek baar jab transaction complete ho jaye aur data save ho jaye, toh chahe light chali jaye ya server crash ho jaye, data zaye (lost) nahi hoga.

Aise sakht usool accounting applications, bank accounts, aur e-commerce stores ke liye bohot zaroori hotay hain.

---

### AWS Par Relational Database Chalane Ke Do Raaste

Agar aap AWS cloud par relational database chalana chahte hain, toh aap ke paas do raste (options) hote hain:

1. **Amazon RDS (Managed Service):** AWS ki taraf se bani banayi database service istemal karein jahan hardware, updates, aur backups ki tension AWS khud leta hai.
2. **Self-Hosted (Virtual Machines par khud manage karna):** EC2 Virtual Machines le kar un par khud MySQL ya koi bhi database manual install karein aur khud manage karein.

#### Amazon RDS Kya Hai?

Amazon Relational Database Service (Amazon RDS) aik ready-to-use managed database service hai. Ye aam taur par istemal hone wale tamam bade database engines ko support karti hai:

* **PostgreSQL**
* **MySQL**
* **MariaDB**
* **Oracle Database**
* **Microsoft SQL Server**
* **Amazon Aurora:** Ye AWS ka apna cloud-native database engine hai jo MySQL aur PostgreSQL ke sath 100% compatible hai aur bohot ziada fast aur scalable hai.

Agar aap ki application pehle se in mein se koi database engine istemal kar rahi hai, toh RDS par shift hona bohot aasan hai. Sab se ziada dhyan data ko purane server se RDS par shift (migrate) karne mein dena hota hai.

---

### Managed Service RDS vs. Self-hosted on virtual machines

AWS mein **Managed Service** ka matlab hai ke service dene wala (AWS) system ko chalane, updates lagane, aur hardware ki dekh-bhal karne ki zimmedari leta hai.

Below diye gaye table mein RDS aur Virtual Machines (EC2) par khud database chalane ka mwaazna (comparison) kiya gaya hai:

| Comparison Feature | Amazon RDS | Self-hosted on virtual machines |
| --- | --- | --- |
| **Cost for AWS services** | Zyada hota hai kyunke RDS ki cost virtual machines (EC2) se zyada hoti hai. | Kam hota hai kyunke virtual machines (EC2) RDS ke muqablay mein sasti hoti hain. |
| **Total cost of ownership** | Kam hota hai kyunke operating costs bohot sare customers ke darmiyan split ho jati hain. | Bohat zyada hota hai kyunke apne database ko manage karne ke liye aap ko apni khud ki manpower ki zaroorat parti hai. |
| **Quality** | Managed service ki zimmedari AWS ke professionals ki hoti hai. | Aap ko professionals ki team banani parti hai aur khud quality control implement karna parta hai. |
| **Flexibility** | Zyada hoti hai, kyunke aap relational database system aur ziyadatar configuration parameters chun sakte hain. | Is se bhi zyada hoti hai, kyunke aap virtual machines par install kiye gaye relational database system ke har hissay ko control kar sakte hain. |

#### Is Table Ka Asan Matlab:

1. **AWS Service Bill (Service ki qeemat):** RDS ka monthly bill aik aam EC2 Virtual Machine se thoda ziada hota hai kyunke AWS aap ko auto-backup, security patches, aur management ki suhulat de raha hai.
2. **Total Cost of Ownership (TCO - Kul Kharcha):** Jab aap khud Virtual Machine par database chalate hain, toh aap ko system administrators aur database experts (DBAs) ko bari salaries deni parti hain taake wo database ko 24/7 manage karein. RDS mein AWS ye saara kaam khud karta hai, is liye overall kharcha (TCO) RDS par kam aata hai.
3. **Quality (Kaam ki behtari):** RDS ko AWS ke sab se experienced engineers manage kar rahe hote hain, jabke self-hosted mein aap ko khud experts dhoond kar team banani parti hai.
4. **Flexibility (Azaadi):** Self-hosted mein aap ke paas OS level ka full control hota hai, jabke RDS mein aap ko Operating System ka access nahi milta, lekin aap database ke tamam zaroori parameters ko asani se change kar sakte hain.

Virtual machines par apna database khareed kar zero se set karna bohot ziada waqt aur tajarba (know-how) maangta hai. Is liye AWS hamesha recommend karta hai ke **Amazon RDS** ka istemal kiya jaye taake aap ke kaam ki quality behtar ho aur operational kharcha kam ho.

---

## Figure 10.1 The company’s blogging infrastructure consists of two load-balanced web servers running WordPress and a MySQL database server.

Figure 10.1 mein ek mukammal production-ready WordPress architecture ko dikhaya gaya hai. Is architecture ke har aik hissaye aur request flow ko step-by-step samajhte hain:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

#### Request Ka Safar (Step-by-Step Flow):

1. **Step 1 (User Request):** User apne web browser se website open karta hai (HTTP Request bhejta hai). Ye request sab se pehle **Load Balancer** ke paas jaati hai.
2. **Step 2 (Traffic Distribution):** Load Balancer (ELB) us request ko pakad kar peeche maujood do Virtual Machines (EC2 instances) ke darmiyan barabar baant (distribute kar) deta hai.
3. **Step 3 (Processing & Storage Connection):** Har Virtual Machine par Web Server (WordPress + PHP) chal raha hota hai. Ye web servers do jagah connect hote hain:
* **Network Filesystem (Amazon EFS):** Tamam images, media files, aur uploaded files ko store aur access karne ke liye `NFSv4.1` protocol ke zariye EFS se connect hote hain. Is se dono virtual machines aik hi shared filesystem ko access karti hain.
* **Database (Amazon RDS):** Website ke articles, comments, user IDs, aur passwords ko query/store karne ke liye managed MySQL database (RDS) se connect hote hain.



#### Component Breakdown (Diagram Mein Maujood Har Component Ki Detail):

* **Elastic Load Balancing (ELB):** Ye AWS ki managed load balancer service hai jo **Fault Tolerant** hai (yaani agar traffic bohot ziada aa jaye ya koi issue ho jaye, toh ye khud ko sambhal leti hai).
* **Virtual Machines (EC2):** Infrastructure as a Service (IaaS) jo Linux ya Windows OS provide karti hai. Yahan par WordPress aur PHP code execute hota hai.
* **Network Filesystem (EFS):** Elastic File System multiple virtual machines ko ek sath files store aur read karne ki ijazat deta hai.
* **Amazon RDS (MySQL):** Managed MySQL database jahan data store hota hai. AWS is ke backups, security patches, aur replication ki zimmedari khud leta hai.
* **Security Groups (Firewalls):** Har component (Load Balancer, Web Servers, EFS, RDS) ke aage **Firewall** ka icon bana hai. Ye Security Groups hain jo ye tay karte hain ke kaun sa traffic andar aa sakta hai aur kaun sa bahar ja sakta hai (maslan: RDS sirf EC2 web servers se traffic accept karega, direct public internet se nahi).

---

> **Not all examples are covered by the Free Tier**
> Is chapter mein di gayi tamam misalein AWS Free Tier ke andar shamil nahi hain (kuch features ke extra charges lag sakte hain). Jab bhi koi aisa step aayega jiss par paise lag sakte hon, wahan warning message dikhaya jayega.
> **Paisa Bachane Ka Tarika:** Agar aap ne is book ke liye bilkul naya AWS account banaya hai aur aap is chapter ki saari exercises ko 2-3 dino mein mukammal kar ke saare resources ko delete (clean up) kar dete hain, toh aap ko koi extra charge nahi parega. Is liye zaroori hai ke aap is chapter ko kuch hi dino mein khatam karein aur aakhir mein saare banaye gaye resources ko delete kar dein.

---