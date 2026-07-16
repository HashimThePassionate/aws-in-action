# Securing your system: IAM, security groups, and VPC

## Securing your systems on AWS

Deewar (wall) ki misaal se security ko samajhna sab se asaan hai. Agar aap ne apne ghar ki hifazat ke liye ek mazboot deewar khari karni hai, toh aapko bohot saari eenton (bricks) ki zaroorat hogi. Agar un mein se ek bhi eent kamzor hui ya apni jagah par na hui, toh chor (hackers) us kamzoori ka faida utha kar andar ghus sakte hain.

Aapko screen par di gayi image (**Figure 5.1**) mein yeh poora concept saaf nazar aa raha hoga. Chaliye pehle is figure ko break down karte hain ke writer is deewar ke zariye kya samjhana chahta hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

---

### Figure 5.1 Ka Breakdown: Security Ki Deewar

**Figure 5.1** ko agar aap ghaur se dekhein, toh isme security ko ek eenton ki deewar ki tarah dikhaya gaya hai:

* **Buniyadi Eentein (Bottom Layer):** Is deewar ki buniyaad mein do main cheezein hain—**"Installing software updates"** (software ko up-to-date rakhna) aur **"Restricting access to your AWS account"** (apne cloud account ki chaabi har kisi ko na dena).
* **Darmyan Wali Eentein (Middle Layer):** Beech mein sab se important brick hai **"Controlling network traffic to and from your EC2 instances"** (yeh tay karna ke kaunsa traffic aapke virtual computers ke andar aa sakta hai aur kaunsa bahar ja sakta hai).
* **Upar Wali Eentein (Top Layer):** Deewar ko mukammal karne ke liye upar **"Creating a private network in AWS"** (apna ek khusoosi khufia network banana) aur **"Securing your applications"** (apne software ko khud andar se secure rakhna) ki bricks lagayi gayi hain.

**Sabaq (Lesson):** Agar aap ne cloud infrastructure aur apni application ko bilkul safe rakhna hai, toh aapko in tamam building blocks (eenton) ko ek sath sahi jagah par fit karna hoga. Agar ek bhi brick missing hui, toh deewar nakara ho jayegi.

---

Chaliye ab is deewar ki sab se ahem char eenton (bricks) ko bilkul asaan lafzon mein samajhte hain:

### 1. Installing software updates (Software Updates Ko Install Karna)

* **Bachon Wali Example:** Jaise aapke mobile phone mein har thode din baad apps ya operating system ki update aati hai. Agar aap update nahi karenge, toh phone slow ho jata hai ya usme bugs aa jate hain jinse hacker aapka data chura sakte hain.
* **Asaan Technical Detail:** AWS par jab hum virtual servers (EC2 instances) chalate hain, toh unme chalne wale softwares (jaise Linux, Web Server, ya Databases) mein rozana nayi kamzoriyan (vulnerabilities) dhoondi jati hain. Software banane wali companies in kamzoriyon ko theek karne ke liye patches (updates) nikalti hain. Hamari sab se pehli zimmedari yeh hoti hai ke hum in updates ko bina kisi deri ke install karein. Agar hum susti karenge, toh automated hackers hamare system ko asani se apna shikar bana lenge.

### 2. Restricting access to your AWS account (AWS Account Ka Access Mehdood Karna)

* **Bachon Wali Example:** Jaise aap apne ghar ki locker ki chaabi har kisi ke hath mein nahi dete. Sirf usay dete hain jise waqti zoorat ho, aur kaam khatam hote hi chaabi wapas le lete hain.
* **Asaan Technical Detail:** Agar aap ki team mein aur bhi log (coworkers) hain ya kuch automatic scripts hain jo aapke AWS account ko chalati hain, toh sab ko full control (Administrator access) dena sab se bari be-waqufi hai. Agar kisi buggy script (galti se likhe gaye code) ya kisi ghalti se coworker ne galat command chala di, toh aapka poora infrastructure tabah ho sakta hai. Is liye hum **Least Privilege Principle** use karte hain—yani jis ko jitna kaam hai, usay sirf utni hi ijazat (permissions) di jaye.

### 3. Controlling network traffic to and from your EC2 instances (Traffic Ko Control Karna)

* **Bachon Wali Example:** Ghar ke darwaze aur khidkiyan. Agar aap ke ghar mein mehman aane hain, toh aap sirf baithak (drawing room) ka darwaza kholte hain, ghar ke saare pichle darwaze aur khidkiyan band rakhte hain taake koi chor peeche se na ghuse.
* **Asaan Technical Detail:** Jab aap cloud par koi web server chalate hain, toh bahar ki duniya (internet) ke liye sirf do darwaze khule hone chahiye:
* **Port 80:** Yeh HTTP traffic ke liye hota hai (unsecured web browsing).
* **Port 443:** Yeh HTTPS traffic ke liye hota hai (secured/encrypted browsing).
In do ports ke ilawa, internet se aane wale tamam raste (jaise Database port ya SSH port) band hone chahiye taake koi bahar ka banda direct aapke servers mein dakhil na ho sake.



### 4. Creating a private network in AWS (Private Network Banana)

* **Bachon Wali Example:** Ek aam park aur ek VIP private society. Park mein koi bhi aa ja sakta hai, lekin VIP society ke gate par security hoti hai aur har koi wahan bina ijazat nahi ja sakta.
* **Asaan Technical Detail:** AWS par hum **VPC (Virtual Private Cloud)** banate hain. Iske andar hum **Subnets** (network ke chhote hisse) aur **Routing Tables** (traffic ka rasta dikhane wale maps) define karte hain. Hum apne sensitive databases ko hamesha ek "Private Subnet" mein rakhte hain, jiska internet se direct koi rasta hi nahi hota. Agar internet se koi traffic aana bhi chahe, toh woh pehle public area mein aayega, direct private area mein nahi ghus sakta.

---

### One Important Brick is Missing: Securing Your Applications (Application Security)

Deewar toh aap ne mazboot bana di, lekin agar aapne ghar ke andar khane mein zeher mila diya, toh nuksaan andar se hi hoga.

AWS aapke network aur hardware ko toh secure kar deta hai, lekin jo **Application** (software code ya website) aap chala rahe hain, use secure rakhna aapka apna kaam hai. Writer batata hai ke is book mein application security cover nahi ki gayi, lekin aapko ye baatein lazmi follow karni chahiye:

