# A Simple Example : Wordpress in 15 minutes

Socho hum ek game khel rahe hain jahan hum ek website ko internet par chalana chahte hain. Is chapter mein hum seekhenge ke aik aam website (jaise WordPress blog) ko sirf 15 minutes mein AWS cloud par kaise shift (migrate) karte hain aur wahan is ka ek naya dhancha (infrastructure) khara karte hain.

Puri book mein hum isi WordPress ki misaal ko bar-bar dekhnge taake mushkil cheezein aaram se samajh aa jayein. Jaise database ke concepts ko hum chapter 10 mein deeply parhain ge aur auto-scaling (yani users zyada hon toh machines ka khud-ba-khud barh jana) ko chapter 17 mein seekhenge.

> **Zaroori Note (Free Tier):** Agar aap ka AWS account bilkul naya hai aur aap ne koi aur cheez nahi chalai hui, toh yeh lab bilkul muft (Free) hai. Bas is baat ka khayal rakhna hai ke kaam khatam hote hi 2-3 din ke andar sab kuch delete (clean up) kar dena hai taake baad mein koi charges na parhein.

### Ek Real-World Example Se Samajhte Hain

Farz karo aap aik aisi company mein kaam karte ho jo naye software aur operations engineers ko attract karne ke liye ek blog chalati hai, aur is blog ke liye WordPress use karti hai. Is blog par rozana taqreeban 1,000 log aate hain. Purane tareeqe (on-premises/apne server) par is website ko chalane ka kharcha $250 mahina aa raha hai, jo ke kafi mehnga hai! Aur sab se bara masla yeh hai ke yeh blog mahine mein kahin dafa band (outage/down) ho jata hai.

Company chahti hai ke naye engineers par acha impression pare, is liye website ka **Highly Available** hona zaroori hai. Highly available ka matlab hai **99.99% Uptime**—yani website saal bhar mein sirf kuch hi minutes ke liye band ho sakti hai, is se zyada nahi! Is cheez ko check karne ke liye hum ek Proof of Concept (PoC) kar rahe hain jahan hum yeh 3 kaam karenge:

1. WordPress ke liye highly available setup banayenge.
2. Mahine ka kharcha (cost) check karenge.
3. Aakhri faisla karke infrastructure ko delete kar denge.

WordPress chalne ke liye **PHP** language use karta hai, data store karne ke liye **MySQL database** chahiye hota hai, aur images/files ke liye storage disk chahiye hoti hai. Pages serve karne ke liye **Apache** web server kaam karta hai. Ab hum in requirements ko AWS ke tools ke sath jorain ge.

---

## Creating your infrastructure

Upar diye gaye purane system ko AWS par laane ke liye hum niche di gayi 5 AWS services ka use karenge:

* **Elastic Load Balancing (ELB):** Yeh samajh lo aik traffic pulis wala (traffic cop) hai. Jab bohot saare log website par aate hain, toh yeh load balancer un sab ka load alag-alag computer machines par barabar baant (distribute) deta hai. Yeh khud kabhi fail nahi hota (highly available by default). Yeh computers ka 'health check' bhi karta hai—yani check karta rehta hai ke computer theek kaam kar raha hai ya nahi. Agar koi computer kharab ho jaye, toh yeh wahan traffic bhejna band kar deta hai. Yahan hum **Application Load Balancer (ALB)** use kar rahe hain jo **Layer 7 (HTTP/HTTPS)** par kaam karta hai, yani yeh website ke web traffic ke mutabaq smart decisions leta hai.
* **Elastic Compute Cloud (EC2):** Yeh cloud ke andar aap ke virtual computers (machines) hain. Hum yahan Linux operating system ka ek khas version use kar rahe hain jise **Amazon Linux** kehte hain (jo AWS ke liye special optimized hai), lekin aap Ubuntu ya Windows bhi use kar sakte hain. Kyunki computers achanak kharab ho sakte hain, is liye design decision yeh hai ke hum ek ke bajaye **kam se kam 2 EC2 machines** lagayenge. Agar aik machine kharab ho gayi, toh Load Balancer foran saari traffic doosri machine par bhej dega jab tak pehli machine ki jagah nayi machine na aa jaye.
* **Relational Database Service (RDS) for MySQL:** WordPress ka sara data (posts, comments, user info) MySQL database mein save hota hai. Agar hum khud computer par database banayein toh backup lena aur updates install karna bohot mushkil hota hai. **RDS** aik aisi service hai jahan AWS database ka sara boring aur mushkil kaam (backups lena, security patches lagana, updates install karna) khud sambhal leta hai. RDS database ko bhi highly available bana deta hai taake data kabhi zaya na ho.
* **Elastic File System (EFS):** WordPress ki apni application files aur users ki upload ki hui images ko save karne ke liye ek shared storage chahiye hoti hai. Kyunki hamari website 2 alag-alag EC2 machines par chal rahi hai, dono computers ko ek hi jagah se images uthani aur pehchanni hain. **EFS** ek aisi network file storage hai jise dono computers aik sath use kar sakte hain network protocol (**NFSv4.1**) ke zariye. Yeh bohot mazboot (durable) aur highly available hoti hai.
* **Security groups:** Yeh aap ke computers, databases, aur load balancers ke bahar khara aik virtual security guard (**Firewall**) hai. Yeh control karta hai ke kaun andar aa sakta hai aur kaun bahar ja sakta hai. Jaise hum rule banayenge ke internet se koi bhi shakhs Load Balancer ke Port 80 (HTTP traffic) par aa sakta hai. Lekin database (Port 3306) par sirf hamare web servers (EC2 machines) hi baat kar sakte hain, koi bahar ka banda direct database ko touch nahi kar sakta.

