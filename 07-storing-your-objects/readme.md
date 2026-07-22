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