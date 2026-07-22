# Storing your objects: S3

## Data Storage Ke Do Bade Tachalish (Two Challenges)

Data ko sambhal kar rakhna (store karna) aasan kaam nahi hai. Is mein do bohot badi mushkilaat aati hain:

* **Ever-increasing volumes of data (Data ka lagataar barhna):**
* **Bacho ki tarah samjhein:** Farz karein aap ke paas khilono ka ek chota dabba hai. Roz aap naya khilona khareedte hain. Ek din dabba poora bhar jayega! Ab naye khilonay rakhne ke liye dabba chota par jayega. Exactly waise hi, kisi ek computer ya server ki hard disk limited hoti hai. Roz aane wali videos, photos aur files itni zyada hoti hain ke ek machine ki disk bhar jati hai.


* **Ensuring durability (Data ko hamesha ke liye safe rakhna):**
* **Bacho ki tarah samjhein:** Agar aap ne apni sari zaroori drawings usi ek dabbe mein rakhi hain, aur wo dabba paani mein gir jaye ya usay aag lag jaye, toh sab kuch hamesha ke liye zaya ho jayega. Single machine par data rakhne se agar wo hard disk kharab ho jaye, toh data wapas nahi milta. Isay hum **Single Point of Failure** kehte hain.



### Distributed Data Store: Revolutionary Solution

In dono mushkilaat ko hal karne ke liye writer ek naya tareeqa batata hai: **Distributed Data Store**.

* Ek akeli machine ke bajaye, hum hazaron computers ko aapas mein network ke zariye jod dete hain.
* **Unlimited Storage:** Jab data barhta hai, hum network mein mazeed machines add kar dete hain. Space kabhi khatam nahi hoti.
* **High Durability (Data Loss ka khatma):** Aap ki file kisi ek computer par nahi, balkay alag alag machines par copy karke rakhi jati hai. Agar ek computer kharab bhi ho jaye, toh aap ka data doosre computers par safe rehta hai.

---

## Amazon S3 Ka Ta'aruf (Introduction to Amazon S3)

AWS humein **Amazon S3 (Simple Storage Service)** deta hai, jo ek **fully managed, distributed object store** hai.

* **Fully Managed ka matlab:** Aap ko peeche wale servers, disks, ya hardware ko khud setup ya repair nahi karna parta—AWS sab kuch khud sambhalta hai.
* **Kiya store kar sakte hain?** Images, videos, PDF documents, installation files (.exe), backup files, wagera.
* **Writer ke bataye gaye main Use Cases:**
1. **Data Backups:** Apne zaroori data ki copy safe rakhna.
2. **Low-cost Archiving:** Purana data jo roz istemal nahi hota (jaise saalon purane records), usay kam qeemat par lambe arse ke liye store karna.
3. **User-Generated Content:** Applications mein users jo photos/videos upload karte hain unhe sambhalna.
4. **Static Website Hosting:** Direct S3 se bina kisi EC2 server ke HTML, CSS aur JavaScript wali websites chalana.



---

## Not all examples are covered by the Free Tier

Writer yahan ek zaroori maali (financial) hidayat de raha hai:

* **Free Tier ki Hadaayat:** AWS ka ek free plan hota hai, lekin is chapter ki tamam practical examples free nahi hain. Kuch cheezon par chote-mote charges lag sakte hain.
* **Cost Warning System:** Jab bhi koi aisi example aayegi jiss se paise kat sakte hon, writer pehle se ek **Warning Message** dikhaye ga.
* **Paisa Bachane ka Tareeqa:** Agar aap instructions ko sahi tarah follow karein aur kisi bhi practical ko khatam karne ke baad resources ko zyada din tak chalne na dein (delete kar dein), toh aap par koi extra charges nahi aayenge.

---

## What is an object store?

### Purana Tarika (Traditional Hierarchy / File System) vs Object Store

* **Traditional File System (Purana Tarika):** Pehle hum data ko folders aur sub-folders ke andar rakhte thay (jaise `C:\Documents\Photos\2026\pic.jpg`). Lekin jab billions of files ho jayein, toh folders ke andar file dhoondna system ko slow kar deta hai.
* **Object Store (Naya Tarika):** Object Store mein koi traditional folder tree nahi hota. Yahan har data item ko ek **Object** bana kar ek flat space mein daal diya jata hai.

