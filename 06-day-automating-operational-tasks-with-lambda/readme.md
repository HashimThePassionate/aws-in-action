# Automating operational tasks with Lambda

Yeh text **AWS Lambda** aur **Serverless Architecture** ka ek bohat aasan aur mazeedarchhed (introduction) hai. Aayein is pooray text ke ek ek point, concept, aur real-world misaal ko bacho ki tarah bilkul aasan Roman Urdu mein samajhte hain:

---

Is section mein writer humein AWS ke sab se zabardast aur mashhoor tool se milwa raha hai, jiska naam hai **AWS Lambda**.

### AWS Lambda: Aap ki Toolbox Ka Swiss Army Knife

Writer Lambda ko **Swiss Army Knife** keh raha hai. Swiss Army knife dekhi hai na aap ne? Ek chota sa pocket knife jismein scissor, bottle opener, screwdriver aur chaku sab aik sath hote hain. Bilkul waise hi, AWS Lambda bhi cloud ki duniya ka aisa tool hai jo har chote-bade kaam ke liye fit ho jata hai.

#### 1. Server Ya Virtual Machine Ka Headache Khatam

Pehle jab bhi aap ko apna code (jaise Python ya JavaScript) chalana hota tha, toh aap ko ek poori **Virtual Machine (EC2 instance)** khareedni aur chalani parti thi. Us mein Operating System install karo, settings karo, phir code chalao.

Lekin **AWS Lambda** ke aane se aap ko kisi Virtual Machine ki bilkul zaroorat nahi rahi!

* **Execution Environments (Tayyar Mahool):** Lambda aap ko bani-banai jagah (environments) deta hai jahan aap apna code seedha chala sakte hain.
* **Supported Languages:** C#/.NET Core, Go, Java, JavaScript (Node.js), Python, aur Ruby. *(Aaj kal modern AWS mein aap custom runtimes aur Docker containers bhi chala sakte hain).*

#### 2. Aap Ko Bas 3 Chote Kaam Karne Hain:

1. **Function Likhna:** Apne kaam ka code (function) tayyar karein.
2. **Upload Karna:** Code ko AWS Lambda par upload kar dein.
3. **Configure Karna:** Yeh set karein ke code ko kitni memory (RAM) chahiye aur yeh kab chalay.

Is ke baad aap ka kaam khatam! Aap ka code ek **Fully Managed Computing Environment** mein chalega — matlab background mein server ko sambhalna, chalana, aur dekh-bhal karna poora AWS ka kaam hai, aap ka nahi.

---

### AWS Infrastructure Ko Automate Karna

AWS Lambda poore AWS system ke sath bohat gehre tarike se mila hua (integrated) hai. Is ka sab se bara fayda yeh hai ke aap apni infrastructure ke daily repetitive (baar baar hone wale) kaam automatic karwa sakte hain.

#### Writer Ki Real-World Examples:

Writer apni company mein Lambda ko do bare kaamon ke liye use karta hai:

1. **Container Cluster Scaling:** Unhon ne ek apna formula (custom algorithm) banaya hua hai. Jab container cluster par load barhta hai ya kam hota hai, toh Lambda automatically naye servers add ya remove kar deta hai.
2. **Log Files Processing:** System ke saare logs aur data files ko automatically read aur analyze karne ke liye Lambda function chalta hai.

---

### Zero Maintenance aur High Availability

Lambda aap ko ek aisa environment deta hai jahan aap ko koi maintenance (dekh-bhal) nahi karni parti aur yeh **Highly Available** hota hai (matlab kabhi band nahi hota).

* **No Security Updates:** Aap ko Linux ya Windows ke security patches install karne ki zaroorat nahi.
* **No Server Replacement:** Agar background ka koi server kharab ho jaye, toh AWS usay khud replace karta hai. Aap ko pata bhi nahi chalega.
* **No Remote Access Management:** Admin log-in ke liye SSH keys ya RDP (Remote Desktop) manage karne ka koi jhanjhat nahi.

---

### Billing Model: "Jitna Chalaoge, Sirf Usi Ka Paisa Dena Hai" (Pay-per-Invocation)