### Figure 2.1 Breakdown

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

Chalein ab upar di gayi image (**Figure 2.1**) ko samajhte hain ke yeh sab mil kar kaise kaam karte hain:

1. **Users aur Incoming Requests:** Sab se pehle left side par users hain jo website open karne ke liye requests bhejte hain.
2. **Load Balancer (ELB) aur Firewall:** Yeh requests sab se pehle ek Firewall (Security Group) se guzarti hain aur Load Balancer ke paas aati hain jo traffic ko distribute karta hai.
3. **Virtual Machines (EC2):** Load balancer traffic ko do alag-alag virtual machines (EC2 web servers) par bhej raha hai jahan WordPress chal raha hai. Har machine ke agay apna Firewall laga hua hai.
4. **Storage aur Database:** Yeh dono web servers right side par majood do cheezon se connect hain: upar **Network filesystem (EFS)** hai jahan files store hoti hain (NFSv4.1 protocol ke zariye), aur neeche **Amazon RDS Database** hai jahan MySQL chal raha hai. Dono ke baahar bhi Firewalls lage hain taake security tight rahe.

---

### CloudFormation Ke Zariye Automation

Yeh saari cheezein hath se banana kafi mushkil lagta hai, lekin AWS mein hum yeh sab kuch sirf chand clicks se **AWS CloudFormation** ke zariye automatic bana sakte hain. CloudFormation background mein yeh saare kaam khud hi kar deta hai:

1. Load Balancer (ELB) banata hai.
2. MySQL Database (RDS) ready karta hai.
3. Network filesystem (EFS) khara karta hai.
4. Firewall rules (security groups) attach karta hai.
5. Do virtual machines (EC2) banata hai, un par EFS mount karta hai, Apache aur PHP install karta hai, WordPress 4.8 download karta hai, database configure karta hai, aur web server start kar deta hai.

Is setup ko shuru karne ke liye aap ko AWS Management Console (`[https://console.aws.amazon.com](https://console.aws.amazon.com)`) open karna hoga. Wahan navigation bar mein 'Services' par click karke **CloudFormation** select ya search karein.

> **Zaroori Baat (Default Region):** Hamesha yaad rakhein ke cloud mein aap ko koi ek ilaqa (Region) chunna hota hai. Hum is book ke saare kaam **N. Virginia (us-east-1)** region mein karenge. Screen ke top-right corner se hamesha check kar lein ke N. Virginia select ho.

Ab **Create Stack** par click karein aur **With New Resources (Standard)** ko select karein taake 4-steps ka wizard shuru ho sake.

### Figure 2.2 Breakdown

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

Jaise ke aap **Figure 2.2** mein dekh sakte hain, yeh hamara **Step 1 of 4** hai:

* Sab se pehle **Template is ready** ko select karna hai, kyunki hamare paas bani-banayi template file majood hai.
* Template source mein **Amazon S3 URL** par click karna hai aur niche box ke andar exact yeh link paste karna hai: `[https://s3.amazonaws.com/awsinaction-code3/chapter02/template.yaml](https://s3.amazonaws.com/awsinaction-code3/chapter02/template.yaml)`. Is ke baad **Next** button par click karna hai.