1. **User Input Verification:** User jo bhi data website par likhe (jaise search bar mein ya login form mein), usay check karein ke usme koi kharab code ya virus toh nahi hai (SQL Injection se bachne ke liye).
2. **No Plain Text Passwords:** Passwords ko database mein seedha seedha (plain text) kabhi save nahi karna chahiye. Unhein hamesha "Hash" (encryption technique) kar ke save karein.
3. **TLS/SSL Encryption:** Apne users aur virtual machines ke darmiyan hone wali baatcheet ko encrypt karein (jaise HTTPS use karna), taake beech raste mein koi aapka data chura na sake.

---

### Not all examples are covered by Free Tier

> ⚠️ **Cost Warning (Paise Lagne Ka Khatra):**
> Is chapter mein jo hum practical kaam karenge, woh saare ke saare AWS Free Tier (muft scheme) mein cover nahi hote. Jab bhi koi aisi configuration aayegi jahan aapke paise lag sakte hain, wahan ek **Warning Message** diya jayega.

* **Design/Money Saving Decision:** Iska behtareen hal yeh hai ke jab aap koi practical seekh rahe hon, toh usay seekhne ke baad furan un resources ko **delete (clean up)** kar dein. Agar aap unhein 2-3 din se zyada chalta chorenge, toh billing start ho sakti hai. Koshish karein ke chapter ko kuch hi dinon mein mukammal kar ke account saaf kar dein.

---

### Chapter requirements

Is chapter ke concepts ko asani se hazam karne ke liye, aapko in networking ke buniyadi lafzon (terms) ka pata hona zaroori hai. Chaliye inhein bilkul 1-line mein asaan kar ke samajh lete hain:

* **Subnet:** Ek bare network ka chota sa tukra ya kamra (jaise poore ghar mein se ek bedroom).
* **Route tables:** Ek rasta dikhane wala board (road sign) jo batata hai ke network ka traffic kis taraf jayega.
* **Access control lists (ACLs):** Network ke darwaze par khara security guard jo aane aur jaane wale traffic ko rules ke mutabaq check karta hai.
* **Gateway:** Ghar ka main gate jo aapko bahar ki duniya (internet) se jorta hai.
* **Firewall:** Ek hifazati deewar jo kharab ya dangerous traffic ko rokti hai.
* **Port:** Ek specific khidki ya darwaza jo kisi khas service (jaise website ya email) ke liye khususi taur par khola jata hai.
* **Access management:** Yeh tay karna ke kis insaan ko ghar ke kis kamre mein jaane ki permission hai aur kis ki nahi.
* **Basics of the Internet Protocol (IP), including IP addresses:** Internet par chalne wali har device ka ek unique identity card number ya ghar ka pata (address), taake data sahi jagah pahunch sake.

---


## Who’s responsible for security?

Cloud mein security kisi ek bande ka kaam nahi hai. Yeh ek **Shared-Responsibility Environment** hai. Iska matlab hai ke cloud ko secure rakhne ki zimmedari AWS aur aap (customer) ke darmiyan aadhi-aadhi banti hui hai.

Isko bachon ki tarah samajhne ke liye ek **Kiraaye ke Flat (Rented Apartment)** ki misaal lete hain.

> 🏢 **Ghar Wali Misal:**
> Building ka jo maalik (Landlord yani **AWS**) hai, uski zimmedari hai ke woh building ka main gate mazboot banaye, wahan security guard bithaye, aur aag lagne se bachane ke liye fire alarms lagaye. Lekin aapke flat ke andar ka jo darwaza hai, usay tala lagana, apni tijori ki chaabi sambhal kar rakhna, aur flat ke andar kis ko aane dena hai—yeh sab aapki (**Customer**) zimmedari hai. Agar aap apne flat ka darwaza khula chor dein aur chori ho jaye, toh aap landlord ko zimmedar nahi thehra sakte.

Chaliye ab is poore concept ko detail mein step-by-step samajhte hain ke AWS kya karta hai aur aapko kya karna hoga.

---

### AWS Ki Zimmedariyan (Security "OF" the Cloud)

AWS is baat ka zimmedar hai ke jo physical infrastructure (hardware, data centers, cables) aap use kar rahe hain, woh har tarah se safe ho. AWS in char main cheezon ki hifazat karta hai:

* **Protecting the network (Network Ki Hifazat):** AWS apne network ko har waqt chalne wale automatic monitoring systems aur bohot hi heavy internet pipelines ke zariye secure rakhta hai. Iska sab se bara faida yeh hota hai ke yeh aapke systems ko **DDoS (Distributed Denial of Service) attacks** se bachata hai.
* *DDoS Kya Hai?* Farz karein aapki ek khilono ki dukan hai jahan aam log aate hain. Agar koi dushman 10,000 nakli bache aapki dukan ke samne khare kar de taake asli khareedar andar na aa sakein, toh use DDoS attack kehte hain. AWS ka automatic system in nakli logon (fake traffic) ko pehle hi rok deta hai.


* **Performing background checks on employees (Mulaazmeen Ki Janch):** Jo engineers ya staff AWS ke un ahem hisson (sensitive areas) mein kaam karte hain jahan physical servers pare hain, AWS unka mukammal criminal aur professional background check karta hai. Kisi bhi aam ya shakki bande ko un servers ke paas jaane ki ijazat nahi hoti.
* **Decommissioning storage devices (Purani Hard Drives Ko Tabah Karna):** Jab AWS ke data centers mein koi hard drive ya storage device purani ho jati hai ya kharab ho jati hai (End of Life), toh AWS use bahein phenkne ya bechne ke bajaye **physically destroy** (shredder mein daal kar ya hathoray se) tukre-tukre kar deta hai. Iska faida yeh hota hai ke koi badmaash us purani hard drive ko dhoodh kar aapka data wapas nikal nahi sakta.
* **Ensuring the physical and environmental security of data centers (Data Centers Ki Hifazat):** Jahan AWS ke hazaron servers lage hain, un buildings ki hifazat bohot sakht hoti hai. Wahan har waqt security staff maujood hota hai, biometric locks hote hain, aur agar khuda-na-khwasta aag lag jaye toh us se bachne ke liye advance **Fire Protection Systems** lage hote hain jo pani ke bajaye special gas use karte hain taake computers kharab na hon.

