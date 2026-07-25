# Sharing data volumes between machines: EFS

## This chapter covers

Is chapter mein hum detail ke sath yeh baatein sikhenge:

* **High Availability wala Network Filesystem Banana:** Ek aisa storage banana jo kabhi down na ho aur multiple data centers mein replicate ho.
* **Network Filesystem ko Multiple EC2 Instances par Mount Karna:** Ek hi storage disk ko ek waqt mein bohot saare alag alag EC2 servers se jodna.
* **EC2 Instances ke Darmiyan Files Share Karna:** Alag alag servers ke darmiyan data ko real-time mein share karna.
* **Network Filesystem ki Performance Ko Improve (Tweak) Karna:** EFS ki speed aur response time ko optimize karna.
* **Network Bottlenecks ko Monitor Karna:** Yeh check karna ke kahin network speed ya bandwidth slow toh nahi ho rahi.
* **Shared Filesystem ka Backup Lena:** Apne poore shared storage ka secure backup maintain karna.

---

### Network Filesystem (EFS) Kya Hai Aur Yeh Kyun Zaroori Hai?

Aap farz karein ke aap ke paas ek purana application (legacy application) hai jo apni files ko kisi local drive (filesystem) par save karta hai.

* **Amazon S3 Kyun Nahi?** Hum ne Chapter 7 mein Object Storage (S3) parha tha. Lekin purane apps S3 ko direct samajh nahi paate. S3 use karne ke liye app ke poore code ko badalna parta hai, jo mehenga aur mushkil kaam hai.
* **EBS / Instance Store Kyun Nahi?** Chapter 8 mein hum ne Block Storage (EBS aur Instance Store) parha tha. EBS ka masla yeh hai ke woh sirf **ek waqt mein kisi ek EC2 server** ke sath hi lag sakta hai. Aur EBS sirf ek single data center (Availability Zone) mein rehta hai, jis wajeh se AWS iski uptime guarantee sirf **99.9%** deta hai.

Agar aap ko ek aisa storage chahiye jo **ek sath multiple EC2 servers** read/write kar sakein aur jo kabhi down bhi na ho, toh **Amazon Elastic File System (EFS)** sab se behtareen solution hai!

EFS **NFSv4.1** (Network File System version 4.1) protocol par kaam karta hai. Yeh aap ke data ko khud ba khud ek se ziada data centers (Availability Zones) mein copy (replicate) kar deta hai, jis wajeh se AWS iski uptime availability **99.99%** promise karta hai.

> **EFS WORKS ONLY WITH LINUX:** EFS filhal Windows EC2 instances ke sath kaam nahi karta. Agar aap Windows servers chalate hain, toh AWS aap ko EFS ke badle **Amazon FSx for Windows File Server** deta hai.

---

### EBS vs Instance Store vs EFS Figure 9.1 Breakdown)

Writer ne (Figure 9.1) mein teenon major storage types ka mawazna (comparison) kiya hai. Aayein isay ek clear architectural diagram se samajhte hain:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>


**Image (`Figure 9.1`) ka Mukammal Breakdown:**

1. **EBS Volume (Virtual Disk):** Diagram mein dekhein, Subnet 1 ka EBS Volume sirf Subnet 1 ke EC2 Instance 1 se jurha hai. Yeh boundary se bahar nahi ja sakta aur na hi kisi doosre server se attach ho sakta hai.
2. **Instance Store (Local Disk):** Diagram mein dekhein, Instance Store EC2 Instance 2 ke sath chipka hua hai kyunki yeh us physical host machine ke andar (hypervisor layer par) laga hota hai. Yeh temporary hai.
3. **EFS Filesystem (Shared Folder):** Diagram ke centre mein dekhein! EFS Filesystem AZ A ke Subnet 1 aur AZ B ke Subnet 2 dono ke saare EC2 instances se **ek hi waqt mein Network (NFSv4.1)** ke zariye jurha hua hai. Agar AZ A poora ka poora tabah bhi ho jaye, tab bhi AZ B ke servers ke paas EFS ka data chal raha hoga!

