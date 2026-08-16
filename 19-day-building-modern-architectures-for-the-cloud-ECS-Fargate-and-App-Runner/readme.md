# Building modern architectures for the cloud: ECS, Fargate, and App Runner

**This chapter covers**

* **Deploying a web server with App Runner, the simplest way to run containers on AWS:** AWS par containers run karne ka sab se asaan tareeqa AWS App Runner hai. Isme aapko na servers ki fikar karni parti hai, na complex networking ki — sirf container do aur application live.
* **Comparing Elastic Container Service (ECS) and Elastic Kubernetes Service (EKS):** AWS par containers manage karne ke do baray orchestrators hain — ECS (AWS ka apna simple aur tightly-integrated tool) aur EKS (industry-standard Kubernetes ka managed version). In dono ka aapas mein farq aur use-case samjhaya jayega.
* **An introduction into ECS: cluster, task definition, task, and service:** Amazon ECS ke bunyadi building blocks ka breakdown:
* **Cluster:** Wo virtual ground ya boundary jahan aapke containers chalte hain.
* **Task Definition:** Ek blueprint ya recipe jismein likha hota hai ke container ko kitni CPU, memory, aur kaunsi image chahiye.
* **Task:** Us recipe par amal karke chalne wala actual running container instance.
* **Service:** Ek manager jo 24/7 dekhta hai ke aapke matlooba tadaad mein tasks theek se chal rahe hain ya nahi, aur crash hone par naya task start karta hai.


* **Running containers with Fargate without the need for managing virtual machines:** AWS Fargate ek serverless compute engine hai. Iska matlab aapko EC2 virtual machines create, patch, ya manage nahi karni partin; AWS background mein compute power khud sambhalta hai aur aap sirf apne container ke chalne ka pay karte hain.
* **Building a modern architecture based on ALB, ECS, Fargate, and S3:** Ek mukammal modern cloud architecture tayyar karna jismein traffic Application Load Balancer (ALB) sambhalay ga, containerized backend ECS aur Fargate par chalega, aur static files S3 par store hongi.

---

Writer batata hai ke jab wo clients ke sath consulting karte hain, toh do tarah ke projects aate hain:

* **Brownfield projects:** Ye wo projects hain jahan client ka purana setup (legacy system) pehle se company ke apne office ya data center (on-premises) mein chal raha hota hai. Maqsad pehle us system ko cloud par shift (migrate) karna hota hai, aur phir waqt ke sath usay modern banana hota hai. Iski misal aisi hai jaise ek purane bane banaye ghar ko renovate karna.
* **Greenfield projects:** Ye wo projects hain jahan sab kuch bilkul sifar (scratch) se shuru kiya jata hai aur cloud ki latest aur modern technologies ka istemal karke naya solution develop kiya jata hai. Iski misal ek khali plot par modern ghar tameer karne jaisi hai.

Containers aisi zabardast technology hain jo Brownfield aur Greenfield dono qism ke projects mein fit baithti hain. Purane applications ko containerize karke aasani se modernize kiya ja sakta hai, aur naye applications shuru se hi containers par build kiye ja sakte hain.

> **Docker Basics Note:** Writer yahan containers ko AWS par modern tareeqay se chalane par focus kar raha hai. Container image banane ya local machine par Docker chalane ki bunyadi basics ko book mein skip kiya gaya hai, jiske liye unhon ne *Docker in Action* ka hawala diya hai.

---

**Examples not covered by Free Tier**

* Is chapter ke practical examples AWS Free Tier ke tehat poori tarah cover nahi hote.
* In resources ko AWS par deploy karne par kharcha aam tor par **$1 per day** se bhi kam hota hai.
* Har example ke aakhir mein aur chapter ke end par tamam resources ko delete karne ka tareeqa diya gaya hai. Kharchay se bachne ke liye behtar yahi hai ke practicals ko kuch hi dino ke andar mukammal karke resources clean up kar diye jayein.

**Chapter requirements**

Is chapter ko samajhne ke liye darj zail cheezon ki bunyadi samajh honi chahiye:

* Containers ke andar software chalana (Docker ki basic samajh).
* **Amazon Simple Storage Service (S3):** Cloud par object data/files store karne ka tariqa.
* **Elastic Load Balancing (ELB):** Aane wali web traffic ko mukhtalif instances ya containers par barabar taqseem karna.
* **AWS CloudFormation:** Infrastructure as Code (IaC) ke zariye automated tareeqay se template files se poora cloud infrastructure khara karna.

---

**Why should you consider containers instead of virtual machines?**