> 📋 **Compliance Note:** AWS ke in tamam hifazati intezamaat ko bar-bar **Third-Party Auditors** (bahar ki top companies) check karti hain aur certificate deti hain. Agar aap dekhna chahein ke AWS standard par poora utar raha hai ya nahi, toh aap har waqt unki up-to-date report is website par check kar sakte hain: [aws.amazon.com/compliance/](https://aws.amazon.com/compliance/).

---

### What are your responsibilities? See the following:

AWS ne aapko ek bilkul safe aur clean ghar bana kar de diya hai, lekin us ghar ke andar ka nizaam chalana aapki zimmedari hai. **2026** ke modern cloud dor mein aapko in panch cheezon ka khususi khayal rakhna hota hai:

* **Configuring access management using AWS IAM (Chaabiyan Aur Permission Set Karna):** Aapki sab se pehli zimmedari yeh hai ke aap **AWS IAM (Identity and Access Management)** ka sahi use karein. Iska matlab yeh hai ke agar aapke paas **S3** (data save karne ki jagah) aur **EC2** (virtual servers) hain, toh aap har bande ya script ko sirf utna hi access dein jitna uske kaam ke liye zaroori ho (**Minimum Access**). Kisi ko bhi faltu ijazat nahi honi chahiye.
* **Encrypting network traffic using HTTPS (Chitthi Ko Lock Box Mein Bhejna):** Jab aapka data internet par chal raha ho, toh aapki zimmedari hai ke aap use **Encrypt** (code word mein tabdeel) karein, jaise **HTTPS (SSL/TLS)** use karna. Agar aap aisa nahi karenge, toh internet ke raste mein betha koi bhi hacker aapka data parh sakta hai ya usme her-pher kar sakta hai.
* **Configuring a firewall with security groups and NACLs (Chowkidar Khare Karna):** Aapko apne virtual network ki hifazat ke liye firewall set karni hoti hai. AWS mein iske liye do main cheezein hoti hain:
* **Security Groups:** Yeh aapke server (EC2) ka apna personal chowkidar hota hai.
* **NACLs (Network Access Control Lists):** Yeh aapke poore sub-network (subnets) ka main gate ka chowkidar hota hai.
In dono ko sahi tarah configure karna aapka kaam hai taake sirf sahi traffic andar aa sakay aur faltu traffic bahar hi ruk jaye.


* **Encrypting data at rest (Sote Hue Data Ko Tala Lagana):** Jab aapka data database mein ya kisi storage system (jaise EBS ya S3) mein chup-chap para hua ho (At Rest), toh use bhi encrypt karna aapki zimmedari hai. Modern AWS mein aap ek click se encryption on kar sakte hain taake agar koi data chura bhi le, toh use parh na sakay.
* **Managing patches for the OS and additional software (Software Ko Update Rakhna):** AWS aapko virtual machine (EC2) bana kar de deta hai, lekin us machine ke andar jo Operating System (jaise Linux ya Windows) chal raha hai, aur jo softwares aapne khud install kiye hain, unko update (patch) rakhna aapka kaam hai. Agar unme koi security vulnerability aati hai, toh unka patch aapne khud lagana hai.

---

### Khulasa (Summary)

Security hamesha **AWS aur Aapke darmiyan ek teamwork** ka naam hai. Agar AWS apna kaam perfect kare aur aap upar diye gaye rules ke mutabaq apna kaam sahi se karein, toh aap cloud par aala tareen security (High Security Standards) haasil kar sakte hain.

Yad rakhein, agar aap ne flat ka darwaza khula chora (yani firewall ghalt set ki), toh AWS building ka guard hone ke bawajood aapko chori se nahi bacha sakega.

Agar aap is model ko mazeed gehrai se aur tasweeron ke sath samjhana chahte hain, toh aap unki is official link ko dekh sakte hain: [aws.amazon.com/compliance/shared-responsibility-model/](https://aws.amazon.com/compliance/shared-responsibility-model/).

---

## Keeping the operating system up-to-date

Duniya mein koi bhi software aisa nahi hai jisme kabhi koi kharabi ya kamzori (vulnerability) na nikle. Har hafte Linux Kernel, OpenSSL, Java, Apache, PHP, ya WordPress jise chalne wale softwares mein naye security bugs dhoonde jaate hain.

* **Bachon Wali Example:** Farz karein aapke ghar ke taale (lock) mein choron ko ek aisi kharabi ka pata chal jaye jisse koi bhi purani chaabi lagakar taala khola ja sakta hai. Ab agar taala banane wali company us kharabi ko theek karne ke liye ek naya "patch" ya pucha (fix) nikalti hai, toh aapki zimmedari hai ke aap furan use apne taale par lagayein. Agar aap der karenge, toh chor (hackers) us kharabi ka faida utha kar andar ghus jayenge.

Isliye, cloud engineering mein aapke paas ek solid aur automatic plan hona chahiye ke jaise hi koi naya security update aaye, aapke saare virtual servers (EC2 instances) furan patch (update) ho sakein.

**Amazon Linux 2** operating system jab pehli baar start hota hai, toh woh **cloud-init** ke zariye critical aur ahem security updates ko automatic install kar leta hai. Lekin baqi bache hue updates ko handle karne ke liye hamare paas teen main options hote hain:

1. **Boot ke aakhri hisse mein saare updates install karna:** Iske liye hum user-data script mein `yum -y update` command likhte hain.
2. **Boot ke waqt sirf security updates install karna:** Iske liye hum user-data script mein `yum -y --security update` chalate hain.
3. **AWS Systems Manager Patch Manager ka use karna:** Yeh sab se behtareen aur robust tareeqa hai jisme hum rules ke mutabaq patching karte hain.

Chaliye pehle shuruaati do options ke CloudFormation codes ko dekh kar samajhte hain.

---

### User Data Ke Zariye Initial Patching Setup

Agar aap chahte hain ke aapka EC2 instance chalte hi automatic tarike se update ho jaye, toh aap CloudFormation template mein niche diye gaye tarike se UserData ka use kar sakte hain:

#### Option A: Saare Updates Install Karna (All Updates)

```yaml
Instance:
  Type: 'AWS::EC2::Instance'
  Properties:
    # [...]
    UserData: !Base64 |
      #!/bin/bash -ex
      yum -y update    <--------- Installs all updates
```

* **Asaan Explanation:** Yahan `yum` Amazon Linux 2 ka official package manager (yani dukan-dar) hai. Jab server pehli baar chalta hai, toh bash script ke andar betha `yum -y update` dukan-dar se kehta hai ke "bina mujh se pooche (`-y` yani yes), is server ke saare softwares ko unke sab se naye version par update kar do."

#### Option B: Sirf Security Updates Install Karna (Security Updates Only)

Agar aap chahte hain ke baqi softwares ke versions na bbadlein aur sirf wahi cheezein update hon jo security ke liye zaroori hain, toh aap yeh code use karte hain:

```yaml
Instance:
  Type: 'AWS::EC2::Instance'
  Properties:
    # [...]
    UserData: !Base64 |
      #!/bin/bash -ex
      yum -y --security update    <--------- Installs only security updates
```

* **Asaan Explanation:** `--security` tag lagane se `yum` baqi aam features ko chor deta hai aur sirf un patches ko install karta hai jo system ko hackers se bachane ke liye sab se zyada ahem hain.

---

### The Following Challenges Are Still Waiting For A Solution

Upar diye gaye dono tareeqay bohot asaan lagte hain, lekin inme do bare maslay (challenges) hain jinhein hal karna zaroori hai:

* **The Reboot Problem:** Sab se bara masla yeh hai ke kuch updates (khususan **Kernel updates**, jo operating system ka dil hota hai) install hone ke bawajood tab tak kaam shuru nahi kartin jab tak server ko **reboot (restart)** na kiya jaye. Agar server restart nahi hua, toh update hone ke bawajood aapka system kamzor (vulnerable) hi rahega.
* **Not Continuous:** Startup par patching karna kaafi nahi hota. Agar aapka server lagatar 6 mahine tak chalta rahe, toh 6 mahine pehle ki updates toh lag jayengi, lekin is dauran rozana aane wali nayi kamzoriyon se nipatne ke liye aapke paas koi continuous (lagatar) nizaam nahi hoga.

---

### Solution: AWS Systems Manager (SSM) Patch Manager

Inhi dono maslon se bachne ke liye hum khud se dubara koi naya system nahi banate (yani we don't reinvent the wheel), balkay AWS ke bane-banaye tool **AWS Systems Manager (SSM) Patch Manager** ka use karte hain.

**Figure 5.2** ke mutabaq, Patch Manager akela kaam nahi karta, balkay yeh SSM ke andar maujood un ahem core features (capabilities) ka ek majmua hai jo aapas mein mil kar kaam karte hain:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

* **Agent:** Yeh server ke andar betha ek chota sa mukhbir ya worker software hota hai. Amazon Linux 2 mein yeh pehle se install aur automatic start hota hai. Yeh har waqt AWS cloud se aane wale orders ka intezar karta hai.
* **Document:** Yeh samajh lein ke ek aam script ka bara bhai (script on steroids) hai. Patching ke liye hum AWS ka pehle se banaya hua standard document use karte hain jiska naam hai `AWS-RunPatchBaseline`.
* **Run Command:** Yeh cloud se baji gayi woh command hai jo bina kisi manual login (SSH) ke, direct Agent ke zariye server ke andar scripts chala deti hai.
* **Association:** Yeh State Manager ka hissa hai. Iska kaam yeh tay karna hai ke koi command kisi schedule ke tehat ya server ke start hote hi (startup par) lazmi execute ho.
* **Maintenance Window:** Yeh ek khas waqt ka hissa (time window) hota hai jo hum tay karte hain (jaise raat ke 3 baje). Hum AWS ko kehte hain ke jo bhi heavy maintenance ya restart ka kaam hai, woh sirf isi time window ke andar hona chahiye taake aam users ka nuksaan na ho.
* **Patch baseline:** Yeh rules ki ek policy book hoti hai jo batati hai ke kaunse patches ko safe samajh kar install karna hai. AWS ne Amazon Linux 2 ke liye ek default patch baseline di hui hai jo un tamam security patches ko automatic approve kar deti hai jin ki severity **Critical** ya **Important** hoti hai. Isme ek **7-day waiting period** hota hai—yani jab koi naya patch market mein aata hai, toh yeh system 7 din intezar karta hai taake yeh pakka ho sake ke us patch mein koi bug nahi hai, aur phir use automatic approve kar deta hai.

---

### CloudFormation Setup For Patch Manager

Niche diye gaye CloudFormation code ke zariye hum ek automatic Maintenance Window aur Association tay karte hain taake patching automatic aur continuous ho sake:

#### 1. Maintenance Window Define Karna

```yaml
MaintenanceWindow:
  Type: 'AWS::SSM::MaintenanceWindow'
  Properties:
    AllowUnassociatedTargets: false
    Duration: 2
    Name: !Ref 'AWS::StackName'
    Schedule: 'cron(0 5 ? * SUN *)'
    Cutoff: 1
```

* **Asaan Explanation:** Yeh code ek **2 ghante ki window** (`Duration: 2`) banata hai jo har **Sunday subah 5:00 AM UTC** par chalegi (`cron(0 5 ? * SUN *)`). Yahan `Cutoff: 1` ka matlab yeh hai ke aakhri 1 ghante mein koi naya kaam shuru nahi kiya jayega, taake jo kaam pehle se chal rahe hain unhein khatam hone ka poora waqt mil sake.

#### 2. Target Server Select Karna

```yaml
MaintenanceWindowTarget:
  Type: 'AWS::SSM::MaintenanceWindowTarget'
  Properties:
    ResourceType: INSTANCE
    Targets:
      - Key: 'InstanceIds'
        Values:
          - !Ref Instance
    WindowId: !Ref MaintenanceWindow
```

* **Asaan Explanation:** Yeh resource hamari maintenance window ko hamare specific EC2 instance (`!Ref Instance`) ke sath jor deta hai. Agar hum chahein toh yahan IDs ke bajaye specific Tags (jaise Environment: Production) ka use kar ke bohot saare servers ko ek sath target kar sakte hain.

#### 3. Task Assign Karna (What to do?)

```yaml
MaintenanceWindowTask:
  Type: 'AWS::SSM::MaintenanceWindowTask'
  Properties:
    MaxConcurrency: '1'
    MaxErrors: '1'
    Priority: 1
    WindowId: !Ref MaintenanceWindow
    Targets:
      - Key: 'WindowTargetIds'
        Values:
          - !Ref MaintenanceWindowTarget
    TaskArn: 'AWS-RunPatchBaseline'
    TaskType: 'RUN_COMMAND'
    TaskInvocationParameters:
      RunCommandParameters:
        Comment: 'Patch instance'
        OutputS3BucketName: !Ref S3Bucket
```

* **Asaan Explanation:** Yeh resource bta raha hai ke jab Sunday subah 5 baje window khulegi, toh kaunsa kaam hoga. Yeh `RUN_COMMAND` ke zariye `AWS-RunPatchBaseline` document ko hamare server par chalayega. Yeh document khud hi check karega ke kaunse patches bache hain, unhein install karega, aur agar zaroorat hui toh instance ko **reboot** bhi kar dega. Is poore kaam ka logs ek S3 bucket mein save ho jayega.

#### 4. Startup Par Checking Ke Liye Association

```yaml
MaintenanceWindowTaskAssociation:
  Type: 'AWS::SSM::MaintenanceWindowTaskAssociation'
  Properties:
    Name: !Ref MaintenanceWindow
    TaskArn: 'AWS-RunPatchBaseline'
    Targets:
      - Key: 'InstanceIds'
        Values:
          - !Ref Instance
```

* **Asaan Explanation:** Yeh association is baat ko yakeeni banati hai ke jab bhi instance pehli dafa start (boot) ho, toh yeh same patching parameters ke mutabaq furan checking shuru kar de.

---

### Prerequisite: IAM Instance Role For S3 Access

Patch Manager ko chalne ke liye hamare EC2 instance ke paas AWS ke kuch specific S3 buckets se data khinchne (Read access) ki ijazat honi chahiye. Iske bina Agent kaam nahi kar sakega:

```yaml
InstanceRole:
  Type: 'AWS::IAM::Role'
  Properties:
    #[...]
    Policies:
      - PolicyName: PatchManager
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action: 's3:GetObject'
              Resource:
                - !Sub 'arn:aws:s3:::patch-baseline-snapshot-${AWS::Region}/*'
                - !Sub 'arn:aws:s3:::aws-ssm-${AWS::Region}/*'
```

* **Asaan Explanation:** Yeh IAM Policy server ko ijazat deti hai ke woh chalte waqt AWS ke regional buckets (`patch-baseline-snapshot` aur `aws-ssm`) ke andar ja kar patching se mutaliq zaroori configuration files aur data ko `s3:GetObject` ke zariye read kar sake.

---

### Visualizing Patches With Scan Association

Patch Manager sirf patches lagata nahi hai, balkay yeh aapko yeh bhi dikha sakta hai ke kaunse patches bache hue hain aur aapka server kitna safe hai. Is data ko har waqt fresh rakhne ke liye hum ek alag association banate hain jo sirf scan karti hai:

```yaml
AssociationRunPatchBaselineScan:
  Type: 'AWS::SSM::Association'
  Properties:
    ApplyOnlyAtCronInterval: true
    Name: 'AWS-RunPatchBaseline'
    Parameters:
      Operation:
        - Scan
    ScheduleExpression: 'cron(0 0/1 * * ? *)'
    Targets:
      - Key: InstanceIds
        Values:
          - !Ref Instance
```

* **Asaan Explanation:** Yeh association har ghante (`cron(0 0/1 * * ? *)`) chal kar server ka muayna (Scan) karti hai ke koi naya patch aane wala toh nahi hai.
* **Crucial Design Decision:** Yahan `ApplyOnlyAtCronInterval: true` lagaya gaya hai taake yeh association startup par run na ho. Iska faida yeh hota hai ke yeh hamari pehli wali association (jo startup par install karti hai) ke sath takrayegi nahi. Agar `AWS-RunPatchBaseline` document ek hi waqt mein ek server par do dafa chal jaye (ek taraf se Scan aur ek taraf se Install), toh yeh crash ho jata hai. Is takrao (conflict) se bachne ke liye yeh setting lazmi hai.

---

### Figure 5.3 Ka Breakdown: Patch Manager Dashboard

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

Jab aap is poori CloudFormation template ko deploy karte hain aur AWS Systems Manager ke console mein ja kar **Patch Manager** ko open karte hain, toh aapko **Figure 5.3** jaisa ek dashboard dikhta hai:

* **Gol Chart (Pie Chart):** Yeh chart aapko live status dikhata hai. Jaisa ke figure mein dikhaya gaya hai, agar aapka server poori tarah up-to-date hai, toh wahan **"Compliant: 1"** likha aayega aur ek green circle poora nazar aayega.
* **Non-compliance Counts:** Dashboard ke right side par agar kisi patch ki installation fail ho jaye, ya koi critical patch missing ho, ya koi server restart (reboot) hone ka intezar kar raha ho, toh unki ginti (count) `0` se badh kar wahan show ho jati hai taake engineer ko furan pata chal sake.
* **Manual Override:** Agar aap automatic schedule ka intezar nahi karna chahte aur furan patching shuru karna chahte hain, toh aap dashboard par upar right corner mein diye gaye **"Patch now"** button par click kar ke manually bhi isi waqt patching process start kar sakte hain.

---

### Cleaning up

> ⚠️ **Cost Reminder:**
> Jab aap is section ka practical mukammal kar lein, toh AWS CloudFormation par ja kar apne banaye hue `ec2-os-update` stack ko **Delete** karna mat bhooliyega. Agar aap resources ko chalta chor denge, toh unke chalne ka kharcha (charges) aapke account par par sakta hai.

---

## Securing your AWS account

AWS account ki security aap ke cloud infrastructure ki buniyaad hai. Agar koi badmaash ya attacker aapke AWS account ka access haasil kar leta hai, toh woh aapka keemti data chura sakta hai, aapke kharche par heavy resources chala kar aapko hazaron dollars ka bill bhej sakta hai, ya phir aapka saara data ek jhatke mein delete kar sakta hai.

> 🧺 **Bachon Wali Example:**
> Apne AWS account ka tasawwur ek bari tokri (basket) ki tarah karein. Jaise aap **Figure 5.4** mein dekh sakte hain, is tokri ke andar aapke saare toys (ya cloud resources) jaise EC2 instances, CloudFormation stacks, aur IAM users ek sath pare hote hain.

<div align="center">
  <img src="./images/04.png" width="600"/>
</div>

* **Figure 5.4 Ka Breakdown:** Is tasweer mein saaf dikhaya gaya hai ke jab aap **Management Console** (web browser) ke zariye aate hain, toh aap direct account ke main malik yani **Root User** ke roop mein dakhil hote hain. Jabki **Terminal (CLI)** ke zariye kaam karte waqt aapne `mycli` naam ka ek chota user banaya hua hai.
* **The Root User Threat:** Har nayi tokri (AWS account) ke sath ek **Root User** automatic milta hai jiske paas bina kisi rok-tok ke har cheez ka full control hota hai. Ab tak hum isi se login kar rahe hain, lekin asal professional dunya mein aisa karna khatarnak hai. Is section mein hum ek extra user banayenge taake root user ko hamesha ke liye locked aur safe rakha ja sake, aur har bande ko uske kaam ke mutabaq limited access diya jaye.

#### Attacker Aapke Account Mein Kaise Ghus Sakta Hai? (Authentication Path)

Kisi bhi chor ko aapke account ke andar aane ke liye teen raste milte hain:

1. **Root User ke zariye:** Iske liye attacker ko aapka main email aur password chahiye.
2. **IAM User ke zariye:** Iske liye usay user ka password ya phir CLI wali **Access Keys** (Access Key ID + Secret Access Key) chahiye hotin hain.
3. **AWS Resource (jaise EC2 instance) ban kar:** Agar attacker aapki chalne wali machine (EC2) ke andar ghus jaye, toh woh wahan bethi **Instance Metadata Service (IMDS)** se baatchit kar ke temporary keys chura sakta hai.

In chor-raston ko band karne ke liye hamara sab se pehla qadam **Multifactor Authentication (MFA)** on karna hai, jo password ke upar ek extra security ka tala laga deta hai.

---

## Securing your AWS account’s root user

Root user aapke account ka sab se bada darwaza hai, isliye is par MFA lagana sab se zyada zaroori hai. MFA active hone ke baad, agar kisi chor ko aapka password pata chal bhi jaye, toh bhi woh tab tak login nahi kar sakega jab tak uske paas aapke phone mein aane wala temporary token (OTP) na ho.

<div align="center">
  <img src="./images/05.png" width="600"/>
</div>

* **Figure 5.5 Ka Breakdown:** Agar aap **Figure 5.5** ki tasweer ko dekhein, toh yeh AWS IAM console ka **Your Security Credentials** wala page dikha rahi hai. Tasweer mein numbered tags (1, 2, 4, 5) ke zariye unhi steps ko highlight kiya gaya hai jo niche diye gaye hain taake aapko dhoodhne mein aasani ho.

#### Root User Par MFA On Karne Ke Steps:

1. Management Console mein top right (upar seedhe haath) par apne **Name** par click karein.
2. Drop-down menu mein se **Security Credentials** ko select karein.
3. Apne smartphone par ek free MFA app install karein jo TOTP (Time-based One-Time Password) standard ko support karti ho (jaise **Google Authenticator** ya Microsoft Authenticator).
4. Page par niche aa kar **Multi-Factor Authentication (MFA)** ke section ko expand (kholein).
5. **Activate MFA** ke blue button par click karein.
6. **Virtual MFA Device** ka option select karein aur next step par chalein.
7. Ab samne aane wale QR code ko apne mobile ki app se scan karein aur lagatar do temporary codes enter kar ke setup mukammal karein.

> ⚠️ **Professional Security Tip:** Agar aap apne mobile ko hi virtual MFA bana rahe hain, toh us mobile ke andar kabhi bhi root user ka password save mat karein aur na hi us mobile se AWS console chalayein. Token aur password dono alag alag jagah hone chahiye. Zyada high security ke liye aap hardware tokens jaise **YubiKeys** ka use bhi kar sakte hain.

---

## AWS Identity and Access Management (IAM)

<div align="center">
  <img src="./images/06.png" width="600"/>
</div>

* **Figure 5.6 Ka Breakdown:** **Figure 5.6** mein IAM ka poora dhaanchan dikhaya gaya hai. Yeh service AWS ke dimgah ki tarah kaam karti hai. Jab bhi aap ya aapka koi server **AWS API** ko koi request bhejta hai (jaise: *Mera ek naya server chalao*), toh beech mein betha IAM do cheezein check karta hai: Pehla **Authentication** (Kya aap sach mein wahi hain jo dawwa kar rahe hain?) aur doosra **Authorization** (Kya aapko yeh kaam karne ki ijazat hai?).

IAM ke andar char buniyadi components (pors) hote hain jinhein samajhna laazmi hai:

* **IAM User:** Yeh aam taur par zinda insano (jaise aap ya aapke developers) ya AWS se bahar chalne wale automatic softwares ke liye banta hai.
* **IAM Group:** Yeh users ka ek majmua (collection) hota hai jinhein hum ek jaisi permissions dena chahte hain.
* **IAM Role:** Yeh kisi insan ke liye nahi hota, balkay AWS ke apne resources (jaise ek EC2 instance) ko temporary power dene ke liye banta hai.
* **IAM Identity Policy:** Yeh ek simple document hota hai jisme saaf-saaf likha hota hai ke kis cheez ki ijazat hai aur kis ki nahi.

Let's look at the exact differences in **Table 5.1**:

#### Table 5.1 Differences among an AWS account root user, IAM user, and IAM role

| Feature | AWS account root user | IAM user | IAM role |
| --- | --- | --- | --- |
| **Can have a password** (needed to log in to the AWS Management Console) | Always | Yes | No |
| **Can have access keys** (needed to send requests to the AWS API (e.g., for CLI or SDK)) | Yes (not recommended) | Yes | No |
| **Can belong to a group** | No | Yes | No |
| **Can be associated with an EC2 instance, ECS container, Lambda function** | No | No | Yes |

> 🔑 **Golden Rule:** AWS mein paidaishi taur par (by default) kisi bhi user ya role ke paas koi taqat nahi hoti, woh kuch nahi kar sakte. Jab tak aap unke sath ek **Identity Policy** attach nahi karenge, unka access zero rahega.

---

## Defining permissions with an IAM identity policy

Identity policy ko hum **JSON** format mein likhte hain. Iske andar ek ya ek se zyada **Statements** hoti hain jo kisi kaam ko ya toh **Allow** (ijazat) kartin hain ya **Deny** (rokna). Agar hum kisi jagah `*` (wildcard) lagate hain, toh iska matlab hota hai "Sub Kuch".

> 💡 **Identity vs. Resource Policies (Farq):**
> * **Identity Policies:** Yeh hamesha kisi User, Group, ya Role ke upar chipkayi jati hain.
> * **Resource Policies:** Yeh direct kisi resource (jaise S3 bucket) par lagayi jati hain. Unme ek ahem cheez hoti hai jise **`Principal`** kehte hain. Principal yeh batata hai ke kaunsa banda ya account is resource par aa sakta hai (aur isko public yani poori duniya ke liye open bhi kiya ja sakta hai).
> 
> 

Chaliye policies ke kuch mazedaar design decisions aur codes ko breakdown karte hain:

#### Example 1: Full EC2 Access Policy

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": "ec2:*",
        "Resource": "*"
    }]
}