Pehle jab aap EC2 server lete the, toh chahe server par koi kaam ho raha ho ya wo khali (idle) baitha ho, aap ko 24 ghante ka kiraya dena parta tha.

* **Lambda Ka Tareeqa:** Lambda mein billing **Invocation** (jitni baar aap ka code call/trigger hua) aur **Execution Time** (jitne milliseconds tak code chala) par hoti hai.
* **Misaal:** Agar aap ka koi task din mein sirf **ek baar** chalta hai, toh aap sirf us ek baar chalne ke kuch milliseconds ka paisa denge. Baqi 23 ghante 59 minute khali baithne ka **ek rupya bhi nahi dena parega**.

---

### Is Chapter Mein Aagay Hum Kya Seekhein Ge? (Step-by-Step Roadmap)

1. **Pehli Real-World Example (Website Health Check):**
Aap AWS Management Console ka use karke ek chota sa Lambda function banayein ge jo regular waqfe ke baad aap ki website ko check karega ke wo chal rahi hai ya nahi.
2. **Doosri Real-World Example (Automated EC2 Tagging with CloudFormation):**
Aap Python code likhein ge aur **CloudFormation** (jo chapter 4 mein seekha tha) ke zariye automatically Lambda deploy karenge. Yeh function jaise hi koi naya EC2 instance banega, us par automatically **Tag** (label) laga dega.
3. **Aagay Ke Practical Use-Cases:**
Chapter ke aakhir mein aap seekhein ge ke Lambda se Web Applications, Internet of Things (IoT) backends, aur Big Data ko kaise process kiya jata hai.

---

### Examples are almost all covered by the Free Tier

* **Free Tier Coverage:** Is chapter ki taqriban saari examples **AWS Free Tier** mein bilkul FREE cover ho jati hain (AWS Lambda har mahine 1 Million requests free deta hai).
* **CloudTrail Exception:** Sirf ek jagah **AWS CloudTrail** ki wajah se shayad kuch cents (bohat hi choti raqam) lag sakti hain, agar aap ke account mein pehle se CloudTrail chal raha ho.
* **Clean-up Instructions:** Har section ke aakhir mein created resources ko delete/clean-up karne ka tareeqa bataya gaya hai taake aap ka koi faltu bill na aaye.

---


## Executing your code with AWS Lambda

Cloud mein computing power alag-alag layers (jaise pyaz ke chilke hote hain) mein milti hai, jinhein hum **layers of abstraction** kehte hain:

* **Virtual Machines (Amazon EC2):** Yeh sab se pehli layer hai jahan aap ko ek poora Virtual Computer aur Operating System (Linux ya Windows) milta hai.
* **Containers:** Yeh Virtual Machine ke upar ek aur layer hai, jahan aap apne app ko us ki saari settings ke sath pack karte hain (is ke baare mein chapter 18 mein detail se baat hogi).
* **Functions (AWS Lambda):** Yeh sab se choti aur barik layer (**fine-grained manner**) hai. Yahan aap ko koi poora computer ya container nahi sambhalna parta, balki yeh sirf chote-chote code snippet (functions) chalane ki jagah hai.

---

## What is serverless?

Jab aap AWS Lambda ke baare mein parhte hain, toh aap ko ek lafz baar baar sunne ko milta hai: **Serverless**.

Lekin kya "Serverless" ka matlab yeh hai ke background mein koi server hota hi nahi?

Peter Sbarski ne apni book *Serverless Architectures on AWS* mein is confusion ko bohat pyare tarike se samjhaya hai:

> […] word **serverless** thoda sa misnomer (galat fehmi paida karne wala naam) hai. Chahe aap AWS Lambda jaisa compute service use karein ya kisi API se baat karein, background mein servers ab bhi chal rahe hote hain. Farq sirf yeh hai ke yeh servers aap se **chupaye (hidden)** gaye hain. Aap ko infrastructure ke baare mein sochne ki zaroorat nahi hai aur na hi underlying operating system ko chhedne ka koi tarika hai. Koi aur (AWS) aap ki jagah infrastructure management ki tamam mushkil barikion ko sambhal raha hai, taake aap ka waqt doosre zaroori kaamon ke liye aazad ho sake.
> — Peter Sbarski