---

## Object Ke 3 Main Hissay (Components)

Writer ke mutabiq, Object Store mein har Object ke **3 hissey** hote hain:

1. **Globally Unique Identifier (GUID / Key):** Object ka unique naam ya address.
2. **Metadata:** Object ke baare mein maloomat (Data about data).
3. **Data itself:** Actual file content.

---

## Figure 7.1 Ka Step-by-Step Breakdown

Writer ne **Figure 7.1** mein Object ke structure ko do hisson mein divider karke samjhaya hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

### 1. Globally Unique Identifier (GUID / Key)

* Yeh object ka unique name ya path hota hai.
* Is key ke zariye aap network par kisi bhi machine se is specific object ko dhoond sakte hain.
* *Example (Figure 7.1 ke mutabiq):* `/awsinaction/img/cloud.png`

### 2. Metadata (Data About Data)

Metadata ka matlab hai file ki apni maloomat ya properties. Writer ne is ki yeh misalein di hain:

* **Date of last modification / Creation date:** File kab bani ya update hui (e.g., `2015-01-01`).
* **Object size:** File ka size kitna hai (e.g., `20 KB`).
* **Object's owner / Access Control:** File ka malik kaun hai aur kis ke paas parhne/dekhne ki permission hai (e.g., `public read`).
* **Object's content type / File type:** Data ki kisam kya hai (e.g., `image/png`). Is se browser ko pata chalta hai ke file ko kaise open karna hai.
* **Tags:** Searching aur organization ke liye lagaye gaye labels (e.g., `cloud, nature`).

### 3. Data (Actual Content)

* Yeh wo asal file hoti hai jo aap save kar rahe hain.
* Is mein HTML/CSS code, images, videos, JSON documents, executable programs (.exe), ya koi bhi raw binary data (Blob) ho sakta hai.

---

## Metadata Request Ka Bada Faida (HEAD Request Concept)

Writer ek bohot aham architectural advantage batata hai:

* **Concept:** Aap poori file (Data) ko download kiye bina, **sirf uska Metadata request kar sakte hain**.
* **Bacho ki tarah samjhein:** Farz karein aap ke paas 5 GB ki ek bari video file S3 par parhi hai. Aap sirf yeh check karna chahte hain ke file kitni bari hai ya aakhri baar kab change hui thi.
* **Faida:** Agar aap ko metadata ke liye bhi poori 5 GB file download karni pare, toh aap ka internet aur waqt zaya hoga. Object store mein aap bina main file download kiye sirf 1 KB ka metadata mangwa kar saari info hasil kar sakte hain. Is se bandwidth bachti hai aur speed bohot fast ho jati hai.

---

## 2026 Modern AWS S3 Context (Updated Architecture)

Modern AWS S3 environment mein in concepts se judi chand zaroori baatein:

* **Strong Consistency:** S3 ab **Strong Read-After-Write Consistency** provide karta hai. Yani jaise hi aap koi naya object write ya update karte hain, usi millisecond mein har reader ko updated data hi milta hai.
* **Automatic Encryption:** Modern S3 buckets mein tamaam naye objects server-side encryption (`SSE-S3`) se automatically encrypted hote hain.
* **Performance & Classes:** High performance analytics ke liye ab *S3 Express One Zone* (single-digit millisecond speed) aur ultra-low-cost archiving ke liye *S3 Glacier Deep Archive* jaise modern storage classes available hain.

---

## Amazon S3

AWS S3 cloud computing ki dunya ka sab se mashhoor aur bunyadi pillar hai. Aayein is pure text ko choti se choti bareeki ke sath step-by-step aur bacho ki tarah aasan karke samajhte hain.

---

### Amazon S3 Kya Hai?

* **Full Form / Acronym:** S3 ka matlab hai **S**imple **S**torage **S**ervice (Teen 'S' hone ki wajah se isay **S3** kaha jata hai).
* **Sab Se Purani Service:** Yeh AWS ki sab se pehli aur purani services mein se ek hai jo 2006 mein launch hui thi.
* **Distributed Web Service:** Yeh ek aisi storage service hai jo internet ke zariye chalne wali **HTTPS APIs** par kaam karti hai. Is mein aap ka data kisi ek computer par nahi, balkay hazaron distributed servers par rakha jata hai.

