# sing a relational database service: RDS

## This chapter covers

* **Launching and initializing relational databases with RDS:** Amazon RDS (Relational Database Service) ka istemal karte hue ek nayi relational database ko shuru (launch) aur tayar (initialize) karna.
* **Creating and restoring database snapshots:** Database ka aik point-in-time photo ya backup (Snapshot) lena aur agar kabhi data kharab ho jaye ya delete ho jaye toh us snapshot se database ko wapis pehle jaisa (restore) karna.
* **Setting up a highly available database:** Aisa database system tayar karna jo kabhi bhi band (down) na ho. Agar aik jagah koi masla aaye bhi, toh doosri jagah se database baghair rukaawat ke chalta rahe.
* **Monitoring database metrics:** Database ki sehat, speed, memory, aur CPU ka lagatar jaiza lena taake pata chal sake ke system sahi tarah kaam kar raha hai ya nahi.
* **Tweaking database performance:** Database ki setting aur speed ko behtar banana taake queries tezi se run hon aur application fast chale.

---

### Relational Databases aur AWS ka Ta'aruf

**WordPress** aik aisi mashhoor website banana wali application (Content Management System ya CMS) hai jo pure internet ki aik bohot bari taaddad ko chalati hai. Internet par maujood bohot sari doosri applications ki tarah, WordPress bhi apne tamam articles, comments, user accounts, aur doosri information ko mehfooz rakhne ke liye ek **Relational Database** ka istemal karta hai.

Relational databases ko structured data (yaani aisa data jo rows aur columns ki shakal mein saaf suthra rakha gaya ho) ko store karne aur dhoondne (query karne) ke liye sab se ziada reliable aur standard zariya mana jata hai. Bohot sari mashhoor applications **MySQL** jaise relational database systems par chalti hain.

#### Relational Databases kis cheez par focus karte hain? (ACID Property)

Relational databases ka sab se bada maqsad **Data Consistency** (data ka har halat mein sahi aur mehfooz rehna) hota hai. Ye databases **ACID** guarantees dete hain:

* **Atomicity (A - Aisa kaam jo poora ho ya bilkul na ho):** Transaction ya toh poori tarah kamyab hogi ya phir bilkul nahi hogi. *Misaal ke taur par:* Agar aap bank app se apne dost ko Rs. 100 bhejte hain, toh aap ke account se paise katna aur dost ke account mein paise jama hona—dono kaam hone chahiye. Agar beech mein net kat jaye, toh paise wapis aap ke account mein hi rahenge, adhe raste mein ghayab nahi honge.
* **Consistency (C - Rules ka hamesha follow hona):** Database mein sirf wahi data save hoga jo us ke banaye gaye qawaneen (rules) par poora utarta ho.
* **Isolation (I - Aik doosre se alag rehna):** Agar aik waqt mein hazaron log database ko istemal kar rahe hain, toh aik bande ki transaction doosre bande ke kaam mein rukawat ya garbar paida nahi karegi.
* **Durability (D - Data ka hamesha ke liye mehfooz hona):** Ek baar jab transaction complete ho jaye aur data save ho jaye, toh chahe light chali jaye ya server crash ho jaye, data zaye (lost) nahi hoga.

Aise sakht usool accounting applications, bank accounts, aur e-commerce stores ke liye bohot zaroori hotay hain.

---

### AWS Par Relational Database Chalane Ke Do Raaste

Agar aap AWS cloud par relational database chalana chahte hain, toh aap ke paas do raste (options) hote hain:

1. **Amazon RDS (Managed Service):** AWS ki taraf se bani banayi database service istemal karein jahan hardware, updates, aur backups ki tension AWS khud leta hai.
2. **Self-Hosted (Virtual Machines par khud manage karna):** EC2 Virtual Machines le kar un par khud MySQL ya koi bhi database manual install karein aur khud manage karein.

#### Amazon RDS Kya Hai?

Amazon Relational Database Service (Amazon RDS) aik ready-to-use managed database service hai. Ye aam taur par istemal hone wale tamam bade database engines ko support karti hai:

* **PostgreSQL**
* **MySQL**
* **MariaDB**
* **Oracle Database**
* **Microsoft SQL Server**
* **Amazon Aurora:** Ye AWS ka apna cloud-native database engine hai jo MySQL aur PostgreSQL ke sath 100% compatible hai aur bohot ziada fast aur scalable hai.

Agar aap ki application pehle se in mein se koi database engine istemal kar rahi hai, toh RDS par shift hona bohot aasan hai. Sab se ziada dhyan data ko purane server se RDS par shift (migrate) karne mein dena hota hai.

---

### Managed Service RDS vs. Self-hosted on virtual machines

AWS mein **Managed Service** ka matlab hai ke service dene wala (AWS) system ko chalane, updates lagane, aur hardware ki dekh-bhal karne ki zimmedari leta hai.

Below diye gaye table mein RDS aur Virtual Machines (EC2) par khud database chalane ka mwaazna (comparison) kiya gaya hai:

| Comparison Feature | Amazon RDS | Self-hosted on virtual machines |
| --- | --- | --- |
| **Cost for AWS services** | Zyada hota hai kyunke RDS ki cost virtual machines (EC2) se zyada hoti hai. | Kam hota hai kyunke virtual machines (EC2) RDS ke muqablay mein sasti hoti hain. |
| **Total cost of ownership** | Kam hota hai kyunke operating costs bohot sare customers ke darmiyan split ho jati hain. | Bohat zyada hota hai kyunke apne database ko manage karne ke liye aap ko apni khud ki manpower ki zaroorat parti hai. |
| **Quality** | Managed service ki zimmedari AWS ke professionals ki hoti hai. | Aap ko professionals ki team banani parti hai aur khud quality control implement karna parta hai. |
| **Flexibility** | Zyada hoti hai, kyunke aap relational database system aur ziyadatar configuration parameters chun sakte hain. | Is se bhi zyada hoti hai, kyunke aap virtual machines par install kiye gaye relational database system ke har hissay ko control kar sakte hain. |

#### Is Table Ka Asan Matlab:

1. **AWS Service Bill (Service ki qeemat):** RDS ka monthly bill aik aam EC2 Virtual Machine se thoda ziada hota hai kyunke AWS aap ko auto-backup, security patches, aur management ki suhulat de raha hai.
2. **Total Cost of Ownership (TCO - Kul Kharcha):** Jab aap khud Virtual Machine par database chalate hain, toh aap ko system administrators aur database experts (DBAs) ko bari salaries deni parti hain taake wo database ko 24/7 manage karein. RDS mein AWS ye saara kaam khud karta hai, is liye overall kharcha (TCO) RDS par kam aata hai.
3. **Quality (Kaam ki behtari):** RDS ko AWS ke sab se experienced engineers manage kar rahe hote hain, jabke self-hosted mein aap ko khud experts dhoond kar team banani parti hai.
4. **Flexibility (Azaadi):** Self-hosted mein aap ke paas OS level ka full control hota hai, jabke RDS mein aap ko Operating System ka access nahi milta, lekin aap database ke tamam zaroori parameters ko asani se change kar sakte hain.

Virtual machines par apna database khareed kar zero se set karna bohot ziada waqt aur tajarba (know-how) maangta hai. Is liye AWS hamesha recommend karta hai ke **Amazon RDS** ka istemal kiya jaye taake aap ke kaam ki quality behtar ho aur operational kharcha kam ho.

---

## Figure 10.1 The company’s blogging infrastructure consists of two load-balanced web servers running WordPress and a MySQL database server.

Figure 10.1 mein ek mukammal production-ready WordPress architecture ko dikhaya gaya hai. Is architecture ke har aik hissaye aur request flow ko step-by-step samajhte hain:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

#### Request Ka Safar (Step-by-Step Flow):