Virtual machines (VMs) aur containers dono ka bunyadi maqsad ek jaisa hai: isolated environment mein software chalana. Isliye agar aapko EC2 virtual machines ka pehle se pata hai, toh container ko samajhna bilkul aasan hai.

**Figure 18.1 ka Jaiza**

**Figure 18.1** dono approaches ke darmiyan similarity ko high level par wazeh karti hai:

* **Left Side (Virtual Machine Flow):** Hum ek **AMI (Amazon Machine Image)** letay hain aur `Run instance` ka command de kar ek **EC2 instance** khara kar letay hain. AMI ek master photocopy ya snapshot ki tarah hoti hai jismein Operating System aur zaroori configuration saved hoti hai.
* **Right Side (Container Flow):** Hum ek **Container image** letay hain aur `Run container` command de kar ek **Container** chala letay hain.

Dono methods ek pre-built image se hi start hote hain. Mental model ke tor par aap containers ko ek **Lightweight Virtual Machine** samajh sakte hain — jo VM ki tarah bhari bharkam Operating System ka load nahi leti balke bohot tezi se start hoti hai aur kam memory leti hai.

**"Works on my machine" ka Masla aur Docker ka Kirdar**

Developers ke darmiyan aksar ek purana masla rehta hai: *"Ye application mere laptop par bilkul theek chal raha hai, pata nahi production server par kyun phat gaya!"*

Aisa is liye hota hai kyunki developer ke laptop par libraries, environment variables, dependencies aur runtime version alag hote hain, jabke server par halaat mukhtalif hote hain.

2013 mein Docker ne container concept ko aam banaya. Real world mein jaise shipping containers hotay hain — ek lohay ka standard box jismein chahe kapray hon, car ho ya electronics, us box ka size standard hota hai aur wo aaram se ship, train ya truck par fit ho jata hai. Isi tarah software development mein:

* Container ek standardized unit ban jata hai jismein aapka application code, uski tamam libraries, binaries aur configs aik sath pack ho jati hain.
* Ye standardize package **Continuous Deployment (CD)** ko aasan banata hai, jahan har naya code change baghair kisi kharabi ke automatically test aur production environments par deploy ho jata hai.

**Portability aur uski Asal Limits**

Nazriyati (theoretical) tor par, aap ek hi container image ko:

1. Apne local laptop par chala sakte hain.
2. Apni company ke on-premises data center mein chala sakte hain.
3. AWS jese public cloud provider par chala sakte hain.

Iske baraks, EC2 ki AMI ko apne local laptop par chalana intehayi mushkil aur complex hota hai. Lekin containers mein bhi do ahem hudood (boundaries) hoti hain:

* **OS Boundary:** Linux container sirf Linux host (kernel) par chalega, aur Windows container Windows par.
* **CPU Architecture Boundary:** Agar container image Intel/AMD (x86_64) processor ke liye bani hai, toh wo ARM chips (jaise AWS Graviton ya Apple Silicon) par directly bina emulation ke us tarah behave nahi karegi.

> **Vendor Marketing vs Reality:** Cloud vendors aksar dawat dete hain ke "container ko uthao aur kisi bhi cloud par le jao baghair kisi badlao ke." Haqeeqat mein container image zaroor portable hoti hai, lekin poore system ko doosre cloud ke network, IAM permissions, database aur monitoring systems ke sath integrate karne mein kafi mehnat darkar hoti hai.

**Immutable Servers ka Tasawwur**

Containers ek behtareen engineering practice ko lazmi banate hain jise **Immutable Server** kaha jata hai.

* **Mutable Server (Purana tareeqa):** Ek server launch kiya, jab koi bug theek karna ho ya library update karni ho toh live chalte hue server ke andar login (SSH) kiya aur wahan ja kar changes kar diye. Isse waqt ke sath system un-predictable ho jata hai.
* **Immutable Server (Modern tareeqa):** Ek aesa server jisko launch karne ke baad kabhi tabdeel (modify) nahi kiya jata. Agar koi chota sa code change ya update karna hai, toh:
1. Purani chal rahi cheez ko modify nahi karte.
2. Nayi image build ki jati hai.
3. Nayi image se naye servers/containers launch kiye jate hain.
4. Purane containers ko band (destroy) kar diya jata hai.



Containers mein aam tor par chalte hue container mein login karke changes karne ki ijazat nahi hoti (aur na hi ye recommend kiya jata hai). Isliye containers by-design aapko immutable server model par chalne par majboor karte hain.

Is poore process ki bunyad **Dockerfile** hoti hai — ek aisi configuration file jismein step-by-step instructions (recipe) likhi hoti hain ke container image ko kaise prepare aur build karna hai.


---