```

* `"Version": "2012-10-17"`: Yeh AWS ke policy ka engine version hai jise lock rakhna zaroori hai.
* `"Effect": "Allow"`: Iska matlab hai hum ijazat de rahe hain.
* `"Action": "ec2:*"`: `ec2` service ka har ek kaam (wildcard `*` ki wajah se) allowed hai.
* `"Resource": "*"`: Yeh access account ke kisi bhi EC2 resource par chal sakta hai.

#### Example 2: Deny Overrides Allow (Rok-tok Sub Se Pehle)

AWS ka ek pakka qanoon hai: **Deny hamesha Allow par bhaari parta hai**. Agar ek statement ijazat de rahi hai aur doosri mana kar rahi hai, toh kaam ruk jayega.

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": "ec2:*",
        "Resource": "*"
    }, {
        "Effect": "Deny",
        "Action": "ec2:TerminateInstances",
        "Resource": "*"
    }]
}

```

* **Detail Breakdown:** Pehli statement ne saare kaam allow kiye, lekin doosri statement ne aakar khususi taur par `"ec2:TerminateInstances"` (server ko hamesha ke liye delete karna) par **Deny** laga diya. Nateeja yeh nikala ke user baqi sab kuch kar sakega, par server delete nahi kar payega.

#### Example 3: Faltu Statement Ka Nuksaan

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Deny",
        "Action": "ec2:*",
        "Resource": "*"
    },
    {
        "Effect": "Allow",
        "Action": "ec2:TerminateInstances",
        "Resource": "*"
    }]
}