### Serverless System Ki 3 Buniyaadi Shartein:

Writer ke mutabaq koi bhi system tabhi **Serverless** kehlayega jab us mein yeh 3 batein hongi:

1. **No VM Management:** Aap ko Virtual Machines ko manage, update, ya maintain karne ki bilkul zaroorat na ho.
2. **Fully Managed Scalability & High Availability:** System khud hi load ke hisab se bara/chota ho sake (**Scalability**) aur kabhi band na ho (**High Availability**).
3. **Pay-per-Request & Consumption:** Bill sirf utna aaye jitni baar request aayi aur jitna CPU/Memory istemal hua. Idle (khali) baithne ka koi bill na ho.

AWS Lambda in teeno sharton par poora utarta hai. Is ke alawa market mein doosri companies ke bhi serverless products hain, jaise Google ka **Google Cloud Functions** aur Microsoft ka **Azure Functions**.

---

## Running your code on AWS Lambda

AWS Lambda, Amazon ke poore serverless platform ki sab se buniyaadi eent (basic building block) hai. Sab se pehla step yeh hai ke aap apne code ko apne server ke bajaye Lambda par chalana seekhein.

Writer ne **Figure 6.1** ke zariye code chalane ke 4 aasan steps dikhaye hain:

```
[1. Write Code] ──> [2. Upload Code + Dependencies] ──> [3. Create Function & Runtime] ──> [4. Execute in Cloud]

```

### Figure 6.1 Ka mukammal breakdown:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

* **Step 1: Implementing a function in C#, Go, Java, JavaScript, Python, or Ruby**
Sab se pehle aap apni pasand ki kisi bhi supported language mein apna function (code) likhte hain.
* **Step 2: Uploading source code and its dependencies (e.g., libraries or modules)**
Aap apne code file (jaise Python ki `.py` file) aur us ke sath jo extra libraries ya modules chahiye hote hain, unka `.zip` package bana kar upload karte hain.
* **Step 3: Creating a function determining the runtime environment**
AWS Lambda Console par ja kar function create karte hain aur batate hain ke yeh kis environment (e.g., Python 3.12 ya Node.js 20) par chalega.
* **Step 4: Executing source code in a scalable and highly available compute environment**
Aap apne function ko invoke (call) karte hain, aur AWS background mein automatically ek dam safe aur highly available environment mein aap ka code chala deta hai.

### AWS Lambda Ke Supported Runtimes:

Lambda filhal in languages ko natively support karta hai:

* **C# / .NET Core**
* **Go**
* **Java**
* **JavaScript / Node.js**
* **Python**
* **Ruby**

> **Custom Runtimes & Container Images:**
> Agar aap koi aisi language use karna chahte hain jo list mein nahi hai, toh aap **Custom Runtime** ya **Docker Container Image** la sakte hain. Lekin is se system thoda mushkil (complex) ho jata hai, is liye writer recommend karta hai ke shuru mein inhi official supported runtimes ka use karein.

---

## Comparing AWS Lambda with virtual machines (Amazon EC2)

Aayein dekhte hain ke AWS Lambda aur Traditional EC2 Virtual Machine mein kya farq hai:

1. **Granularity (Tukde Ka Size):** Virtual Machine (EC2) aap ko poora Operating System deti hai jahan aap ek se ziyada app chala sakte hain. Jabke Lambda aap ko sirf ek chote se **single function** ko chalane ki jagah deta hai.
2. **Zimmewari (Responsibility):** EC2 mein server ko secure rakhna, scale karna, aur band hone se bachana aap ki zimmedari hai. Lambda mein infrastructure ko production-ready rakhna poora AWS ki zimmedari hai.
3. **Billing Farq (Real-World Price Comparison):**
* EC2 chalne par har second/ghante ka bill aata hai, chahe koi user aaye ya na aaye.
* Lambda sirf us waqt ka bill leta hai jab code asal mein chal raha ho.



#### Writer Ki Real-World Example:

Maan lijiye aap ko har 5 minute baad apni website check karne ke liye ek script chalani hai (Website Health Check):