---

## All examples are covered by the Free Tier

* Is chapter ki tamam practical examples **AWS Free Tier** ke andar aati hain.
* Aap ko koi extra paise nahi dene parenge jab tak aap hidayat par amal karein aur in resources ko kuch dino se ziada na chalne dein.
* Chapter ke aakhir mein saare banaye gaye resources ko delete (clean up) karne ki mukammal guide di jayegi.

---

### EFS Ke Do Main Components Figure 9.2 Breakdown)

EFS system do bunyadi hisson se mil kar banta hai:

1. **Filesystem:** Yeh asal storage resource hai jo AWS Region ke andar aap ke data ko save karta hai. Lekin aap isay direct access nahi kar sakte.
2. **Mount Target:** Yeh Subnet ke andar bana hua ek Network Endpoint (IP Address) hota hai. EC2 instance is Mount Target par NFSv4.1 protocol ke zariye connect hota hai aur data parhta/likhta hai.

**Image Figure 9.2 ka Structural Breakdown:**

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

**Image breakdown:**

* Har Subnet ke andar ek **EFS Mount Target** lagaya gaya hai.
* Subnet 1 ke dono EC2 servers apne hi subnet ke Mount Target se baat karte hain.
* Subnet 2 ke dono EC2 servers apne subnet ke Mount Target se baat karte hain.
* Dono Mount Targets peeche se main **EFS Filesystem** ke sath jure hue hain, jisse charon EC2 servers ko same data nazar aata hai.

---

### Real-World Example: Shared `/home` Directories in Linux

Linux ek multiuser operating system hai, jahan alag alag users apna apna kaam kar sakte hain. Linux mein har user ka apna makhsoos folder hota hai jo `/home/$username` par pada hota hai.

Terminal par jab hum home directories ko dekhne ki command chalate hain:

```bash
$ ls -d -1 /home/* # Absolute paths ke sath tamam home directories ko list karta hai
drwx------ 2 andreas     andreas    4096 Jul 24 06:25 /home/andreas
drwx------ 3 michael     michael    4096 Jul 24 06:38 /home/michael
```

**Command aur Output ki Deep Detail:**

* `ls`: Files aur folders list karne ki command.
* `-d`: Sirf directories (folders) dikhao, unke andar ka saman nahi.
* `-1` (number 1): Har entry ko ek alag line par dikhao.
* `/home/*`: `/home` folder ke andar jitne bhi folders hain sab ka absolute path dikhao.
* `drwx------`:
* `d` = Directory (folder) hai.
* `rwx` = Owner ko **Read, Write, Execute** ki poori permissions hain.
* `------` = Baki kisi user ya group ko koi access nahi hai.


* `2` aur `3`: Hard links ki taadad (folders ki internal counting).
* `andreas andreas`: Folder ka Owner User `andreas` hai aur Owner Group bhi `andreas` hai.
* `4096`: Folder metadata ka size (4 KB).
* `Jul 24 06:25`: Folder banne ya last modify hone ki tareekh aur waqt.
* `/home/andreas`: Andreas user ka private ghar (home folder). Isay sirf Andreas hi khol sakta hai.
* `/home/michael`: Michael user ka private ghar (home folder). Isay sirf Michael hi khol sakta hai.

#### Real-World Masla (Problem)

Agar aap ke paas 5 alag alag EC2 servers chal rahe hain, toh har server par user ka home folder alag alag hoga. Agar Michael `EC2-Server-1` par login karke koi file save karega, aur agli dafa woh `EC2-Server-2` par login hoga, toh usay apni file **nahi milegi** kyunki woh doosra server hai!

#### EFS ka Hal (Solution)

Hum ek **EFS Filesystem** banayenge aur usay tamaam EC2 servers ke `/home` folder par mount kar denge!

Is se yeh hoga ke saare users ke home folders EFS par chale jayenge. Michael jis bhi EC2 server par login karega, usay apna wahi saara data, files, aur environment hamesha ready milega, bilkul waise hi jaise woh apne zati ghar mein ho!

---

