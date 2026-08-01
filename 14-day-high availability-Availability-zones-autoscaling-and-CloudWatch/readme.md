# Achieving high availability: Availability zones, autoscaling, and CloudWatch

## This chapter covers

Is chapter mein hum cloud engineering aur AWS ke sab se aham hissay ko samajhne wale hain. Writer batata hai ke is chapter mein hum char (4) mukhya (main) cheezein seekhenge:

* **CloudWatch alarm ki madad se kharab hone wali virtual machine ko dobara sahi karna (Recovering a failed virtual machine with a CloudWatch alarm):** Jab aap ki Virtual Machine (EC2 instance) kisi maslay ki wajah se band ya kharab ho jaye, toh CloudWatch naam ka chokidaar (monitoring tool) usay dekhte hi khud hi dobara restart aur recover kar deta hai.
* **Autoscaling ka istemal karke virtual machines ko lagataar chalate rehna (Using autoscaling to guarantee your virtual machines keep running):** Ek aisa automatic system banana jo hamesha yeh yakeeni banaye ke aap ki zaroorat ke mutabaq virtual machines hamesha chal rahi hain aur koi kharab ho toh usay replace kar de.
* **AWS Region mein Availability Zones ko samajhna (Understanding availability zones in an AWS region):** Yeh samajhna ke AWS ne alag alag shahron (Regions) mein bijli aur internet se azad alag alag data centers (Availability Zones) kaise banaye hain.
* **Disaster-recovery ki zarooriyat ka tajziya karna (Analyzing disaster-recovery requirements):** Jab koi bari tabahi (jaise bijli ka poora fail hona ya data center doobna) aaye, toh us se apne system ko bachane ke tareeqay samajhna.

---

### Real-World Example: Dukaan Ki Misaal (Online Shop)

Writer humein ek bohat aasan misaal se samjhata hai:

* **Purana / Kharab Tareeqah (Failure Scenario):** Farz karein aap ki internet par ek dukan (e-commerce web shop) hai jo ek computer (Virtual Machine) par chal rahi hai. Raat ke waqt jab aap so rahe hote hain, us computer ka koi hissa (hardware) kharab ho jata hai. Ab aap ki dukaan band ho gayi! Grahak (users) dukan par aate hain lekin cheezein nahi khareed paate. Woh subah hone tak 8 ghante intezar karne ke bajaye kisi doosri dukan par chale jaate hain. Yeh aap ke karobar (business) ke liye ek bohat bara nuqsan hai.
* **Naya / Aala Tareeqah (Highly Available Scenario):** Ab farz karein aap ne apna system "Highly Available" banaya hua hai. Raat ko hardware kharab hota hai, lekin **bina kisi insaan ke button dabaye**, system khud hi 2-3 minto mein samajh jata hai ke masla aaya hai. Woh kharab computer ko chor kar ek naye sahi computer par dukan ko dobara start kar deta hai. Grahak bina kisi rukawat ke khareedari jari rakhte hain!

Writer yahan ek bohat **ahem sachai** batata hai:

> **Virtual Machines (EC2 instances) by default "Highly Available" nahi hoti.** Is ka matlab hai ke agar aap khud kuch settings nahi karenge, toh cloud par bhi computer kharab ho sakta hai.

---

### Virtual Machine Kharab Hone Ki 4 Badi Wajahein (System Failure Scenarios)

Ek Virtual Machine ke band hone ya kharab hone ki writer ne 4 aasan wajahein batayi hain:

1. **Virtual Machine ke Operating System (OS) ka fail hona:** Jaise aap ke computer ki Windows ya Linux crash ho jaye (Software Problem).
2. **Main Computer (Host Machine) ke Software ka fail hona:** Jis bare asli physical computer par aap ki choti virtual machine chal rahi hai, uska apna OS ya virtualization software (Hypervisor) crash ho jaye.
3. **Physical Hardware ka tootna ya kharab hona:** Physical computer ka Processor (Compute), Hard Disk (Storage), ya Networking Wire/Card kharab ho jaye.
4. **Data Center ka fail hona:** Jis building (Data Center) mein woh computer rakha hai, wahan ki bijli (power) chali jaye, internet kat jaye, ya cooling system (ACs) band hone se computers garam ho kar band ho jayein.

---

### AWS Kya Karta Hai Aur Aap Ki Kya Zimmedari Hai?