### Figure 2.3 Breakdown

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

Jaise ke aap **Figure 2.3** mein dekh sakte hain, yeh hamara **Step 2 of 4** hai jahan details enter karni hain:

* **Stack name:** Yahan box mein chote lafzon mein `wordpress` likhna hai taake is poore setup ka ek naam rakha ja sake.
* **Parameters:** Niche `WordpressAdminPassword` ka option hai, yahan aap ne apna ek secret password likhna hai jo aap WordPress dashboard login ke liye use karenge. Is ke baad **Next** daba dena hai.

### Figure 2.4 Breakdown

<div align="center">
  <img src="./images/04.png" width="600"/>
</div>

Jaise ke aap **Figure 2.4** mein dekh sakte hain, yeh hamara **Step 3 of 4** hai jahan hum **Tags** lagate hain:

* Tags sticker ki tarah hote hain jo resources par pehchan ke liye lagaye jaate hain taake testing aur production ka farq pata chal sake ya kharche track ho sakein. Yahan hum Key mein `system` aur Value mein `wordpress` likhenge. Is se humein asani se pata chal jayega ke yeh saare components isi WordPress project ka hissa hain.
* *Tagging Rules:* Key ka naam 128 characters se chota aur Value ka naam 256 characters se kam hona chahiye. Is ke baad **Next** button dabayein.

---

## Additional CloudFormation stack options

Is step par mazeed advance cheezein aati hain jaise permissions (IAM roles) lagana ya notifications set karna. 99% aam kaamon mein hmeim in ki zaroorat nahi hoti, is liye hum inhein bina chere aage barh sakte hain.

### Figure 2.5 Breakdown

<div align="center">
  <img src="./images/05.png" width="600"/>
</div>

Jaise ke aap **Figure 2.5** mein dekh sakte hain, yeh hamara **Step 4 of 4** (Review screen) ka aakhri hissa hai:

* Yahan AWS aap se ijazat mangta hai ke CloudFormation background mein security aur access management (IAM) resources bana sake. Aap ne blue box ke andar majood **"I acknowledge that AWS CloudFormation might create IAM resources."** ke checkbox ko tick mark (check) karna hai.
* Phir right side par orange rang ke **Create stack** button par click kar dena hai.

### Figure 2.6 Breakdown

<div align="center">
  <img src="./images/06.png" width="600"/>
</div>

Jaise hi aap button click karenge, aap ke samne yeh screen (**Figure 2.6**) aa jayegi:

* Yahan aap ke stack ka status **CREATE_IN_PROGRESS** dikhayi dega. Is ka matlab hai ke cloud mein computers, networks aur databases background mein ek blueprint se banna shuru ho gaye hain.
* Yeh poora setup banne mein taqreeban **15 minutes** langenge. Aap thori thori der baad upar diye gaye gol arrow (Refresh button) par click kar ke status check kar sakte hain.

### Figure 2.7 Breakdown

<div align="center">
  <img src="./images/07.png" width="600"/>
</div>

Jab saara kaam mukammal ho jaye ga, toh status badal kar hira (green) rang mein **CREATE_COMPLETE** ho jayega jaisa ke **Figure 2.7** mein dikhaya gaya hai:

* Aap ne apni `wordpress` row ke shuru mein majood checkbox ko select karna hai.
* Phir center mein majood tabs mein se **Outputs** tab par click karna hai.
* Outputs ke andar aap ko ek **URL** milega (jaise `http://wordp-LoadB-...`). Yeh aap ki live WordPress website ka address hai! Is link par click karte hi aap ka blog browser mein khul jayega.

---

## Automation references

Ab aap soch rahe honge ke yeh sab itni asaani se khud-ba-khud kaise ho gaya? Is ka jawab hai **Automation**. AWS ka sab se bada asool hi yeh hai ke aap har cheez ko automate kar sakte hain.

Background mein hamara poora cloud infrastructure ek blueprint (naksha/template) ko dekh kar bana hai. Is tarah se infrastructure ko program karne ka tareeqa hum chapter 4 mein deeply seekhenge aur software ko automatic deploy karne ka tareeqa chapter 15 mein parhenge. Next section mein hum is poore blogging infrastructure ko mazeed andar se explore karenge!


---