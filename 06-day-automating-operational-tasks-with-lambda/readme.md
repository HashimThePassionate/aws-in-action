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