1. **Step 1 (User Request):** User apne web browser se website open karta hai (HTTP Request bhejta hai). Ye request sab se pehle **Load Balancer** ke paas jaati hai.
2. **Step 2 (Traffic Distribution):** Load Balancer (ELB) us request ko pakad kar peeche maujood do Virtual Machines (EC2 instances) ke darmiyan barabar baant (distribute kar) deta hai.
3. **Step 3 (Processing & Storage Connection):** Har Virtual Machine par Web Server (WordPress + PHP) chal raha hota hai. Ye web servers do jagah connect hote hain:
* **Network Filesystem (Amazon EFS):** Tamam images, media files, aur uploaded files ko store aur access karne ke liye `NFSv4.1` protocol ke zariye EFS se connect hote hain. Is se dono virtual machines aik hi shared filesystem ko access karti hain.
* **Database (Amazon RDS):** Website ke articles, comments, user IDs, aur passwords ko query/store karne ke liye managed MySQL database (RDS) se connect hote hain.



#### Component Breakdown (Diagram Mein Maujood Har Component Ki Detail):

* **Elastic Load Balancing (ELB):** Ye AWS ki managed load balancer service hai jo **Fault Tolerant** hai (yaani agar traffic bohot ziada aa jaye ya koi issue ho jaye, toh ye khud ko sambhal leti hai).
* **Virtual Machines (EC2):** Infrastructure as a Service (IaaS) jo Linux ya Windows OS provide karti hai. Yahan par WordPress aur PHP code execute hota hai.
* **Network Filesystem (EFS):** Elastic File System multiple virtual machines ko ek sath files store aur read karne ki ijazat deta hai.
* **Amazon RDS (MySQL):** Managed MySQL database jahan data store hota hai. AWS is ke backups, security patches, aur replication ki zimmedari khud leta hai.
* **Security Groups (Firewalls):** Har component (Load Balancer, Web Servers, EFS, RDS) ke aage **Firewall** ka icon bana hai. Ye Security Groups hain jo ye tay karte hain ke kaun sa traffic andar aa sakta hai aur kaun sa bahar ja sakta hai (maslan: RDS sirf EC2 web servers se traffic accept karega, direct public internet se nahi).

---

> **Not all examples are covered by the Free Tier**
> Is chapter mein di gayi tamam misalein AWS Free Tier ke andar shamil nahi hain (kuch features ke extra charges lag sakte hain). Jab bhi koi aisa step aayega jiss par paise lag sakte hon, wahan warning message dikhaya jayega.
> **Paisa Bachane Ka Tarika:** Agar aap ne is book ke liye bilkul naya AWS account banaya hai aur aap is chapter ki saari exercises ko 2-3 dino mein mukammal kar ke saare resources ko delete (clean up) kar dete hain, toh aap ko koi extra charge nahi parega. Is liye zaroori hai ke aap is chapter ko kuch hi dino mein khatam karein aur aakhir mein saare banaye gaye resources ko delete kar dein.

---

## Starting a MySQL database

Aayein sab se pehle samajhte hain ke jab aap AWS par WordPress jaisi application chalate hain toh database ka kya kirdar hota hai. Is chapter mein hum ziada tar **MySQL** database par baat karenge jo RDS provide karta hai. Lekin achhi baat yeh hai ke jo baatein aap yahan seekhenge, wo bilkul waisi hi doosre database engines par bhi apply hoti hain—jaise ke Amazon Aurora, PostgreSQL, MariaDB, Oracle, aur Microsoft SQL Server.

### Purana Tarika vs AWS RDS (Ek Aasan Misaal)

Jab aap WordPress ki official tutorial dekhte hain, toh pehla step MySQL database ko set up karna hota hai.

* **Purana Tarika (Self-Hosted/Same Machine):** Pehle log kya karte the ke jis aik akeli Virtual Machine (EC2 Instance) par web server/website chal rahi hoti thi, usi machine ke andar MySQL database bhi install kar dete the.
* **Is mein masla kya tha?**
1. **Single Point of Failure (SPOF):** Is ka matlab hai ke aap ke poore system mein kamzori ki aik hi kadi hai. Agar wo aik Virtual Machine crash ho gayi ya band ho gayi, toh aap ki website aur database **dono aik sath thapp (down)** ho jayenge.
2. **Mushkil Management:** Database ka rozaana backup lena, usay kisi doosri jagah mehfooz rakhna, aur kharab hone par wapis sahi karna bohot ziada mushkil aur sar-dardi wala kaam hota tha.




