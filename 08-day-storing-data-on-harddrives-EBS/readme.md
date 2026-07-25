# Storing data on hard drives: EBS and instance store

Aap ke diye gaye text ki mukammal, step-by-step aur aasan Roman Urdu mein deep explanation niche di gayi hai, taake har theoretical aspect aur detail aap ke liye hamesha ke liye clear ho jaye.

---

Aap farz karein ke aap ko ek purana enterprise application (legacy application) AWS cloud par shift (migrate) karna hai jo abhi aap ke apne office ke physical servers (on-premises) par chal raha hai.

Purane zamane ke ziada tar applications files ko read aur write karne ke liye aik **Filesystem** ka istemal karte hain. In purane apps ko direct **Object Storage** (jaise AWS S3) par shift karna hamesha aasan ya mumkin nahi hota, kyunki is ke liye poore application ka code dubara likhna parta hai jo bohot mehenga (expensive) kaam hai.

Isi maslay ke hal ke liye AWS aap ko **Block-level storage** deta hai. Is ka sab se bara faida yeh hai ke aap bina kisi mehengi app-modification ke apna legacy application wese hi AWS par chala sakte hain jaise woh pehle chal raha tha.

---

### What is Block-Level Storage and Filesystem?

Block-level storage ko samajhne ke liye pehle in bunyadi chizon ko samajhna zaroori hai:

* **Disk Filesystem (FAT32, NTFS, ext3, ext4, XFS):** Yeh bilkul aap ke personal computer ki tarah kaam karta hai. Filesystem ka kaam yeh hisab rakhna hota hai ke aap ki konsi file storage mein kis jagah (kis block address par) mehfooz hai.
* **Block:** Block bytes ka ek chota sa sequence (tukda) hota hai. Yeh storage ki sab se choti **addressable unit** hoti hai (yani storage ki sab se choti jagah jiska apna ek khas address hota hai).
* **Operating System (OS) ka Role:** OS ek middleman (aadhat/raabta karwane wale) ka kaam karta hai. Yeh us application (jise file chahiye) aur neeche majood Filesystem/Block Storage ke darmiyan saara len-den sambhaltay hai.
* **Important Condition:** Aap block-level storage ko sirf aur sirf ek **EC2 Instance** ke sath jod kar use kar sakte hain, jahan aap ka OS chal raha ho.

---

### How OS handles Read Requests (Step-by-Step Flow)

Jab bhi koi application block-level storage se file read karti hai, toh woh OS ke **System Calls** (`open`, `write`, `read`) ka istemal karti hai. Aayein read request ka step-by-step flow samajhte hain:

1. **Step 1 (Application Call):** Application ko koi file (maslan `/path/to/file.txt`) parhni hoti hai, toh woh OS ko ek **Read System Call** bhejti hai.
2. **Step 2 (OS Forwarding):** OS is request ko pakadta hai aur aage **Filesystem** ko paas kar deta hai.
3. **Step 3 (Filesystem Translation):** Filesystem us file ke path (`/path/to/file.txt`) ko dekhta hai aur usay translate karke batata hai ke disk ke **kis Block Address par** yeh data para hua hai. Wahan se data parh kar app ko de diya jata hai.

#### Databases (jaise MySQL) ko Block Storage kyun chahiye?

Databases jaise MySQL data ko hamesha ke liye save (persistence) rakhne ke liye inhi low-level system calls ka istemal karti hain. Aap MySQL ko yeh nahi keh sakte ke "tum apna data S3 (Object Storage) mein daal do", kyunki MySQL direct system calls ke zariye files ko access karti hai, is liye databases ke liye Block-Level Storage laazmi hai.

---

> ### ⚠️ Not all examples are covered by the Free Tier
> 
> 
> * Is chapter ki tamam examples AWS Free Tier mein shamil nahi hain. Jab bhi koi aisa kaam aayega jiske paise lagenge, wahan **Warning Message** nazar aayega.
> * **Ghabranay ki zaroorat nahi:** Agar aap in examples ko ziada din tak chalne nahi denge aur 1–2 din mein khatam kar lenge, toh aap ko koi paise (cost) nahi dene parenge.
> * **Condition:** Yeh sirf tabhi apply hota hai jab aap ne is book ke liye bilkul fresh AWS account banaya ho aur us mein koi aur extra cheez na chal rahi ho.
> * **Best Practice:** Is chapter ke saare practicals kuch dino mein khatam karein aur aakhir mein apne account ke saare resources delete (clean-up) kar dein.
> 
> 

---

### Two Kinds of AWS Block-Level Storage

AWS aap ko do mukhtalif qisam ke block-level storage options deta hai:

```
                  ┌──────────────────────────────────────────┐
                  │          AWS Block-Level Storage         │
                  └─────────────────────┬────────────────────┘
                                        │
             ┌──────────────────────────┴──────────────────────────┐
             ▼                                                     ▼
┌───────────────────────────┐                         ┌───────────────────────────┐
│ Persistent Network Volume │                         │  Temporary Host-Attached  │
│        (e.g., EBS)        │                         │  (e.g., Instance Store)   │
├───────────────────────────┤                         ├───────────────────────────┤
│ • Connected via Network   │                         │ • Physically Attached     │
│ • Independent Lifecycle   │                         │ • Extremely High Speed    │
│ • Auto-Replicated Data    │                         │ • Data lost on shutdown   │
└───────────────────────────┘                         └───────────────────────────┘

```

#### 1. Persistent Block-Level Storage (Network Connected)

* **Kaise connect hota hai?** Yeh storage volume aap ke EC2 Virtual Machine se **Network** ke zariye jurha hota hai (Jaise ek Network Hard Drive / AWS EBS).
* **Sab se Behtar Choice Kyun Hai?** Ziada tar maslo ke liye yeh best choice hai kyunki yeh aap ke Virtual Machine (EC2) ki zindagi (life cycle) par depend nahi karta. Agar aap ki VM band ya delete bhi ho jaye, tab bhi aap ka data mehfooz rehta hai.
* **Durability aur Availability:** AWS aap ke data ko automatically ek se ziada physical disks par copy (replicate) karta rehta hai taake agar ek disk kharab bhi ho jaye, toh aap ka data zaya na ho.

#### 2. Temporary Block-Level Storage (Physically Attached)

* **Kaise connect hota hai?** Yeh storage volume direct us physical host machine ke sath lagaya hua hota hai jis par aap ki Virtual Machine chal rahi hoti hai (Isay AWS Instance Store kehte hain).
* **Kyun Istemal Karein?** Yeh sirf tab kaam aata hai jab aap ko **Extreme Performance** chahiye ho. Direct physical attachment ki wajah se is mein latency (delay) bohot kam milti hai aur data transfer ki speed (throughput) bohot ziada hoti hai.
* **Trade-off (Bara Nuqsan):** Yeh temporary hota hai. Agar aap ka Virtual Machine stop ya terminate hua, toh is disk par majood saara data hamesha ke liye khatam ho jayega.

---

### Aage Hum Kya Parhenge?

Agle teen sections mein writer humein inhi dono solutions ko deeply sikhayega:

1. Storage ko EC2 Instance ke sath connect karna.
2. Dono ki Performance testing karna.
3. Data ka Backup lene ke tareeqay explore karna.

---