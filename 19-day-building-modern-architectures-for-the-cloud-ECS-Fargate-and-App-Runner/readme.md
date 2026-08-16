# Building modern architectures for the cloud: ECS, Fargate, and App Runner

## **This chapter covers**

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

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

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


## **Comparing different options to run containers on AWS**

AWS par containers chalane ke liye bohot se raste hain, lekin sab se seedha aur asan tareeqa **AWS App Runner** hai. App Runner ek aisi service hai jahan aapko load balancers, virtual servers, ya complex networking ki fikar karne ki zaroorat nahi hoti. Sirf container image ka link do, aur AWS aapke liye sab kuch configure karke ek live URL faraham kar deta hai.

Agar aapki local machine par AWS CLI configured nahi hai, toh use pehle set up karna zaroori hai taake terminal se AWS commands chalaye ja sakein.

---

**Listing 18.1 Creating an App Runner service**

```bash
aws apprunner create-service \
  --service-name simple \
  --source-configuration '{"ImageRepository": \
  {"ImageIdentifier": "public.ecr.aws/s5r5alt5/simple:latest", \
  "ImageRepositoryType": "ECR_PUBLIC"}}'
```

* `aws apprunner create-service`: Yeh CLI ka main command hai jo App Runner ke andar aik nayi service create karne ka hukum deta hai.
* `--service-name simple`: Service ka naam `simple` rakha gaya hai taake AWS console ya CLI mein isay asaani se pehchana ja sakay.
* `--source-configuration`: Yeh parameter App Runner ko batata hai ke container kahan se uthana hai.
* `ImageIdentifier`: Container registry ka mukammal path hai (`public.ecr.aws/s5r5alt5/simple:latest`), jo Amazon ECR Public Gallery par mojood ek simple web server image ko point kar raha hai.
* `ImageRepositoryType`: Iski value `"ECR_PUBLIC"` set ki gayi hai, jo yeh wazeh karti hai ke image Amazon ECR ke public repository mein parri hai aur isay download karne ke liye kisi private password ya secret credentials ki zaroorat nahi hai.



---

Command run hone ke baad takreeban 5 minute lagte hain service ko tayyar hone mein. Uske baad hum service ka status aur URL check karne ke liye Listing 18.2 wala command chalate hain.

**Listing 18.2 Fetching information about App Runner services**

```bash
$ aws apprunner list-services
{
  "ServiceSummaryList": [
    {
      "ServiceName": "simple",
      "ServiceId": "5e7ffd09c13d4d6189e9bb51fc0f230",
      "ServiceArn": "arn:aws:apprunner:us-east-1:...", 
      "ServiceUrl": "bxjsdpnnaz.us-east-1.awsapprunner.com", 
      "CreatedAt": "2022-01-07T20:26:48+01:00",
      "UpdatedAt": "2022-01-07T20:26:48+01:00",
      "Status": "RUNNING" 
    }
  ]
}
```

* `aws apprunner list-services`: Is account aur region mein mojood tamam App Runner services ki summary nikalta hai.
* `"ServiceName": "simple"`: Service ka wahi naam jo humne create karte waqt rakha tha.
* `"ServiceId"`: AWS ki taraf se assign kardah unique identifier.
* `"ServiceArn"`: Amazon Resource Name (ARN) — AWS resource ka unique pata, jo service ko delete ya update karte waqt kaam aata hai.
* `"ServiceUrl"`: Default domain name jo AWS ne generate karke diya. Is URL ko browser mein open karke aap apni live running application dekh sakte hain. App Runner custom domains (maslan `example.com`) ko bhi mukammal support karta hai.
* `"Status": "RUNNING"`: Yeh zahir karta hai ke background mein infrastructure create ho chuka hai, container active hai, aur requests receive karne ke liye tayyar hai.

---

**Figure 18.2 ka Jaiza**

**Figure 18.2** App Runner ke flow aur architecture ko wazeh karti hai:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

* **Users to App Runner:** Bahir se internet ke users `HTTPS` ke zariye App Runner ko access karte hain. Yeh request automated tareeqay se load balancer se hoti hui container tak pohnachti hai.
* **App Runner to Backend / AWS Services:** Container ke andar chalne wali web application internet ke zariye doosri AWS services jese **Amazon DynamoDB** (database), **Amazon S3** (file storage), ya kisi bhi **Public Endpoint / API** ke sath connect ho sakti hai.
* **Platform as a Service (PaaS):** App Runner developer ko infrastructure management se azaad kar deta hai. Aap sirf application ka container image faraham karte hain, aur App Runner khud yeh kaam karta hai:
* Container ko start karna aur uski health monitor karna.
* Users ki aane wali requests ko active containers ke darmiyan barabar baantna (Load Balancing).
* Agar traffic barh jaye toh mazeed containers start karna (Auto-scaling) aur load kam hone par containers kam kar dena.



---

**Pricing Model (Bachat aur Kharcha Kaise Calculate Hota Hai?)**

App Runner ka pricing model bohot dilchasp hai: **Jab container koi request process nahi kar raha hota (idle time), toh CPU ka kharcha $0 hota hai — aap sirf memory (RAM) ka pay karte hain**. Jab koi user request bhejta hai, sirf tab CPU active hoti hai aur uska kharcha shuru hota hai.