* **Naya Tarika (AWS RDS Managed Database):** Is maslay ko hal karne ke liye hum AWS RDS ka fully managed MySQL database istemal karte hain.
* RDS aik aise hoshiyar aur zimmedar helper ki tarah hai jo database ke saare mushkil kaam khud sambhalti hai.
* AWS khud ba khud aap ke database ka **Backup** leta hai aur usay wapis pehle jaisa karne (**Restore**) ki suhulat deta hai.
* AWS aap ke database ko ek ke bajaye **do mukhtalif Data Centers (Multi-AZ)** mein baant kar rakhta hai. Agar aik Data Center mein koi masla (jaise light jana ya server crash) aa bhi jaye, toh system khud ba khud doosre data center se chalne lagta hai (Automatic Recovery).



---

## Launching a WordPress platform with an RDS database

AWS par RDS database launch karne ke mukhya taur par **2 aasan steps** hotay hain:

1. **Database Instance Launch Karna:** Cloud par database chalane ke liye aik dedicated virtual engine/server tayar karna.
2. **Application ko Endpoint se Connect Karna:** Application (WordPress) ko database ka address (Endpoint URL) dena taake wo data save aur read kar sake.

Is setup ko banane ke liye hum AWS **CloudFormation** ka istemal karenge. CloudFormation aik automatic script (template) hoti hai jo khud hi saara infrastructure tayar kar deti hai.

### Infrastructure Launch Karne Ki Command

Neeche di gayi command ko execute kar ke hum CloudFormation ke zariye RDS MySQL Database aur Web Server ka poora setup aik sath launch karte hain:

```bash
aws cloudformation create-stack --stack-name wordpress --template-url \
https://s3.amazonaws.com/awsinaction-code3/chapter10/template.yaml \
--parameters "ParameterKey=WordpressAdminPassword,ParameterValue=test1234" \
--capabilities CAPABILITY_IAM

```

#### Is Command Ki Har Ek Line Aur Parameter Ka Matlab:

* `aws cloudformation create-stack`: AWS CLI ko hukum diya ja raha hai ke CloudFormation ke zariye aik naya "Stack" (resources ka aik poora group) banaye.
* `--stack-name wordpress`: Is poore setup/stack ka naam "wordpress" rakha gaya hai.
* `--template-url [https://s3.amazonaws.com/.../template.yaml](https://s3.amazonaws.com/.../template.yaml)`: CloudFormation ko bataya ja raha hai ke blueprint (yaml file) AWS S3 bucket par parhi hui hai, wahan se parh kar saare resources banao.
* `--parameters "ParameterKey=WordpressAdminPassword,ParameterValue=test1234"`: Template ko WordPress Admin ka password (`test1234`) pass kiya ja raha hai.
* `--capabilities CAPABILITY_IAM`: Kyunke yeh template security permissions (IAM Roles) bhi banaye gi, is liye hum AWS ko pehle se ijazat (capability) de rahe hain ke wo IAM resources bana sake.

Command run karne ke baad stack banne mein kuch minute lagte hain. Tab tak aayein dekhte hain ke RDS database ke kon kon se main attributes hotay hain.

---

### Table 10.2 Attributes needed to connect to an RDS database

| Attribute | Description (Roman Urdu) |
| --- | --- |
| **AllocatedStorage** | Aap ke database ka storage size GBs mein. |
| **DBInstanceClass** | Neeche chalne wali virtual machine ka size (jise instance type bhi kehte hain). |
| **Engine** | Woh database engine jo aap istemal karna chahte hain (jaise Aurora, PostgreSQL, MySQL, MariaDB, Oracle Database, ya Microsoft SQL Server). |
| **DBName** | Database ke liye identifier ya naam. |
| **MasterUsername** | Admin user ka naam. |
| **MasterUserPassword** | Admin user ka password. |

#### Attributes Ka Aasan Matlab:

* **AllocatedStorage:** Database ke paas data save karne ke liye kitni jagah (hard disk space) hogi (maslan 5 GB).
* **DBInstanceClass:** Database ki taqat aur speed kitni hogi (jaise CPU aur RAM). High-traffic systems mein bade instance class use hotay hain.
* **Engine:** Aap kaun sa database software chalana chahte hain (maslan MySQL).
* **DBName:** Wo specific folder ya database jo engine ke andar bane ga.
* **MasterUsername & MasterUserPassword:** Super-admin user ki login details jo database par poora control rakhta hai.

---

### Security Concept: Public Access vs VPC Access (Ahem Design Decision)

AWS mein database ko **Publicly Accessible** (Internet se direct accessible) banaya ja sakta hai, lekin yeh **hargiz recommended nahi hai**.

* **Risk:** Agar aap database ko public kar dein ge, toh duniya ka koi bhi hacker internet se aap ke database par attacks kar sakta hai.
* **Best Practice:** Database ko HAMESHA **VPC (Virtual Private Cloud)** ke andar chupa kar rakha jata hai. Internet se direct access mukammal block hoti hai. Database tak sirf wahi EC2 Instance (Web Server) pohanch sakta hai jo usi VPC ke andar chal raha ho.

---

### Listing 10.1 Excerpt from the CloudFormation template for setting up an RDS database

Neeche CloudFormation YAML template ka wo hissa diya gaya hai jo RDS Database aur us ki Security tayar karta hai:

```yaml
Resources:
  # [...]
  DatabaseSecurityGroup: # Database instance ke liye security group, jo web servers ke liye MySQL default port par aane wale traffic ki ijazat deta hai
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: 'awsinaction-db-sg'
      VpcId: !Ref VPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 3306 # MySQL ka default port 3306 hai
          ToPort: 3306
          SourceSecurityGroupId: !Ref WebServerSecurityGroup # Web server chalane wale EC2 instances ki security group ka reference deta hai

  Database: # Amazon RDS ke sath aik database instance banata hai
    Type: 'AWS::RDS::DBInstance'
    DeletionPolicy: Delete
    Properties:
      AllocatedStorage: 5 # Ye database 5 GB ki storage provide karta hai
      BackupRetentionPeriod: 0 # Backups ko disable karta hai. (Production mein isay on kar dein)
      DBInstanceClass: 'db.t2.micro' # Database instance ka size t2.micro hai, jo sab se chhota available size hai
      DBName: wordpress # Wordpress ke naam se aik default database banata hai
      Engine: MySQL # Database engine ke tor par MySQL istemal karta hai
      MasterUsername: wordpress # MySQL database ke admin user ka username
      MasterUserPassword: wordpress # MySQL database ke admin user ka password
      VPCSecurityGroups:
        - !Sub ${DatabaseSecurityGroup.GroupId} # Database instance ke liye security group ka reference deta hai
      DBSubnetGroupName: !Ref DBSubnetGroup # Un subnets ko define karta hai jin mein RDS database instance launch hoga
    DependsOn: VPCGatewayAttachment

  DBSubnetGroup: # Subnet group ...
    Type: 'AWS::RDS::DBSubnetGroup'
    Properties:
      Description: DB subnet group
      SubnetIds:
        - Ref: SubnetA
        - Ref: SubnetB # Subnet A aur B par mushtamil hai, isliye RDS in subnets ke darmiyan database instances ko distribute karega

```

#### Code Ka Mukammal Breakdown:

1. **`DatabaseSecurityGroup` (Firewall Rule):**
* **`Type: 'AWS::EC2::SecurityGroup'`:** Yeh AWS ko bata raha hai ke ek virtual firewall banao.
* **`IpProtocol: tcp` & `FromPort: 3306 / ToPort: 3306`:** Port 3306 ko khol raha hai kyunke MySQL hamesha port 3306 par baat karta hai.
* **`SourceSecurityGroupId: !Ref WebServerSecurityGroup`:** Yeh sab se zaroori security rule hai! Yeh kehta hai ke port 3306 par **sirf aur sirf** hamare Web Server ki taraf se aane wali requests ko andar aane do, baaqi sab ko block kar do.