* **Chota Masla (Small Outage):** Agar sirf ek physical computer kharab hota hai, toh AWS khud hi aap ki EC2 instance ko reboot kar ke kisi doosre sahi physical computer par chala deta hai.
* **Bara Masla (Larger Outage):** Agar poora ka poora computer rack ya data center ka hissa band ho jaye, toh AWS yeh kaam khud nahi karega! **Aap khud zimmedar hain** ke aap apne system ko auto-recovery par lagayein (CloudWatch Alarms ke zariye) ya apne kaam ko ek se zyada machines par taqseem (distribute) karein.

---

## Examples are 100% covered by the Free Tier

* **Bilkul Muft (Free Tier):** Is chapter mein jitni bhi practical misalein di gayi hain, woh AWS ke Free Tier mein aati hain. Aap ko koi paise nahi dene parenge.
* **Shart (Condition):** Shart yeh hai ke aap ne AWS ka naya account banaya ho aur is chapter ki practice khatam karne ke baad resources ko delete (clean up) kar dein. Inhein hafton tak chalte mat chhoriyega.

---

### High Availability Kya Hai? (Definition)

**High Availability (HA)** ka matlab hai ek aisa system jo **bina kisi rukawat ke lagataar chalta rahe (almost zero downtime)**. Agar koi kharabi aa bhi jaye, toh system khud hi usay theek kar le aur users ko pata bhi na chale.

Writer ne **Harvard Research Group (HRG)** ki ek standard classification batayi hai jisay **AEC-2** kehte hain:

* **99.99% Uptime:** Iska matlab hai ke poore saal (365 dino) mein aap ka system 99.99% time chalna chahiye.
* **Downtime Limit:** Poore saal mein aap ka system **zyada se zyada 52 minute aur 35.7 seconds** ke liye hi band ho sakta hai, us se zyada nahi!
* Jab aap EC2 instances ko is chapter ke tareeqon se set karenge, toh aap yeh 99.99% ka target haasil kar sakte hain bina kisi insaani madad (human intervention) ke.

---

## High availability vs. fault tolerance

In dono lafzon mein bohat barik aur ahem farq hai jisay writer ne bohat khoobsurat tareeqay se samjhaya hai:

| Feature | High Availability (HA) | Fault Tolerance (FT) |
| --- | --- | --- |
| **Aasan Misaal** | Gari ka tyre puncture ho jaye, toh gari 2 minute ke liye ruke aur automatic jack lag kar naya tyre lag jaye. | Gari ke 8 wheels hoon, agar 1 puncture ho bhi jaye toh gari bina ruke usi speed se chalti rahe. |
| **Downtime (Rukawat)** | Is mein **chota sa downtime (kuch minto ki rukawat)** aata hai jab tak system recover hota hai. | Is mein **ZERO downtime (koi rukawat nahi)** hoti. System bina ruke chalta rehta hai. |
| **Kharabi par Reaction** | Kharabi ke baad automatic recovery hoti hai. | Kharabi ke dauran bhi service mein koi farq nahi aata. |

*(Note: Fault-tolerant systems banana hum chapter 16 mein seekhenge).*

---

### AWS ke Building Blocks (HA Banane Ke Zariye)

AWS humein High Availability banane ke liye 3 mukhya tools/building blocks deta hai:

1. **Availability Zones (AZs):** Ek hi region mein alag alag, azad data centers ka istemal karna taake agar ek data center doob bhi jaye toh doosra chalta rahe.
2. **CloudWatch Monitoring & Auto-Recovery:** Virtual Machine ki sehat par nazar rakhna. Agar machine kharab ho, toh CloudWatch alarm bajaye aur auto-recovery trigger kare. *(Yeh un kaam-kaaj ke liye behtareen hai jo sirf ek hi machine par chal sakte hain)*.
3. **Autoscaling:** Yeh guarantee dena ke farz karein hamesha 3 machines chalni chahiye. Agar 1 kharab ho jaye toh Autoscaling khud hi purani ko mita kar nayi machine khari kar deta hai. *(Yeh un kaam-kaaj ke liye behtareen hai jo ek se zyada machines par taqseem ho sakte hain)*.

Is chapter ke agle hisso mein hum pehle single-machine ki auto-recovery seekhenge, phir data center outage se bachna, aur aakhir mein disaster recovery plans ko AWS architecture mein badalna seekhenge.

---