> **Bacho ki tarah samjhein:**
> Amazon S3 ko cloud ke andar ek **"Jadui Digital Tijori" (Digital Vault)** samjhein. Jaise aap apne ghar ke khilonay dabba mein daal kar rakh dete hain, waise hi aap apni saari digital files (photos, videos, code) is tijori mein daal sakte hain. Internet ke zariye aap duniya ke kisi bhi kone se is tijori ko khol kar apna data nikal ya daal sakte hain.

---

### Writer Ki Di Gayi Real-World Examples (Use Cases)

Writer ne S3 ke **4 aham real-world istemal (use cases)** bataye hain:

1. **Static Website Content Deliver Karna:**
* **Writer ki Example:** Writer batata hai ke unka apna blog (`[https://cloudonaut.io](https://cloudonaut.io)`) S3 par hosted hai.
* **Asaan Samjh:** S3 par aap HTML, CSS, JavaScript, aur images rakh kar poori ki poori static website chala sakte hain. Is ke liye aap ko kisi mehnge EC2 server ki zaroorat nahi parti.


2. **Data Ka Backup Rakhna:**
* **Writer ki Example:** Aap apne personal computer ki photo library ko **AWS CLI** (Command Line Interface) ki madad se S3 par safely upload/backup kar sakte hain.


3. **Data Lakes (Analytics Ke Liye Structured Data Store Karna):**
* **Writer ki Example:** System performance benchmark ke jo JSON result files hote hain, unhe S3 par store karke analytics tools ke zariye analysis kiya ja sakta hai.


4. **User-Generated Content Store Karna:**
* **Writer ki Example:** Writer ne ek web application banayi—**AWS SDK** ki madad se—jo users ki upload ki hui files ko direct S3 par store karti hai. (Jaise Facebook ya WhatsApp par jab aap photo upload karte hain).



---

### Storage Limits, Performance, aur Pricing Rules

Writer S3 ke kuch bohot aham technical rules aur costs ke baare mein batata hai:

* **Virtually Unlimited Storage Space:**
* S3 mein kul (total) kitna data rakha ja sakta hai? Is ki koi limit nahi hai! Aap Petabytes ya Exabytes data bhi store kar sakte hain.


* **Single Object Size Limit (5 TB Rule):**
* S3 mein aap unlimited data rakh sakte hain, *lekin* **ek single file (object) ka maximum size 5 TB (Terabytes)** se zyada nahi ho sakta. Agar koi file 5 TB se badi ho, toh usay ek baar mein upload nahi kiya ja sakta.


* **High Availability & Durability:**
* S3 aap ke data ko ek se zyada data centers (Availability Zones) mein copy karke rakhta hai, taake agar koi ek server jal bhi jaye, toh aap ka data hamesha safe rahe (99.999999999% durability).



#### Pricing (S3 Ka Bill Kaise Banta Hai?)

Writer batata hai ke S3 bilkul free nahi hai, is mein 3 cheezon ke paise kat-te hain:

1. **Per GB Storage:** Jitne Gigabytes data aap S3 mein mahine bhar ke liye rakhte hain.
2. **Per Request Cost:** Data par hone wale har action ke paise (e.g., File upload karna = `PUT` request, file download karna = `GET` request).
3. **Data Transfer Out:** S3 se internet par data bahar nikalne (download karne) ki bandwidth cost.

---

### S3 Ko Access Karne Ke 4 Main Tareeqay

Aap S3 ke sath 4 different tariqon se communicate kar sakte hain:

* **1. AWS Management Console:** Browser ke andar AWS ka visual dashboard.
* **2. AWS CLI (Command Line Interface):** Terminal ya Command Prompt par commands likh kar.
* **3. AWS SDKs:** Programming languages (Python, Node.js, Java, C#) ke code ke andar se.
* **4. Third-Party Tools:** Desktop applications (jaise Cyberduck ya CloudBerry).

---

### Figure 7.2 Ka Step-by-Step Breakdown

Writer ne **Figure 7.2** mein S3 ke sath data transfer ka basic flow dikhaya hai:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

* **Figure Flow:**
`User` $\longleftrightarrow$ `Upload/download an object` $\longleftrightarrow$ `Internet` $\longleftrightarrow$ `Amazon S3`
* **Explanation:**
* User chahe Management Console use kare, CLI, ya koi app—har file transfer **Internet** ke zariye hoti hai.
* Security ke liye yeh poora communication **HTTPS (SSL/TLS Encryption)** protocol ke zariye hota hai, taake raste mein koi aap ka data steal ya read na kar sake.



---

### What is an S3 Bucket? (Grouping Objects)

Writer ab S3 ke main organizational unit **Bucket** ko samjhata hai.

* **Bucket Kya Hai?** S3 mein objects ko seedha aise hi khula nahi rakha jata. Objects ko group karne aur organize karne ke liye pehle ek container banaya jata hai jise **Bucket** kehte hain.
* **Bacho ki tarah samjhein:**
Bucket ko ek **"Bada Dabba" ya "Shopping Bag"** samjhein. Jab aap bazar se items khareedte hain, toh pehle ek bag lete hain aur phir sara saman us bag ke andar daalte hain. Bag = Bucket, aur saman = Objects.

```
+-------------------------------------------------------------+
|                     AMAZON S3 BUCKET                        |
|                  (Name: awsinaction)                        |
|                                                             |
|   +-----------------------+     +-----------------------+   |
|   | Object 1              |     | Object 2              |   |
|   | Key: img/cloud.png    |     | Key: docs/file.pdf    |   |
|   | Metadata: 20 KB       |     | Metadata: 1 MB        |   |
|   | Data: [ Image ]       |     | Data: [ PDF Document ]|   |
|   +-----------------------+     +-----------------------+   |
+-------------------------------------------------------------+

```

#### Globally Unique Name Rule (Bohot Aham Design Decision)

Writer ek bohot aham shart batata hai: **Bucket ka naam Globally Unique hona zaroori hai.**

* **Globally Unique ka matlab:** Aap jo bucket name select karenge, wo poore AWS network mein (duniya ke kisi bhi AWS account ya kisi bhi region mein) pehle se kisi aur ne use na kiya ho.
* **Bacho ki tarah samjhein:** Jaise dunya mein har insaan ka Passport Number ya CNIC unique hota hai, ya Instagram par ek username ek hi bande ko milta hai—waise hi S3 Bucket ka naam poori dunya mein sirf ek hi ho sakta hai.
* **Wajah (Architectural Reason):** S3 bucket ko internet par ek web address (`[https://bucket-name.s3.amazonaws.com](https://bucket-name.s3.amazonaws.com)`) milta hai. Kyun ke internet par do alag alag websites ka address same nahi ho sakta, is liye bucket name ka unique hona lazmi hai.

---

### Figure 7.3 Ka Step-by-Step Breakdown

Writer ne **Figure 7.3** mein Bucket aur us ke andar parhe hue Objects ke rishte ko visualize kiya hai:

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

* **Figure Flow Breakdown:**
* **Outer Container (The Bucket):** Ek bada dabba hai jis ka unique naam rakha gaya hai: `awsinaction`.
* **Inner Contents (The Objects):** Is bucket ke andar multiple objects stacked hain.
* **Object Internal Architecture:** Figure mein ek object ko khol kar dikhaya gaya hai jis ki key `/img/cloud.png` hai. Us ke do parts hain:
1. **Metadata Side:** Object ki properties jaise `public read` permission, `image/png` type, `cloud,nature` tags, `20 KB` size, aur `2015-01-01` date.
2. **Data Side:** Content itself (Aasman aur badalon wali actual image file).





---

### 2026 Modern AWS S3 Architecture Standards

Modern cloud architecture ke mutabiq in points par dhyan dena zaroori hai:

* **Block Public Access (By Default On):** Modern S3 buckets banate waqt AWS security ke liye **Block Public Access** ko default taur par On rakhta hai, taake tiyaari mein koi private data publically leak na ho jaye.
* **Access Control Lists (ACLs) vs Bucket Policies:** Figure 7.3 mein metadata ke andar `public read` (ACL) dikhaya gaya hai. Modern 2026 practice mein ACLs ko disable kar diya jata hai aur permissions ko manage karne ke liye **S3 Bucket Policies** aur **IAM Policies** ka istemal kiya jata hai.
* **Default Encryption:** Ab har naya S3 bucket server-side encryption (**SSE-S3**) se automatically encrypt hota hai bina kisi extra cost ya manual setup ke.

---