2. **`Database` (RDS Instance Component):**
* **`Type: 'AWS::RDS::DBInstance'`:** Amazon RDS database instance banane ka component.
* **`DeletionPolicy: Delete`:** Jab hum CloudFormation stack ko delete karenge, toh yeh database bhi delete ho jayega.
* **`AllocatedStorage: 5`:** Is database ko shuruat mein 5 Gigabytes (GB) ki disk space di ja rahi hai.
* **`BackupRetentionPeriod: 0`:** Backups ko **0 days** par set karke temporary disable kiya gaya hai (kyunke yeh seekhne/testing ke liye hai). Production systems mein isay hamesha `7` ya `30` din set kiya jata hai.
* **`DBInstanceClass: 'db.t2.micro'`:** Hardware ka size `db.t2.micro` hai. *(Note: Modern AWS environments mein t3.micro ya t4g.micro instance types use hote hain, lekin concept wahi hai ke yeh sab se sasta aur chhota testing size hai).*
* **`DBName: wordpress`:** Automatic 'wordpress' naam ka database create kar dega.
* **`Engine: MySQL`:** Database software MySQL hoga.
* **`MasterUsername: wordpress` & `MasterUserPassword: wordpress`:** Admin credentials.
* **`VPCSecurityGroups`:** Is database par upar banaya gaya firewall (`DatabaseSecurityGroup`) lago kar dega.
* **`DBSubnetGroupName: !Ref DBSubnetGroup`:** Yeh batata hai ke database VPC ke kin subnets mein chalega.
* **`DependsOn: VPCGatewayAttachment`:** Tab tak database mat banao jab tak VPC ka network rasta (Gateway) mukammal na ban jaye.


3. **`DBSubnetGroup` (High Availability Ki Buniyaad):**
* **`SubnetIds: [- Ref: SubnetA, - Ref: SubnetB]`:** Yeh database ko do mukhtalif Availability Zones (Datacenters) se jorta hai. Is se AWS ko pata chalta hai ke agar Multi-AZ setup banana ho toh database ko in do alag alag datacenters mein phailana hai.



---

### CloudFormation Stack Status Check Karna

Stack banne ke baad hum yeh command chala kar check karte hain ke kya poora infrastructure ban chuka hai ya nahi:

```bash
aws cloudformation describe-stacks --stack-name wordpress

```

#### Listing 10.2 Checking the state of the CloudFormation stack

Command ka output JSON format mein aisa dikhta hai:

```json
$ aws cloudformation describe-stacks --stack-name wordpress
{
  "Stacks": [{
    "StackId": "[...]",
    "Description": "AWS in Action: chapter 10",
    "Parameters": [...],
    "Tags": [],
    "Outputs": [
      {
        "Description": "WordPress URL",
        "OutputKey": "URL",
        "OutputValue": "http://[...].us-east-1.elb.amazonaws.com"
      }
    ],
    "CreationTime": "2017-10-19T07:12:28.694Z",
    "StackName": "wordpress",
    "NotificationARNs": [],
    "StackStatus": "CREATE_COMPLETE",
    "DisableRollback": false
  }]
}

```

#### JSON Output Ka Line-by-Line Breakdown:

* **`"StackName": "wordpress"`:** Hamare stack ka naam.
* **`"StackStatus": "CREATE_COMPLETE"`:** **Sab se ahem point!** Is ka matlab hai ke saara infrastructure (EC2, Load Balancer, Security Groups, aur RDS Database) kamyabi se ban chuka hai. (Agar yahan `CREATE_IN_PROGRESS` likha aaye toh aap ko thoda mazeed intezar karna hoga).
* **`"Outputs"`:** Job khatam hone ke baad CloudFormation jo kaam ki details return karta hai.
* **`"OutputKey": "URL"`** aur **`"OutputValue": "http://[...].us-east-1.elb.amazonaws.com"`:** Yeh Load Balancer ka public web address hai. Is URL ko copy karke browser mein open karenge toh aap ke samne live WordPress website khul jayegi jo peeche RDS MySQL database se judi hui hai.


* **`"DisableRollback": false`:** Agar stack banate waqt koi error aata toh CloudFormation automatic sab kuch saaf (rollback) kar deta.

---

### Database Chalane Ke Baad Admin Ki Zimmedariyan

