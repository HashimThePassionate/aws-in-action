# Scaling up and down Autoscaling and CloudWatch

## This chapter covers

* **Creating an Auto Scaling group with a launch template:** Ek Auto Scaling group (ASG) ko Launch Template ki madad se banana. Modern AWS mein Launch Template humari virtual machines (EC2 instances) ka blueprint ya design hoti hai, jis mein hum batate hain ke naye banne wale server ka size, operating system aur configuration kya hogi.
* **Using autoscaling to change the number of virtual machines:** Auto Scaling ka istemal kar ke system load ke mutabiq virtual machines ki ginti (tadaad) ko khud-ba-khud kam ya ziada karna.
* **Scaling a synchronous decoupled app behind a load balancer (ALB):** Ek synchronous decoupled application ko Application Load Balancer (ALB) ke peeche rakh kar scale karna. Synchronous ka matlab hai jab user koi request bhejta hai (jaise website open karna) toh usay foran response chahiye hota hai.
* **Scaling an asynchronous decoupled app using a queue (SQS):** Ek asynchronous decoupled application ko Simple Queue Service (SQS) ki madad se scale karna. Asynchronous ka matlab hai ke user apna kaam (jaise video process karna) submit kar ke chala jata hai aur kaam background mein queue ke zariye aaram se hota rehta hai.

---

Suppose you’re organizing a party to celebrate your birthday. How much food and drink do you need to buy? Calculating the right numbers for your shopping list is difficult due to the following factors:

Writer ne yahan AWS scaling ko samjhane ke liye ek bohot hi aasaan real-world example di hai:

Farz karein aap apni birthday party plan kar rahe hain. Aap ko party ke liye kitna khana aur kitni cold drinks khareedni chahiye? Shopping list ke liye sahi tadaad ka andaza lagana bohot mushkil kaam hai kyunke do (2) unpredictable (an-dekhe) maslay samne aate hain:

* **How many people will attend? You received several confirmations, but some guests will cancel at short notice or show up without letting you know in advance. Therefore, the number of guests is vague:** Party mein kitne log aayenge? Aap ko kuch doston ne aane ka bola hai, lekin kuch log achanak plan cancel kar dete hain aur kuch bagair bataye extra doston ko sath le aate hain. Is liye mehmanon ki sahi tadaad hamesha vague (ghair wazih) hoti hai.
* **How much will your guests eat and drink? Will it be a hot day, with everybody drinking a lot? Will your guests be hungry? You need to guess the demand for food and drink based on experiences from previous parties as well as weather, time of day, and other variables:** Mehman kitna khayenge aur peeyenge? Kya us din sakht garmi hogi aur sab bohot ziada drinks peeyenge? Kya mehman bohot bhookay honge? Aap ko sirf pichli parties ke experience, mausam, party ke waqt aur doosri cheezon ko dekh kar ek andaza (guess) lagana padta hai.

---

Solving the equation is a challenge because there are many unknowns. Being a good host, you’ll order more food and drink than needed so no guest will be hungry or thirsty for long. It may cost you more money than necessary, and you may end up wasting some of it, but this possible waste is the risk you must take to ensure you have enough for unexpected guests and circumstances.

Is maslay ko hal karna bohot mushkil hai kyunke bohot saari cheezon ka pehle se pata nahi hota (unknowns). Ek accha host hone ke naatay aap kya karte hain? Aap zaroorat se ziada khana aur drinks khareed lete hain taake koi bhi mehman bhooka ya pyasa na rahe. Is se aap ka kharcha ziada hota hai aur ho sakta hai ke kuch khana zaya (waste) bhi ho jaye, lekin mehmanon ki achhi khatir-daari ke liye aap ko yeh extra kharche aur waste ka risk lena padta hai.

---

