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


## Managing a dynamic EC2 instance pool

Tasavvur karein ke aap ko ek aisi web application (jaise blog ki website) chalani hai jo traffic ke hisab se khud ko kam ya ziada kar sake. Jab website par visitors/requests ki tadaad barhne lage, toh system ko khud-ba-khud bilkul ek jaise naye virtual machines (EC2 instances) chalu (launch) karne chahiye. Aur jab visitors kam ho jayein, toh fuzool chalne wale virtual machines ko khud-ba-khud band (terminate) kar dena chahiye.

Is poore kaam ko bagair kisi insaan ke (automated tareeqay se) chalane ke liye, app ki tamam configuration aur deployment **Bootstrapping** ke dauran honi chahiye. Bootstrapping ka matlab hai jab naya server pehli baar on hota hai, toh woh khud hi saara zaroori software aur code install kar ke ready ho jaye, bina kisi engineer ke button dabaye.

Is section mein aap sab se pehle ek **Auto Scaling group (ASG)** banana seekhenge. Phir aap dekhenge ke Scheduled Actions (khas waqt ke mutabiq) ke zariye EC2 instances ki tadaad kaise badli jati hai. Is ke baad CloudWatch metrics (jaise CPU utilization) ko dekh kar automatically scale karna seekhenge.

Auto Scaling group aap ko dynamic EC2 pool ko 2 tareeqon se manage karne ki ijazat deta hai:

* **Dynamically adjust the number of virtual machines that are running:** Chalne wale virtual machines ki tadaad ko load ke hisab se khud-ba-khud kam ya ziada karna.
* **Launch, configure, and deploy uniform virtual machines:** Bilkul ek jaise (uniform) virtual machines ko launch, configure, aur un par app deploy karna.

Auto Scaling group hamesha aap ki tay karda limits (bounds) ke andar hi phailta aur sikudta hai:

1. **Minimum 2 Virtual Machines:** Kam az kam 2 servers set karne se yeh faida hota hai ke agar ek Data Center (Availability Zone) mein koi kharabi ya bijli ka outage aa jaye, tab bhi doosre Data Center mein doosra server chal raha hoga (Fault Tolerance).
2. **Maximum Virtual Machines:** Ziada se ziada limit set karne se yeh faida hota hai ke aap ka cloud ka bill aap ke budget se bahar na nikal jaye.

Autoscaling ke **3 mukhya hissey (Parts)** hote hain:

* **A launch template that defines the size, image, and configuration of virtual machines:** Ek Launch Template jo naye banne wale virtual machines ka size, Operating System (Image), aur configuration (blueprint) tay karti hai.
* **An Auto Scaling group that specifies how many virtual machines need to be running based on the launch template:** Ek Auto Scaling group jo yeh hisab rakhta hai ke Launch Template ke zariye kitne virtual machines active rehne chahiye.
* **Scaling plans that adjust the desired number of EC2 instances in the Auto Scaling group based on a plan or dynamically:** Scaling Plans jo time-table ya live load ke hisab se Auto Scaling group ke andar desired servers ki tadaad ko badalte hain.

Agar aap chahte hain ke multiple EC2 instances mil kar kaam karein, toh yeh zaroori hai ke har naya server bilkul ek jaisa (identical/homogeneous) ho. Naye servers ka blueprint banane ke liye hum **Launch Template** ka istemal karte hain.

---

## Figure 17.2 Autoscaling consists of an Auto Scaling group and a launch template, launching and terminating uniform virtual machines.

Is figure mein Autoscaling ke poore architecture aur working mechanism ko samjhaya gaya hai:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