```

* **Detail Breakdown:** Is policy mein pehle hi poori EC2 service ko `"Deny"` kar diya gaya hai. Iske baad niche lagaya gaya `"Allow"` bilkul be-asar (not crucial) ho jata hai, kyunki jab ek baar bada Deny lag gaya, toh doosri statement use kabhi bypass nahi kar sakti.

#### ARN (Amazon Resource Name) Aur Specific Resource Lockdown

AWS mein har ek cheez (jaise server, network, storage) ka apna ek unique identity address hota hai jise **ARN** kehte hain. **Figure 5.7** ke mutabaq, ARN ke paanch main hisse hote hain:
`arn:aws:ec2:us-east-1:878533158213:instance/i-3dd4f812`

1. **Partition:** `aws` (cloud ka aam hissa).
2. **Service:** `ec2` (jis service ka resource hai).
3. **Region:** `us-east-1` (jahan woh maujood hai).
4. **Account ID:** `878533158213` (12-digit ka account number).
5. **Resource:** `instance/i-3dd4f812` (asli resource ki ID).

Apne CLI terminal se apna 12-digit ka Account ID nikalne ki command yeh hai:

```bash
$ aws sts get-caller-identity --query "Account" --output text
111111111111

```

Agar aapko ARN pata ho, toh aap specific resource par lock laga sakte hain, jaise is policy mein user ko sirf ek hi specific server delete karne ki ijazat di gayi hai:

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": "ec2:TerminateInstances",
        "Resource": "arn:aws:ec2:us-east-1:111111111111:instance/i-0b5c991e026104db9"
    }]
}

```