Misal ke taur par, ek application jo sirf subah 9 AM se sham 5 PM tak (8 hours per day) use hoti hai, aur baqi 16 ghante bilkul farigh rehti hai:

* Minimum configuration: **1 vCPU** aur **2 GB RAM**.
* **Active Hours (8 ghante/din — 30 din):**
* 1 vCPU: $\$0.064 \times 8 \times 30 = \$15.36$ mahana
* 2 GB Memory: $2 \times \$0.007 \times 8 \times 30 = \$3.36$ mahana


* **Inactive Hours (16 ghante/din — 30 din — CPU bilkul free):**
* 2 GB Memory: $2 \times \$0.007 \times 16 \times 30 = \$6.72$ mahana


* **Total Mahana Kharcha:** $\$15.36 + \$3.36 + \$6.72 = \mathbf{\$25.44}$ mahana.

---

**Listing 18.3 Deleting an App Runner service**

Kharchay se bachne ke liye kaam khatam hote hi service ko delete karna zaroori hai.

```bash
aws apprunner delete-service \
  --service-arn $ServiceArn

```

* `aws apprunner delete-service`: Service aur uske sath attached load balancing infrastructure ko khatam karne ka command.
* `--service-arn $ServiceArn`: `$ServiceArn` ki jagah wo mukammal ARN likhein jo Listing 18.2 ke output mein mila tha.

---

**App Runner ki Limitations**

App Runner itna asaan hone ke bawajood har jagah fit nahi baithta:

* **SLA (Service Level Agreement):** Launch ke waqt iske paas enterprise-grade financial uptime commitment (SLA) mojood nahi thi.
* **High-Traffic Par Mehnga:** Choti aur low-traffic applications ke liye App Runner bohot sasta hai. Lekin agar aapki application par musalsal lakhon requests aa rahi hain aur CPU 24 ghante busy rehta hai, toh yeh doosre tareeqon (jaise ECS ya EKS) ke muqablay mein kafi mehnga parh sakta hai.

---

**Amazon ECS vs Amazon EKS**

AWS par containers run karne ke do baray orchestrators hain: **Amazon Elastic Container Service (ECS)** aur **Amazon Elastic Kubernetes Service (EKS)**.

**WHAT IS KUBERNETES?**

Kubernetes (jise **K8s** bhi kaha jata hai) ek open-source system hai jo hazaron containers ko automatically deploy, scale, aur manage karta hai. Isay Google ne banaya tha aur aaj kal **CNCF (Cloud Native Computing Foundation)** isay maintain karti hai. Yeh local machine, private data center, aur public clouds har jagah chal sakta hai.

Dono tools ka core maqsad aik hi hai:

* Agar koi container crash ho jaye, toh naya container start karna.
* Naya code bina downtime ke deploy karna.
* Traffic ke hisab se containers ki tadaad ko kam ya zyada karna.

---

**Table 18.1 Launch configuration parameters**

| Category | ECS | EKS |
| --- | --- | --- |
| **Portability** | ECS AWS par dastiyab hai. ECS Anywhere on-premises workloads ke liye ECS istemal karne ka ek extension hai. Doosre cloud providers ECS ko support nahi karte. | EKS AWS par dastiyab hai. On-premises workloads ke liye, aap ke paas EKS Anywhere hai, jo AWS ki taraf se supported hai lekin uske liye VMware vSphere ki zaroorat hoti hai aur yeh option deta hai ke aap Kubernetes ko khud deploy aur manage karein. Iske ilawa, zyadatar doosre cloud providers ke paas bhi Kubernetes ki offering hoti hai. |
| **License** | Proprietary service hai lekin bilkul muft (free of charge) hai. | Open source license hai (Apache License 2.0). |
| **Ecosystem** | Bohat saari AWS services (misal ke taur par ALB, IAM, aur VPC) ke sath bohat behtareen tareeqay se kaam karta hai. | Aik vibrant open source ecosystem ke sath ata hai (misal ke taur par Prometheus, Helm). AWS services ke sath integration mojood hai lekin hamesha mukammal mature nahi hoti. |
| **Costs** | Cluster muft hota hai. Lazmi baat hai, aap compute infrastructure ke pese ada karte hain. | AWS har cluster ke liye taqreeban $72 mahana charge karta hai. Iske ilawa, AWS sifarish karta hai ke aisay workloads ko jo isolation chahte hain, ek hi cluster par deploy na karein. Iske oopar, aap compute infrastructure ke bhi pese de rahe hote hain. |

---

**Writer ka Faisla (Author's Preference)**

Agarche Kubernetes developers mein bohot mashhoor hai, writer zyada tar workloads ke liye **Amazon ECS** ko tarjeeh deta hai:

* ECS ka apna cluster management bilkul **free** hai, jabke EKS mein har cluster ke alag paise dene parte hain.
* ECS AWS ki baqi services (IAM roles, CloudWatch, ALB) ke sath seedha aur behtareen integrate hota hai.
* AWS CloudFormation ke sath ECS ki integration mukammal aur robust hai, jisse Infrastructure as Code likhna intehayi aasan ho jata hai.


---