Amazon RDS aik **Managed Service** hai, is liye OS updates, security patching, aur hardware maintenance sab AWS khud sambhalta hai. Database launch hone ke baad **System Administrator** ko sirf do main kaam karne hote hain:

1. **Storage ko Monitor Karna:** Yeh dekhna ke database ki storage (5 GB) bhar toh nahi rahi. Jab space kam parne lage toh Allocated Storage ko barha dena chahiye *(modern AWS setups mein auto-scaling enable ki ja sakti hai jo storage khud barha deti hai)*.
2. **Performance ko Monitor Karna:** Performance par nazar rakhna taake agar CPU usage ziada ho jaye ya Read/Write Speed (I/O) slow hone lage, toh Database Instance Class ko upgrade (bada) kiya ja sake.

---

## Exploring an RDS database instance with a MySQL engine

Previous section mein banaye gaye CloudFormation stack ne hamare liye MySQL engine ke sath ek **RDS database instance** tayar kar diya hai.

Jab bhi hum koi database banate hain, toh sab se pehla sawal yeh hota hai ke hamari application (jaise WordPress) us database ke sath baat kaise karegi? Data daalne, dhoondne, badalne ya delete karne ke liye application **SQL Queries** bhejti hai.

#### Endpoint Kya Hota Hai? (Bacho Ki Tarah Aasan Misaal)

> **Aasan Misaal:** Aise samjhein ke RDS database aap ke ghar ke andar rakha hua ek **Tijori (Safe)** hai. Is tijori tak pohanchne ke liye aap ko **Ghar ka Pata (Address)** aur **Main Darwaza (Port Number)** chahiye hota hai. Cloud ki duniya mein is poore patay (Address + Port) ko **Endpoint** kehte hain.

Jab WordPress ko database se saara data mangwana hota hai, toh wo SQL query bhejta hai:
`SELECT * FROM table;`

* **`SELECT`:** Dhoond kar laao ya nikalo.
* **`*` (Asterisk):** Saara ka saara data (tamam columns aur rows).
* **`FROM table`:** Is khas table (register/list) mein se.

---

### Command Line Breakdown: RDS Endpoint Ki Maloomat Nikalna

Neeche di gayi AWS CLI command ke zariye hum apne launched RDS database ka Endpoint address aur port pata karte hain:

```bash
aws rds describe-db-instances --query "DBInstances[0].Endpoint"

```

#### Command Ka Line-by-Line Breakdown:

* **`aws rds`:** AWS CLI ko hukum diya ja raha hai ke hum **Amazon RDS** service se baat karna chahte hain.
* **`describe-db-instances`:** Is command se AWS account mein chalne wale tamam RDS database instances ki mukammal tafseel (details) mangwai jati hain.
* **`--query "DBInstances[0].Endpoint"`:** Kyunke `describe-db-instances` ka response bohot bada hota hai, is liye hum query filter laga kar keh rahe hain ke: *"Mujhe baaqi saari details nahi chahiye, sirf pehle database (`DBInstances[0]`) ki `Endpoint` waali information dikhao."*

---

### JSON Output Ka Line-by-Line Breakdown

Jab aap yeh command chalate hain, toh AWS aap ko yeh JSON output deta hai:

```json
{
  "HostedZoneId": "Z2R2ITUGPM61AM",
  "Port": 3306,
  "Address": "wdwcoq2o8digyr.cqrxioeaavmf.us-east-1.rds.amazonaws.com"
}

```

#### Output Ki Har Ek Line Ka Matlab:

* **`"HostedZoneId": "Z2R2ITUGPM61AM"`:** Yeh AWS Amazon Route 53 (DNS Service) ka internal ID hai. AWS piche background mein is ID ko istemal karta hai taake is lambe web address ko network IP address mein connect kar sake.
* **`"Port": 3306"`:** **MySQL ka default Darwaza (Port Number)**. Computer networks par har service alag port par baat karti hai. MySQL hamesha port `3306` par hi listen karta hai.
* **`"Address": "wdwcoq2o8digyr.cqrxioeaavmf.us-east-1.rds.amazonaws.com"`:** Yeh aap ke database ka **Unique Domain Name (Address)** hai. WordPress ya kisi bhi client application mein hum IP address dene ke bajaye yeh DNS Address daalte hain.