1. **Top Box (Dynamic Pool & Limits):** Autoscaling EC2 instances ka ek dynamic pool define karti hai. Aap is mein minimum, maximum, aur desired number of virtual machines set karte hain.
2. **Auto Scaling group:** Yeh group instances ki ginti ko manage karta hai. Jab naye server ki zaroorat hoti hai, toh yeh Launch Template ko signal bhejta hai ki *"Naya Virtual Machine Launch Karo"*.
3. **Launch Template (Blueprint):** Yeh virtual machine ka naksha (blueprint) hai. Is nakshe ko dekh kar naye exact identical (ek jaise) EC2 instances launch hote hain.
4. **Health Monitoring & Termination:** Auto Scaling group har waqt servers ki health check karta rehta hai (EC2 State ya Load Balancer ke zariye). Agar koi server kharab (unhealthy) ho jaye ya traffic kam ho jaye, toh Auto Scaling group us server ko terminate (delete) kar deta hai.
5. **Bottom Note (Stateless & Decoupled):** Yeh saari EC2 fleet (servers) hamesha **Stateless** (jahan data save na ho) aur **Decoupled** (ek doosre se alag) honi chahiye taake kisi ek server ke aane ya jaane se application par koi bura asar na pare.

---

### Table 17.1 Launch template parameters

| Name | Description (Roman Urdu) | Possible values (Roman Urdu) |
| --- | --- | --- |
| **ImageId** | Woh image (OS) jis se virtual machine shuru ki jati hai. | Amazon Machine Image (AMI) ki ID (jaise `ami-028f2b5ee08012131`). |
| **InstanceType** | Nayi virtual machines ka size aur hardware capacity. | Instance type (jaise ke `t2.micro` ya modern `t3.micro`). |
| **UserData** | Virtual machine ke liye user data jo bootstrapping (startup) ke dauran script execute karne ke liye istemal hota hai. | BASE64-encoded string ya plain bash script. |
| **NetworkInterfaces** | Virtual machine ke network interfaces ko configure karta hai. Sab se ahem baat yeh hai ke yeh parameter aap ko instance ke sath public IP address attach karne ki ijazat deta hai. | Network interface configurations ki list (Security groups aur Public IP settings). |
| **IamInstanceProfile** | Ek IAM role ke sath linked IAM instance profile attach karta hai taake server doosri AWS services se secure baat kar sake. | IAM instance profile ka naam ya Amazon Resource Name (ARN, yani ek ID). |

---

Launch template banane ke baad, aap ek Auto Scaling group banate hain jo is template ka hawala (reference) deta hai. Auto Scaling group mein 3 main numbers hote hain:

* **Desired Capacity:** Matlooba servers ki tadaad jo har waqt chalni chahiye. Agar chalne wale servers is number se kam honge, toh ASG naye servers add karega. Agar chalne wale servers is number se ziada honge, toh ASG extra servers ko terminate kar dega. Desired capacity ko aap manually, schedule par, ya load ke hisab se badal sakte hain.
* **Minimum Size (MinSize):** Sehna yogya sab se kam limit. ASG kabhi bhi servers ki tadaad ko is se neche nahi girne dega.
* **Maximum Size (MaxSize):** Oopri limit. ASG kabhi bhi servers ki tadaad ko is limit se aage nahi barhne dega.

Auto Scaling group yeh bhi dekhta rehta hai ke EC2 instances healthy (sahi salamat) hain ya nahi. Agar koi instance kharab ho jaye, toh ASG usay automatically delete kar ke naya instance launch kar deta hai.

---

### Table 17.2 Auto Scaling group parameters