#### Managed Policy vs Inline Policy (Farq Aur Warning)

* **Managed Policy:** Yeh woh aisi policies hoti hain jinhein aap aik se zyada users ya roles par baar-baar reuse kar sakte hain. Yeh do tarah ki hoti hain: **AWS Managed** (jo AWS khud bana kar manage karta hai jaise `AdministratorAccess`) aur **Customer Managed** (jo aap apni company ke hisab se khud banate hain).
* **Inline Policy:** Yeh aisi policy hoti hai jo kisi ek specific user ya role ke andar hi sili hui (embedded) hoti hai. Agar woh user delete hoga, toh policy bhi khatam ho jayegi. CloudFormation ke sath inhein manage karna bohot easy hota hai.

> ⚠️ **Security Warning:** AWS Managed policies ka bar-bar use karna security ke **Least-Privilege Principle** (kam se kam ijazat dene ka qanoon) ke khilaf chala jata hai. Kyunki AWS managed policies aam taur par resources mein `*` ka use karti hain. Isliye behtareen tareeqa yeh hai ke hum CloudFormation ke zariye apni **Inline Policies** khud likhein.

---

## Users for authentication and groups to organize users

Insaano ke liye hum **Username/Password** banate hain taake woh console mein ja sakein, aur softwares/CLI ke liye **Access Keys** use karte hain.