---

## Pricing for Amazon RDS

RDS database ab chal raha hai, lekin is ka kharcha (cost) kitna aayega?

AWS par WordPress chalane ki kul cost hum pehle samajh chuke hain, lekin specifically RDS database ki pricing 2 mukhya chizon par depend karti hai:

1. **Underlying Virtual Machine ka Size (DB Instance Class):** Database ke piche jo server chal raha hai us mein CPU aur RAM kitni hai.
2. **Allocated Storage Ka Amount Aur Type:** Aap ne database ko kitni hard disk space (Gigabytes mein SSD storage) di hai aur us ki type kya hai.

---

### Plain EC2 vs Amazon RDS: Trade-off Aur Cost Ka Mawazna

| Database Option | Hourly Cost | Operational Effort (Mehnat) | DBA Requirements |
| --- | --- | --- | --- |
| **Plain EC2 VM (Self-Hosted)** | Kam (Sasta) | Bohat Zyada (Manual Management) | Dedicated Experts/DBAs chahiye |
| **Amazon RDS (Managed)** | Thodi Zyada | Zero (AWS Automate Karta hai) | Dedicated DBA ki zaroorat nahi |

#### RDS Ke Extra Paise Dena Kyun Faida-mand Hai?

Aam EC2 instance ki nisbat RDS ka hourly rate thoda ziada hota hai. Lekin RDS par extra paise dena bilkul **worth it** hai, kyunke agar aap EC2 par khud database chalayein toh aap ko **Database Administrator (DBA)** ke ye saare mushkil aur tiring kaam khud karne parenge:

* **Installation:** Software ko khud download aur configure karna.
* **Patching:** Security updates aur Operating System patches lagana.
* **Upgrades:** Database software ko naye versions par upgrade karna.
* **Migration:** Data ko ek jagah se doosri jagah safely shift karna.
* **Backups:** Rozaana aur har ghante ke backups khud manage karna.
* **Recovery:** Server crash hone par data ko dobara wapis lana.

RDS mein yeh tamam kaam AWS ka automated system khud ba khud background mein sambhal leta hai, jiss se aap ka waqt aur manpower ka kharcha dono bachte hain.

---

### Table 10.3 Monthly costs for a medium-sized RDS instance

Neeche ek medium-sized RDS database instance ki mahana (monthly) cost ki misaal di gayi hai (ye rates High Availability / Standby setup ke sath US East N. Virginia region ke hain):

| Description | Monthly price |
| --- | --- |
| **Database instance db.t4g.medium** | $94.17 USD |
| **50 GB general purpose (SSD) storage** | $11.50 USD |
| **Database snapshots ke liye mazeed storage (100 GB)** | $9.50 USD |
| **Kul (Total)** | $115.17 USD |

#### Table Ke Har Point Ki Detail Explanation:

* **Database instance db.t4g.medium ($94.17 USD):**
* Yeh RDS ka medium-sized server hai jo ARM-based AWS Graviton architecture par chalta hai (`t4g`).
* Yeh server 24/7 poora mahina chalne ke **$94.17 USD** leta hai. Is mein Standby instance ki cost bhi shamil hoti hai jo backup ke taur par doosre datacenter mein ready rehta hai.


* **50 GB general purpose (SSD) storage ($11.50 USD):**
* Database ki actual tables, text, aur rows ko save karne ke liye fast Solid State Drive (SSD) di jati hai.
* 50 GB storage ki mahana qeemat **$11.50 USD** banti hai.


* **Database snapshots ke liye mazeed storage (100 GB) ($9.50 USD):**
* AWS aap ke database ke jo automatic backups (Snapshots) leta hai, un backups ko Cloud par mehfooz rakhne ke liye 100 GB extra storage ka kharcha **$9.50 USD** aata hai.


* **Kul (Total) ($115.17 USD):**
* In teeno components ko milakar ek reliable, professional, aur high-availability database chalane ka kul mahana kharcha taqreeban **$115.17 USD** aata hai.


---