* **EC2 Instance Par:** Aap ko server 24 ghante, 7 din on rakhna parega. Iska kharcha taqriban **$3.71 per month** aayega.
* **AWS Lambda Par:** Lambda sirf har 5 minute baad kuch milliseconds ke liye jagega aur phir so jayega. Iska kharcha sirf taqriban **$0.04 per month** (sirf 4 cents!) aayega.

---

## Table 6.1 AWS Lambda compared to Amazon EC2

Pehlay writer ka diya gaya table dekhein, phir is ki ek ek feature ko aasan urdu mein samajhte hain:

| Comparison Feature | AWS Lambda | Amazon EC2 |
| --- | --- | --- |
| **Granularity of virtualization** | Small piece of code (a function). | An entire operating system. |
| **Scalability** | Scales automatically. A throttling limit prevents you from creating unwanted charges accidentally and can be increased by AWS support if needed. | As you will learn in chapter 17, using an Auto Scaling group allows you to scale the number of EC2 instances serving requests automatically, but configuring and monitoring the scaling activities is your responsibility. |
| **High availability** | Fault tolerant by default. The computing infrastructure spans multiple machines and data centers. | Virtual machines are not highly available by default. Nevertheless, as you will learn in chapter 13, it is possible to set up a highly available infrastructure based on EC2 instances as well. |
| **Maintenance effort** | Almost zero. You need only to configure your function. | You are responsible for maintaining all layers between your virtual machine’s operating system and your application’s runtime environment. |
| **Deployment effort** | Almost zero due to a well-defined API | Rolling out your application to a fleet of virtual machines is a challenge that requires tools and know-how. |
| **Pricing model** | Pay per request as well as execution time and allocated memory | Pay for operating hours of the virtual machines, billed per second |

---

### Table 6.1 Ka Aasan Breakdown:

* **1. Granularity of virtualization (Kaam ka size):**
* **Lambda:** Bohot chota code ka tukda (sirf 1 function).
* **EC2:** Ek poora ka poora Operating System (jaise aap ka apna computer).


* **2. Scalability (Load ke hisab se bara/chota hona):**
* **Lambda:** Yeh **automatically** scale hota hai. Agar 1 user aaye toh 1 function jagega, agar 10,000 users ek sath aa jayein toh Lambda khud 10,000 instances bana dega. Bill achanak bohot ziyada na badh jaye, is ke liye ek safety limit (**Throttling Limit**) hoti hai jise zaroorat parne par badhaya ja sakta hai.
* **EC2:** EC2 mein aap ko **Auto Scaling Group** khud set karna parta hai, rules khud likhne parte hain, aur khud monitor karna parta hai.


* **3. High availability (System ka crash hone se bachna):**
* **Lambda:** Yeh **by default fault-tolerant** hai. AWS is ke code ko alag-alag data centers (Availability Zones) aur alag-alag machines par khud phailata hai taake ek jagah kharab ho toh doosri jagah se chalta rahe.
* **EC2:** EC2 by-default highly available nahi hota. Agar wo single server kharab hua toh app band ho jayegi. Isay highly available banane ke liye aap ko multiple EC2 instances aur Load Balancer khud setup karna parta hai.


* **4. Maintenance effort (Dekh-bhal ki mehnat):**
* **Lambda:** Almost **Zero**. Aap ko sirf apne function ki settings configure karni hain.
* **EC2:** Bohot mehnat hai. Operating System ke patches, security updates, aur runtime dependencies ki zimmedari aap ki hai.


* **5. Deployment effort (Code ko upload karna):**
* **Lambda:** Almost **Zero**. Ek simple API call ya CLI command se naya code deploy ho jata hai.
* **EC2:** Bohot mushkil task hai. Ek se ziyada servers (fleet) par app deploy karne ke liye alag se deployment tools aur knowledge ki zaroorat hoti hai.


* **6. Pricing model (Paisa kis baat ka Dena hai):**
* **Lambda:** Sirf utni baar ka paisa jitni baar function call hua (**Pay per request**), jitna time chala (**Execution time**), aur jitni RAM select ki (**Allocated memory**).
* **EC2:** Server jitne ghante ya seconds on raha, utna kiraya Dena parega—chahe server par koi kaam ho raha ho ya wo khali baitha ho.


---