Before the cloud, the same was true for our industry when planning the capacity of our IT infrastructure. Planning to meet future demands for your IT infrastructure was nearly impossible. To prevent a supply gap, you needed to add extra capacity on top of the planned demand to prevent running short of resources. When procuring hardware for a data center, we always had to buy hardware based on the demands of the future. We faced the following uncertainties when making these decisions:

Cloud computing se pehle, traditional IT infrastructure (Physical Data Centers) mein bhi bilkul aisa hi hota tha. Apne IT infrastructure ki future capacity (hardware ki zaroorat) ko plan karna lagbhag namumkin tha.

System ko down hone ya crash hone se bachane ke liye (supply gap ko rokne ke liye), companies ko apni zaroorat se kahin ziada extra physical hardware pehle se khareed kar rakhna padta tha taake resources kam na par jayein. Data center ke liye hardware khareedte waqt in 3 bari uncertainties (shukook o shubaat) ka samna karna padta tha:

* **How many users need to be served by the infrastructure?** Infrastructure kitne users ko handle karega? Future mein traffic kitna barhega?
* **How much storage would the users need?** Users ko data store karne ke liye kitni hard disk drive space (storage) chahiye hogi?
* **How much computing power would be required to handle their requests?** User ki requests ko process karne ke liye kitne CPUs aur RAM (computing power) zaroori honge?

---

To avoid supply gaps, we had to order more or faster hardware than needed, causing unnecessary expenses.

In tamam uncertainties aur supply gaps se bachne ke liye, companies ko zaroorat se bohot ziada aur behad mehangay physical servers pehle se khareedne padte thay. Is wajah se lakhon dollars ke fuzool kharche (unnecessary expenses) hote thay aur hardware data center mein bekar para rehta tha.

---

On AWS, you can use services on demand. Planning capacity is less and less important. You can scale from one EC2 instance to thousands of EC2 instances. Storage can grow from gigabytes to petabytes. You can scale on demand, thus replacing capacity planning. AWS calls the ability to scale on demand elasticity.

AWS Cloud par aap tamam services ko **on-demand** (jab zaroorat ho tab) istemal karte hain. Ab pehle se baith kar saalon ki capacity planning karne ki zaroorat khatam ho chuki hai.

* Aap 1 single EC2 instance se shuru kar ke zaroorat padne par **hazaron EC2 instances** tak ja sakte hain.
* Aap ki Storage **Gigabytes (GB)** se barh kar **Petabytes (PB)** tak khud-ba-khud phail sakti hai.
* Aap zaroorat ke waqt seconds mein resources ko barha aur ghatasakte hain. AWS cloud ki is khilone ki tarah phailne aur sikudne ki salahiyat ko **Elasticity** (khinchne ya elastick hone ki salahiyat) kaha jata hai.

---

Public cloud providers like AWS can offer the needed capacity with a short waiting time. AWS serves more than a million customers, and at that scale, it isn’t a problem to provide you with 100 additional virtual machines within minutes if you suddenly need them. This allows you to address another problem: recurring traffic patterns, as shown in figure 17.1. Think about the load on your infrastructure during the day versus at night, on a weekday versus the weekend, or before Christmas versus the rest of year. Wouldn’t it be nice if you could add capacity when traffic grows and remove capacity when traffic shrinks? That’s what this chapter is all about.

AWS jaise public cloud providers bohot thode waqt mein aap ko har kisam ki capacity de dete hain. AWS ke paas 1 Million (10 Lakh) se ziada active customers hain. Itne bare scale par hone ki wajah se, agar aap ko achanak 100 extra virtual machines chahiye hon, toh AWS aap ko chand mintos mein woh saare servers ready kar ke de deta hai.

Is elasticity ki wajah se ek bohot bara masla hal hota hai: **Recurring Traffic Patterns** (bar bar dohrane wale traffic ke patterns).

Apne system par traffic ka load sochein:

1. Din ke waqt (jab log active hon) vs Raat ke waqt (jab log so rahe hon).
2. Karobari dinon (Weekdays) vs Hafte ke aakhri dinon (Weekends).
3. Salana tehvaron (jaise Christmas/Black Friday) vs Baqi saal ka aam traffic.

