# Architecting on AWS

## AWS Ka Bunyaadi Usool: "Everything Fails All The Time"

Amazon.com ke CTO **Werner Vogels** ka ek mashhoor qoul (quote) hai:

> **“Everything fails all the time.”** (Har cheez har waqt kharab ho sakti hai.)

AWS ka falsafa yeh nahi hai ke ek aisa system banaya jaye jo KABHI na tute (unbreakable system)—kyun ke aisa system banana practically na-mumkin hai. Is ke bajaye AWS pehle se hi tayyar rehta hai ke agar koi cheez kharab ho jaye, toh usay kaise sambhala jaye (**Plan for failure**).

AWS neechay diye gaye 3 tarikon se pehle se failure ke liye tayyari karta hai:

* **Hard Drives Fail Ho Sakti Hain:** Hard disk kisi bhi waqt kharab ho sakti hai, is liye AWS ki **S3** service aap ka data ek hi waqt mein **multiple hard drives** par store karti hai taake data kabhi zaya na ho.
* **Computing Hardware Fail Ho Sakta Hai:** Physical computer ya server kharab ho sakta hai, is liye virtual machines (EC2) ko zaroorat padne par **automatically kisi doosre computer par restart** kar diya jata hai.
* **Data Centers Fail Ho Sakte Hain:** Poori ki poori building ya data center mein bijli ya internet ka masla ho sakta hai, is liye har AWS region ke andar **multiple data centers (Availability Zones)** hotay hain jinhein ek sath (parallel) ya zaroorat ke hisab se istemal kiya ja sakta hai.

---

### Downtime Ka Nuksan Aur AWS Solution

IT infrastructure aur applications ka band hona (Outage) karoobar (business) ke liye bohot bara khatra hai, kyun ke is se logon ka **aitebar (trust) aur paise dono doob jaate hain**.

AWS services failure ko 3 alag tarikon se handle karti hain:

1. Kuch services background mein **by default** failure ko khud sambhal leti hain.
2. Kuch services mein failure scenario ka response **on demand** milta hai.
3. Kuch services khud failure handle nahi kartin, lekin humein aisa infrastructure banane ke **tools** deti hain jisse hum failure ke khilaf plan kar sakein.

AWS ka pehla main principle **Designing for Failure** hai, aur doosra main principle cloud ki **Elasticity** (lacheelapan) ka istemal karna hai—yani load ke mutabiq servers ki tadaad kam ya zyada karna.

---

### Table 1: Overview of services and their failure-handling possibilities

Neechay di gayi table AWS services aur un ke failure-handling ke tarikon ko zahir karti hai:

| Feature | Description (Roman Urdu) | Examples |
| --- | --- | --- |
| **Fault tolerant** | Services baghair kisi downtime ke khud ba khud failure se recover kar sakti hain. | S3 (object storage), DynamoDB (NoSQL database), Route 53 (DNS) |
| **Highly available** | Services thore se downtime ke sath kuch failures se automatically recover kar sakti hain. | RDS (relational database), EBS (network attached storage) |
| **Manual failure handling** | Services by default failure se recover nahi hotin lekin unke upar highly available infrastructure banane ke liye tools faraham karti hain. | EC2 (virtual machine) |

#### Table Ka Aasan Breakdown (Bacho Ki Tarah Samjhein):

* **Fault Tolerant (S3, DynamoDB, Route 53):** Is ka matlab hai ke piche chahe jitni marzi hard drives ya computers kharab ho jayein, **user ko ek second ka bhi pata nahi chalega aur kaam rukey bager chalta rahega** (Zero Downtime).
* **Highly Available (RDS, EBS):** Is ka matlab hai ke agar koi failure aaye, toh system thode se chote se waqt (kuch seconds ya minutes) ke liye ruk sakta hai, lekin **khud ba khud repair ho kar wapas chal padta hai**.
* **Manual Failure Handling (EC2):** Virtual Machine (EC2) akele khud ko kharab hone se nahi bacha sakti. Lekin AWS aap ko aise tools deta hai jinhein jodh kar aap EC2 ko bhi Highly Available bana sakte hain.

---

## Upcoming Chapters Overview (Interconnecting Roadmap)

Aage aane waale chapters mein hum seekhenge ke ek aisa system kaise banayein jo kisi ek server ya poore data center ke band hone par bhi chalta rahe:

* **Chapter 13 (Single Instance Recovery Foundation):** Is chapter mein hum ek single EC2 instance ko usi data center mein ya kisi doosre data center mein recover karne ki bunyaad seekhenge taake hum single server loss se azaad ho sakein.
* **Chapter 14 (Decoupling Architecture):** System ke alag alag hisson ko aapas mein azaad (decouple) karna seekhenge:
* **Synchronous Decoupling:** Load Balancers ke zariye traffic ko barabar taqseem karna.
* **Asynchronous Decoupling:** **Amazon SQS** (Distributed Queuing Service) ka istemal karke fault-tolerant message queue system banana.


* **Chapter 15 (Zero-Downtime Deployment Automation):** Code ko Bina kisi downtime ke automatically deploy karne ka tarika seekhenge. Yeh Highly Available systems banane ki pehli shart hai.
* **Chapter 16 (Fault-Tolerant Web App Architecture):** Seekhe gaye tamam concepts ko mila kar EC2 instances par chalne waali ek poori Fault-Tolerant web application design karenge.
* **Chapter 17 (Elasticity & Auto Scaling):** System ke live workload aur traffic ke hisab se ya fixed schedule ke mutabiq server capacity ko khud ba khud kam ya zyada karna seekhenge.
* **Chapter 18 (Containers on AWS):** Docker aur containers ke zariye nayi aur purani applications ko AWS par chalane ke tamam options explore karenge.