Abhi tak aap root user use kar rahe the, lekin ab aapko foran **IAM Users** par shift hona chahiye kyunki:

1. Har bande ka apna unique track aur login hoga.
2. Aap har bande ko sirf uske kaam ke mutabaq limited taqat de sakenge.

Kaam ko azaan karne ke liye hum pehle ek **Group** banate hain (jaise `admin`), us group par policy lagate hain, aur users ko us group ka member bana dete hain. Iska faida yeh hai ke agar kal ko hifazat ke liye hum admin se koi power chinna chahein, toh humein 50 alag-alag users ke paas nahi jana padega, hum sirf ek baar group ki policy badlein ge aur sab par automatic apply ho jayega.

Chaliye CLI se ek mukammal admin user aur group banate hain:

```bash
# 1. 'admin' naam ka ek central group banayein
$ aws iam create-group --group-name "admin"

# 2. Is group ke sath AWS ki AdministratorAccess managed policy attach kar dein
$ aws iam attach-group-policy --group-name "admin" \
  --policy-arn "arn:aws:iam::aws:policy/AdministratorAccess"

# 3. Ek naya user paida karein jiska naam 'myuser' ho
$ aws iam create-user --user-name "myuser"

# 4. Apne naye user ko admin group ka member bana dein
$ aws iam add-user-to-group --group-name "admin" --user-name "myuser"

# 5. Is user ke liye console login password set karein (Replace $Password with a strong password)
$ aws iam create-login-profile --user-name "myuser" --password '$Password'

```