Kya yeh zabardast baat nahi hogi ke jab traffic barhe toh khud-ba-khud naye servers add ho jayein, aur jab traffic kam ho toh extra servers delete ho jayein taake paise bachein? Is chapter mein hum wahi seekhenge.

---

## Figure 17.1 Typical traffic patterns for a web shop

Is figure mein ek online shopping store (web shop) ke 3 alag alag recurring traffic patterns ko 3 graphs ke zariye samjhaya gaya hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

1. **Pehla Graph (6 a.m., 12 p.m., 6 p.m. - Daily Pattern):** Is graph mein din ke 24 ghanton ka traffic load dikhaya gaya hai. Subah 6 baje (6 a.m.) system load sab se kam (bottom level) par hota hai kyunke aksar log so rahe hote hain. Dopahar 12 baje traffic barhna shuru hota hai aur Shaam 6 baje (6 p.m.) peak (sab se unchai) par pahunch jata hai jab log kaam se wapas aakar online shopping karte hain.
2. **Doosra Graph (Monday, Thursday, Sunday - Weekly Pattern):** Is graph mein hafte ke dinon ka load dikhaya gaya hai. Monday ko kaam shuru hote hi traffic barhta hai, hafte ke darmiyan (Thursday ke qareeb) peak par rehta hai, aur Sunday tak aate aate log ghoomne phirne nikal jate hain toh system load dubara kam ho jata hai.
3. **Teesra Graph (January to December - Yearly/Seasonal Pattern):** Is graph mein poore saal ka load dikhaya gaya hai. January se November tak traffic bilkul normal aur flat rehta hai. Lekin jaise hi December aata hai (Holiday season aur Christmas shopping ki wajah se), system load achanak aasman ko choone lagta hai (huge traffic spike).

---

Scaling the number of virtual machines is possible with Auto Scaling groups (ASG) and scaling policies on AWS. Autoscaling is part of the EC2 service and helps you scale the number of EC2 instances you need to fulfill the current load of your system. We introduced Auto Scaling groups in chapter 13 to ensure that a single virtual machine was running even if an outage of an entire data center occurred.

AWS par virtual machines (EC2 instances) ko kam ya ziada karna **Auto Scaling groups (ASG)** aur **Scaling Policies** ke zariye hota hai. Auto scaling EC2 service ka hi ek hissa hai jo aap ke system ke current workload ko dekh kar servers ki tadaad ko control karta hai.

Chapter 13 mein hum ne ASG ka istemal High Availability ke liye kiya tha taake agar poora ek data center (Availability Zone) down bhi ho jaye, tab bhi kam se kam ek virtual machine zinda rahe.

---

In this chapter, you’ll learn how to manage a fleet of EC2 instances and adapt the size of the fleet depending on the current use of the infrastructure. To do so, you will use the concepts that you learned about in chapters 14 and 15 and enhance your setup with automatic scaling as follows:

Is chapter mein aap EC2 instances ki poori **Fleet** (fauj/group) ko manage karna aur load ke mutabiq us fleet ka size chota ya bara karna seekhenge. Is ke liye aap Chapter 14 aur 15 ke concepts ko le kar un par Auto Scaling lagayein ge:

* **Using Auto Scaling groups to launch multiple virtual machines of the same kind as you did in chapters 13 and 14:** Ek jaise multiple virtual machines ka group launch karne ke liye Auto Scaling groups ka istemal karna.
* **Changing the number of virtual machines based on CPU load with the help of CloudWatch alarms, which is a new concept we are introducing in this chapter:** CPU utilization (load) ke mutabiq servers ki tadaad kam ya ziada karna CloudWatch Alarms ki madad se (Yeh ek NAYA concept hai).
* **Changing the number of virtual machines based on a schedule to adapt to recurring traffic patterns—something you will learn about in this chapter:** Ek khas time table (Schedule) ke mutabiq servers ko scale karna taake daily/weekly traffic patterns ko handle kiya ja sake (Yeh bhi NAYA concept hai).
* **Using a load balancer as an entry point to the dynamic EC2 instance pool as you did in chapter 14:** Dynamic tarike se kam-ziada hone wale EC2 servers ke aage Load Balancer (ALB) ko entry point banana.
* **Using a queue to decouple the jobs from the dynamic EC2 instance pool, similar to what you learned in chapter 14:** Task/jobs ko EC2 instances se alag karne ke liye Queue (SQS) ka istemal karna.

---

## Examples are 100% covered by the Free Tier

The examples in this chapter are totally covered by the Free Tier. As long as you don’t run the examples longer than a few days, you won’t pay anything for it. Keep in mind that this applies only if you created a fresh AWS account for this book and there is nothing else going on in your AWS account. Try to complete the chapter within a few days, because you’ll clean up your account at the end of the chapter

Writer bilkul clear kar raha hai ke is chapter ki tamam practical misalein 100% AWS Free Tier ke andar aati hain:

* Agar aap in practicals ko sirf kuch dinon ke liye chalate hain aur baad mein delete kar dete hain, toh aap ko **ek rupeya bhi charge nahi hoga**.
* Yeh tabhi applicable hai jab aap ne is book ke liye bilkul **Fresh (Naya) AWS account** banaya ho aur us mein koi doosri heavy resources pehle se na chal rahi hon.
* Practical shuru karne ke baad is chapter ko 2-3 dinon mein khatam karein aur aakhir mein tamam resources ko **Clean Up (delete)** kar dein.

---

The following prerequisites are required to scale your application horizontally, which means increasing and decreasing the number of virtual machines based on the current workload:

Application ko **Horizontally Scale** karne ke liye (matlab workload ke hisab se servers ki tadaad ko badhana ya kam karna), 2 zaroori shartain (Prerequisites) poori honi chahiye:

* **The EC2 instances you want to scale need to be stateless. You can achieve stateless servers by storing data with the help of services like RDS (SQL database), DynamoDB (NoSQL database), EFS (network filesystem), or S3 (object store) instead of storing data on disks (instance store or EBS) that are available only to a single EC2 instance:** Jin EC2 instances ko aap ne scale karna hai, unka **Stateless** hona zaroori hai. Stateless server ka matlab hai ke server ke andar user ka koi permanent data, file ya session save na ho (kyunke agar woh server terminate ho gaya toh data zaya ho jayega). Data ko local disks (EBS ya Instance Store) par rakhne ki bajaye external AWS services par rakha jata hai, jaise:
* **RDS** (SQL Database for structured data)
* **DynamoDB** (Fast NoSQL Key-Value Database)
* **EFS** (Network File System jo sab servers aapas mein share kar sakein)
* **S3** (Object Storage files aur images ke liye)


* **An entry point to the dynamic EC2 instance pool is needed to distribute the workload across multiple EC2 instances. EC2 instances can be decoupled synchronously with a load balancer or asynchronously with a queue:** Dynamic EC2 instance pool (jo continuously kam aur ziada ho rahe hain) ke aage ek central entry point ka hona zaroori hai jo aane wale tamaam kaam ko saare servers par barabar baante. Yeh kaam 2 tarike se hota hai:
* **Synchronous Decoupling:** Load Balancer (ALB) ke zariye real-time requests ko baanta jata hai.
* **Asynchronous Decoupling:** Queue (SQS) ke zariye background jobs ko baanta jata hai.

Hum ne Stateless Server ka concept Part 3 mein samjha tha aur Decoupling ko Chapter 13 mein detail se dekha tha. Is chapter mein hum stateless servers ke concept ko practically apply karenge aur synchronous (ALB) aur asynchronous (SQS) dono tarah ki decoupling ki hands-on examples par kaam karenge.


---