| Name | Description (Roman Urdu) | Possible values (Roman Urdu) |
| --- | --- | --- |
| **DesiredCapacity** | Matlooba (desired) healthy virtual machines ki tadaad. | Integer (Koi bhi mukammal adad, jaise 2, 4). |
| **MaxSize** | Virtual machines ki maximum tadaad; oopri scaling limit (upper scaling limit). | Integer. |
| **MinSize** | Virtual machines ki minimum tadaad; nichli scaling limit (lower scaling limit). | Integer. |
| **HealthCheckType** | Auto Scaling group virtual machines ki health kis tarah check karti hai. | `EC2` (instance ki basic hardware/OS health) ya `ELB` (load balancer ke zariye application ke URL ki health check). |
| **HealthCheckGracePeriod** | Nayi instance launch hone ke baad health check ko us waqt tak pause rakha jata hai jab tak instance mukammal bootstrap na ho jaye. | Sekondon ki tadaad (Number of seconds, e.g., 300). |
| **LaunchTemplate** | Virtual machines shuru karte waqt blueprint ke tor par istemal hone wale launch template ki ID (`LaunchTemplateId`) aur version. | Launch template ki ID aur version. |
| **TargetGroupARNs** | Load balancer ke target groups, jahan autoscaling nayi instances ko khud ba khud register karti hai. | Target group ARNs ki list. |
| **VPCZoneIdentifier** | Subnets ki list jin mein EC2 instances launch karni hain. | Kisi VPC ke subnet identifiers ki list. |

Agar aap `VPCZoneIdentifier` mein ek se ziada subnets dete hain, toh Auto Scaling group naye EC2 instances ko un saare subnets (aur multiple Availability Zones) mein barabar baant (evenly distribute) kar ke launch karta hai taake high availability mile.

---

## Don’t forget to define a health check grace period

Agar aap apne Auto Scaling group ke liye ELB (Load Balancer) ka health check istemal kar rahe hain, toh zaroor **HealthCheckGracePeriod** bhi set karein.

**Yeh kyun zaroori hai?**
Jab naya EC2 instance launch hota hai, toh usay start hone, operating system load karne, aur UserData script ke zariye web server (jaise Apache HTTPD) install karne mein 1 se 2 minute ka waqt lagta hai. Agar aap grace period nahi denge, toh Load Balancer pehle hi second mein check karega, application ko "Down" payee ga, aur ASG us naye server ko useless samajh kar terminate kar dega!

Grace period wo waqt hai jab tak ASG naye server par health check failures ko ignore karta hai. Ek aam web application ke liye **5 minute (300 seconds)** ka grace period bilkul suitable hota hai.

---

## Listing 17.1 Auto Scaling group and launch template for a web app

Niche di gayi CloudFormation YAML template ek dynamic EC2 pool ko setup karti hai:

```yaml
LaunchTemplate:
  Type: 'AWS::EC2::LaunchTemplate'
  Properties:
    LaunchTemplateData:
      IamInstanceProfile:
        Name: !Ref InstanceProfile
      ImageId: 'ami-028f2b5ee08012131'
      InstanceType: 't2.micro'
      NetworkInterfaces:
        - AssociatePublicIpAddress: true
          DeviceIndex: 0
          Groups:
            - !Ref WebServerSecurityGroup
      UserData:
        'Fn::Base64': !Sub |
          #!/bin/bash -x
          yum -y install httpd
AutoScalingGroup:
  Type: 'AWS::AutoScaling::AutoScalingGroup'
  Properties:
    TargetGroupARNs:
      - !Ref LoadBalancerTargetGroup
    LaunchTemplate:
      LaunchTemplateId: !Ref LaunchTemplate
      Version: !GetAtt 'LaunchTemplate.LatestVersionNumber'
    MinSize: 2
    MaxSize: 4
    HealthCheckGracePeriod: 300
    HealthCheckType: ELB
    VPCZoneIdentifier:
      - !Ref SubnetA
      - !Ref SubnetB

```

### Template Code Details:

* `LaunchTemplate:` -> AWS CloudFormation mein Launch Template resource create karne ki shuruaat.
* `Type: 'AWS::EC2::LaunchTemplate'` -> AWS ko bataya ja raha hai ke yeh EC2 Launch Template ka resource hai.
* `Properties:` -> Template ki configurations aur settings ka section.
* `LaunchTemplateData:` -> Instance ke andar ki saari main details yahan di jati hain.
* `IamInstanceProfile:` -> Instance ke sath IAM profile jodne ka block.
* `Name: !Ref InstanceProfile` -> Ek pehle se bane hue `InstanceProfile` ka reference de raha hai taake EC2 ko AWS permissions mil sakain.
* `ImageId: 'ami-028f2b5ee08012131'` -> Amazon Machine Image ki specific ID jo bata rahi hai ke Operating System konsa load hoga.
* `InstanceType: 't2.micro'` -> Instance ka hardware size (1 vCPU, 1 GB RAM).
* `NetworkInterfaces:` -> Virtual Network Card ki settings.
* `- AssociatePublicIpAddress: true` -> Is baat ko yaqeen banata hai ke har naye server ko internet se direct access ke liye ek Public IP mile.
* `DeviceIndex: 0` -> Primary network card (eth0) ki position set karta hai.
* `Groups:` -> Security groups attach karne ki jagah.
* `- !Ref WebServerSecurityGroup` -> Web server ke Security Group ka reference de raha hai (jo HTTP/HTTPS firewall rules apply karta hai).
* `UserData:` -> Server boot hote hi chalne wali script ka block.
* `'Fn::Base64': !Sub |` -> UserData script ko Base64 format mein encode karta hai jaisa ke AWS EC2 ko darkaar hota hai.
* `#!/bin/bash -x` -> Linux script ka header jo commands ko debug mode (`-x`) ke sath execute karta hai.
* `yum -y install httpd` -> Apache Web Server (httpd) ko automatically bina kisi human prompt (`-y`) ke install karta hai.
* `AutoScalingGroup:` -> Auto Scaling Group resource ki shuruaat.
* `Type: 'AWS::AutoScaling::AutoScalingGroup'` -> AWS ko batata hai ke yeh Auto Scaling Group ka resource hai.
* `Properties:` -> ASG ki settings.
* `TargetGroupARNs:` -> Load Balancer ke target group ki list.
* `- !Ref LoadBalancerTargetGroup` -> Naye launch hone wale servers ko automatically Application Load Balancer ke Target Group mein register kar deta hai.
* `LaunchTemplate:` -> ASG ko batata hai ke naye servers kis blueprint se banane hain.
* `LaunchTemplateId: !Ref LaunchTemplate` -> Upar banaye gaye Launch Template ki ID ko link kar raha hai.
* `Version: !GetAtt 'LaunchTemplate.LatestVersionNumber'` -> Hamesha Launch Template ka sab se **latest version** istemal karne ke liye dynamic attribute retrieve karta hai.
* `MinSize: 2` -> ASG ko paband karta hai ke kam se kam 2 instances hamesha chalte rahein.
* `MaxSize: 4` -> ASG ko paband karta hai ke ziada se ziada 4 instances tak hi scaling ho sakti hai.
* `HealthCheckGracePeriod: 300` -> Server launch hone ke baad **300 seconds (5 minutes)** tak ELB health check failure par server ko terminate nahi hone deta taake bootstrapping (yum install httpd) poori ho sake.
* `HealthCheckType: ELB` -> Server ki health ka faisla EC2 ke sath sath Load Balancer ke HTTP response checks par karta hai.
* `VPCZoneIdentifier:` -> Subnets ki list jahan servers launch karne hain.
* `- !Ref SubnetA` -> Pehla Subnet (e.g., Availability Zone A).
* `- !Ref SubnetB` -> Doosra Subnet (e.g., Availability Zone B). Auto Scaling group naye servers ko in do subnets mein barabar baant kar launch karega.

Mukhtasir yeh ke Auto Scaling groups ek behad mufeed tool hain jab aap ko ek se ziada ek jaise virtual machines ko alag alag Availability Zones mein chalana ho. Is ke alawa, Auto Scaling group kisi bhi kharab ya fail ho jane wale EC2 instance ko automatically delete kar ke naya replacement server chalu kar deta hai.


---