#### Naye User Se Login Karne Ka Tarika

Ab aap aam root user wale page se login nahi kar sakte. IAM users ke liye link alag hota hai:
`https://$[accountId.signin.aws.amazon.com/console](https://accountId.signin.aws.amazon.com/console)` (Yahan `$accountId` ki jagah aap apna 12-digit number likhein ge).

---

## Enabling MFA for IAM users

Jaise root user par lagaya tha, waise hi apne naye bane hue `myuser` par bhi MFA on karna laazmi hai:

1. AWS Management Console mein **IAM** service open karein.
2. Left side bar mein **Users** par click karein.
3. Apne naye user **myuser** par click karein.
4. **Security Credentials** ke tab ko select karein.
5. **Assigned MFA Device** ke paas diye gaye **Manage** link par click karein.
6. Samne khulne wala wizard bilkul wahi hai jo humne root user ke liye chalaya tha, use follow kar ke scan karein.

> ⚠️ **Strict Critical Warnings:**
> * **Stop using root:** Aaj ke baad root user use karna bilkul band kar dein, hamesha `myuser` aur naye link se login karein!
> * **No Keys on EC2:** Kabhi bhi kisi IAM user ki Access Keys ko copy kar ke EC2 instance ke andar save mat karein!
> * **No Secrets in Git:** Apne program ke code ke andar kabhi bhi passwords ya keys likh kar GitHub ya repository par push na karein, hamesha **IAM Roles** ka use karein!
> 
> 

---

## Authenticating AWS resources with roles

Aam taur par hamare servers (EC2 instances) ko AWS ke baqi resources se baat karni parti hai, jaise:

* Apna data backup karne ke liye **S3 Bucket** mein file upload karna.
* Kaam khatam hone par paise bachane ke liye khud ko hi **Terminate/Stop** kar dena.
* Network (VPC) ki settings ko automatically tabdeel karna.

In kaamon ke liye server ko AWS API se baat karni parti hai. Agar hum wahan access keys likh kar chorenge, toh unki rotation (har thode din baad badalna) ek bura khwaab ban jayega aur keys leak hone ka khatra rahega. Iska modern hal **IAM Role** hai. Jab aap kisi EC2 instance par role lagate hain, toh AWS khud hi uske andar safe temporary access keys dalti aur badalti rehti hai.

#### Practical Project: Auto-Stopping EC2 Instance (Paise Bachane Ka System)

Hum ek aisi machine banayenge jo chalu hone ke 5 minute baad khud ko automatic band (`stop`) kar degi taake hamare paise fuzool mein zaya na hon. Iske liye hum Linux ki `at` command aur AWS CLI use karenge:

```bash
$ echo "aws ec2 stop-instances --instance-ids i-0b5c991e026104db9" | at now + 5 minutes

```

Lekin is command ko chalne ke liye instance ke paas khud ko rokne ki power (`ec2:StopInstances`) honi chahiye. Chaliye iska CloudFormation script dekhte hain:

```yaml
Role:
  Type: 'AWS::IAM::Role'
  Properties:
    AssumeRolePolicyDocument:
      Version: '2012-10-17'
      Statement:
        - Effect: Allow
          Principal:
            Service: 'ec2.amazonaws.com'    <--------- EC2 service ko is role ka main malik (Principal) banaya
          Action: 'sts:AssumeRole'          <--------- EC2 ko ijazat di ke woh yeh mukammal role dhaar sake
    ManagedPolicyArns:
      - 'arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore'
    Policies:
      - PolicyName: ec2
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
            - Effect: Allow
              Action: 'ec2:StopInstances'   <--------- Servers ko stop karne ki power di
              Resource: '*'                 <--------- Saare instances par...
              Condition:                    <--------- LEKIN sirf tab, jab niche wali condition poori ho!
                StringEquals:
                  'ec2:ResourceTag/aws:cloudformation:stack-id': !Ref 'AWS::StackId' <--------- Sirf un servers ko roke jo isi CloudFormation stack ka hissa hain.

```

* **Design Decision Explanation:** Yahan `Resource: "*"` isliye lagaya gaya kyunki jab stack ban raha hota hai, toh cyclic dependency ki wajah se role ko pehle se server ka exact ARN nahi pata hota. Iska tod humne ek sakht **Condition** laga kar nikala ke server sirf usi machine ko stop kar sakega jiske upar is specific CloudFormation Stack ki tag ID lagi hogi.

Ab is role ko direct server par lagane ke liye pehle ek **Instance Profile** (ek tarah ka lifafa) banana parta hai:

```yaml
InstanceProfile:
  Type: 'AWS::IAM::InstanceProfile'
  Properties:
    Roles:
      - !Ref Role    <--------- Hamare upar wale IAM Role ka reference

```

Aakhri step mein hum is Profile ko apne EC2 instance ke sath attach karte hain aur **UserData** ke andar modern token system (**IMDSv2**) ka use kar ke server ki apni ID nikalte hain aur auto-stop command schedule kar dete hain:

```yaml
Instance:
  Type: 'AWS::EC2::Instance'
  Properties:
    # [...]
    IamInstanceProfile: !Ref InstanceProfile  <--------- Role lifafe ko yahan jor diya
    UserData:
      'Fn::Base64': !Sub |
        #!/bin/bash -ex
        # 1. IMDSv2 ka secure temporary token haasil karein
        TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
        # 2. Us token ko use kar ke is chalne wale server ki exact Instance ID khinchein
        INSTANCEID=`curl -H "X-aws-ec2-metadata-token: $TOKEN" -s "http://169.254.169.254/latest/meta-data/instance-id"`
        # 3. 'at' command ke zariye parameter mein diye gaye waqt (${Lifetime}) baad auto-stop run karein
        echo "aws ec2 stop-instances --region ${AWS::Region} --instance-ids $INSTANCEID" | at now + ${Lifetime} minutes

```

---

### Cleaning up

> ⚠️ **Cost Warning:**
> Jab aap is demo ka practical test mukammal kar lein, toh CloudFormation console par ja kar apne `ec2-iam-role` stack ko **Delete** karna hargiz mat bhooliyega. Yad rakhein, server stop hone ke bawajood bhi uske sath jo network-attached storage (**EBS Volume**) hota hai, AWS uske paise barabar leta rehta hai. Account ko saaf rakhna hi behtareen security aur bachat hai!

---