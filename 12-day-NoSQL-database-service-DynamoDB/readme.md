# Programming for the NoSQL database service: DynamoDB

Is chapter mein hum Amazon Web Services (AWS) ki sab se mashhoor NoSQL database service **DynamoDB** ke baare mein seekhenge. Pehle hum samajhte hain ke is chapter mein hum kya kya cover karne waale hain:

## This chapter covers

* **Advantages and disadvantages of the NoSQL service, DynamoDB:** DynamoDB istemal karne ke kya fayde hain aur kya nuksanat hain.
* **Creating tables and storing data:** DynamoDB mein tables kaise banaye jaate hain aur un mein data kaise store (save) kiya jata hai.
* **Adding secondary indexes to optimize data retrieval:** Data ko tezi se dhoondne (search karne) ke liye Secondary Indexes kaise add kiye jaate hain.
* **Designing a data model optimized for a key-value database:** Key-Value database ke mutabiq data ka structure (model) kaise design kiya jata hai.
* **Optimizing costs by choosing the best fitting capacity mode and storage type:** Apne kharcheon (costs) ko kam se kam rakhne ke liye sahi Capacity Mode (jaise On-Demand ya Provisioned) aur Storage Type chunna.

---

### Database Ko Scale Karne Ki Zarurat Kyun Parti Hai?

Is baat ko ek bilkul aasan misal se samajhte hain:

Socho aap ke paas ek **Warehouse (Bada Godown)** hai. Is godown mein daily kitna samaan aaya aur kitna gaya, yeh sab ek computer application record karti hai, aur wo application sara data ek **Database** mein save karti hai.

* Jab godown mein kam samaan hoga, toh kam log system istemal karenge aur database araam se kaam karega.
* Lekin jab business barhega aur hazaron items roz aayenge-jaayenge, toh database par load bohot zyada ho jayega.
* Is se database **slow (latency high)** ho jayega aur kam rukne lagega.

Is masley ko hal karne ke liye humein database ko **Scale (bada/kabootar ki tarah failana)** karna padta hai. Scale karne ke **2 tarike** hotay hain:

1. **Vertically (Ooper Ki Taraf Scale Karna):**
* **Simple Matlab:** Apne chalte hue ek hi computer/server mein zyada power daal dena. E.g., RAM barha dena, CPU zyaada tezz lagana, ya fast SSD storage laga dena.
* **Kharabi (Trade-off):** Yeh tarika shuru mein aasan lagta hai lekin bohot **mehanga** hota hai! Ek limit ke baad market mein itna shandar/bada computer milna hi band ho jata hai ke aap usay mazeed upgrade kar sakein.


2. **Horizontally (Sidhe Bazu Ki Taraf Scale Karna):**
* **Simple Matlab:** Ek computer par sara bojh daalne ke bajaye us ke sath 2, 3 ya hazaron aur chote chote computers (Nodes) jod dena. In sab computers ke group ko **Database Cluster** kehte hain.



---

### Traditional Relational Database Ko Horizontally Scale Karna Kyun Mushkil Hai?

Purane zamane ke Relational Databases (jaise MySQL, PostgreSQL) ko Horizontally scale karna bohot mushkil kaam hai. Is ki wajah hoti hai **ACID guarantees** (Atomicity, Consistency, Isolation, Durability)—yani data bilkul sahi aur accurate rahe, koi galti na ho.

ACID guarantees ko barkarar rakhne ke liye cluster ke sabhi computers (nodes) ko aapas mein ek doosre se har waqt poochhna aur baat karni parti hai. Is pooch-gach ko hum **Two-Phase Commit** kehte hain.

---

#### Figure 12.1 Detailed Breakdown (Two-Phase Commit Protocol)

Aap diye gaye **Figure 12.1** ko dekhein. Is mein dikhaya gaya hai ke jab 2 Nodes (computers) aapas mein data sync karte hain toh kitna communication overhead hota hai:

<div align="center">
  <img src="./images/01.png" width="600"/>
</div>

* **Step 1:** Ek user ne Database Cluster ko data change karne ki request bheji (jaise data `INSERT`, `UPDATE`, ya `DELETE` karna). Yeh request ek main **Coordinator** computer ke paas jati hai.
* **Step 2 (Phase 1 - Prepare):** Coordinator dono computers (**Node 1** aur **Node 2**) ko ek "Commit Request" bhejta hai aur poochhta hai: *"Kya tum is data ko safe tarike se save kar sakte ho?"*
* **Step 3:** **Node 1** check karta hai aur Coordinator ko jawab (Acknowledge) bhejta hai ke *"Haan, main save kar sakta hoon."* Jab Node 1 haan keh de, toh wo apna waada tod nahi sakta.
* **Step 4:** **Node 2** bhi check karta hai aur Coordinator ko apna jawab bhejta hai ke *"Haan, main bhi tayar hoon."*
* **Step 5 (Phase 2 - Commit):** Coordinator dekhtah hai ke dono Nodes ne "HAAN" kaha hai, toh wo dono Nodes ko final order bhejta hai: *"Chalo ab finally data write (save) kar do!"*
* **Step 6:** **Node 1** aur **Node 2** dono exact ek hi time par data ko hard drive mein permanently save kar dete hain. Is step par koi galti ya failure allow nahi hota.

> **Sab Se Bada Masla (Trade-off):**
> Jaise jaise aap cluster mein mazeed computers (Nodes) add karenge (Node 3, Node 4, Node 10...), un sab ko aapas mein ek doosre se poochne aur confirm karne mein itna zyaada time lag jayega ke aap ka database tezz hone ke bajaye **bohot zyaada slow** ho jayega!

---

### NoSQL Aur DynamoDB Ka wajood

Is slow-down ke masley ko hal karne ke liye **NoSQL Databases** banaye gaye. NoSQL databases ACID guarantees ke sakht usoolon ko thoda naram (relax) kar dete hain taake hazaron computers ek sath mil kar tezi se kaam kar sakein.

#### NoSQL Database Ki 4 Badi Kismein (Types):

1. **Document Store** (E.g., MongoDB, AWS DocumentDB)
2. **Graph Store** (E.g., AWS Neptune)
3. **Columnar Store** (E.g., AWS Keyspaces)
4. **Key-Value Store** (E.g., AWS DynamoDB)

---

### AWS DynamoDB Kya Hai?

**Amazon DynamoDB** AWS ki taraf se di gayi ek NoSQL **Key-Value Store** service hai (jis mein Documents ka support bhi shamil hai).

* **Key-Value Store Ka Matlab:** Yeh bilkul ek Python Dictionary ya **Hash Table** ki tarah kaam karta hai. Har data object ki ek unique **Key** (ID) hoti hai, aur us Key ke zariye aap us ki poori **Value** (Data) haasil kar lete hain.
* **Fully Managed Service:** Is ka matlab yeh hai ke aap ko koi physical server, operating system, ya database software install ya maintain nahi karna padta. AWS piche sab kuch khud sambhalta hai.
* **High Availability & Durability:** Aap ka data automatic multiple data centers mein copy hota hai taake kabhi loss na ho.
* **Unlimited Scaling:** Aap is par 1 item store karein ya **arabon (billions)** items, 1 request per second bhejein ya **hazaron requests per second**, DynamoDB bina slow hue khud hi scale ho jata hai.

#### AWS Ke Doosre NoSQL Options:

* **Keyspaces:** Managed Apache Cassandra.
* **Neptune:** Graph databases ke liye.
* **DocumentDB:** MongoDB compatible database.
* **MemoryDB for Redis:** Super-fast in-memory database.

> **Mahaam Note:** Aap purani legacy applications (jaise MySQL par bani web apps) ko direct DynamoDB par nahi chala sakte. Is ke liye aap ko apni application ka code DynamoDB ke mutabiq naye tarike se likhna padta hai.

---

### DynamoDB Ke Real-World Use Cases (Kahan Istemal Hota Hai?)

1. **Massive & Spiky Workloads (Bohot Zyada Aur Achanak Aane Wala Traffic):**
* Jab aap aisi app bana rahe hoon jahan achanak millions of users aa jayein.
* *Writer ki real-world example:* Web applications par hone wale client-side errors ko real-time mein track karne ke liye unhone DynamoDB istemal kiya.


2. **Choti Applications Ya Simple Data Structures:**
* Jab aap Pay-Per-Request (On-Demand) pricing model chahte hain—jitna use karo bas utna bill do.
* *Writer ki real-world example:* Background mein chalne waale Batch Jobs ki progress tracking ke liye DynamoDB ka istemal kiya gaya.



---

### Hands-On Project: `nodetodo` Application

Is chapter mein hum ek simple Command-Line Tool banayege jiska naam **`nodetodo`** hai. Yeh Database duniya ka **"Hello World"** example hai.

#### Figure 12.2 Detailed Breakdown (nodetodo CLI App in Action)

Aap diye gaye **Figure 12.2** ki terminal screenshot ko dekhein. Is mein step-by-step commands chalai gayi hain:

<div align="center">
  <img src="./images/02.png" width="600"/>
</div>

```bash
# Step 1: Naya User add karna
mwittig:chapter13 michael$ node index.js user-add michael michael@widdix.de +4971537507824
user added with uid michael

```

* **Explanation:** Command line se `user-add` run karke user `michael`, uska email `michael@widdix.de`, aur phone number add kiya gaya hai. System ne isay unique ID (`uid michael`) assign kar di.

```bash
# Step 2: Pehla Task (To-Do) add karna
mwittig:chapter13 michael$ node index.js task-add michael "book flight to AWS re:Invent"
task added with tid 1526650262330

```

* **Explanation:** `task-add` command chalakar `michael` user ke liye pehla kaam ("book flight to AWS re:Invent") add kiya gaya. System ne task ki ID (`tid: 1526650262330`) create ki.

```bash
# Step 3: Doosra Task add karna
mwittig:chapter13 michael$ node index.js task-add michael "revise chapter 10"
task added with tid 1526650265877

```

* **Explanation:** Ek aur task ("revise chapter 10") add kiya gaya. Iski alag Task ID (`tid: 1526650265877`) generate hui.

```bash
# Step 4: User ke sare Tasks ki list dekhna
mwittig:chapter13 michael$ node index.js task-ls michael
tasks [ { tid: '1526650262330',
    description: 'book flight to AWS re:Invent',
    created: '20180518',
    due: null,
    category: null,
    completed: null },
  { tid: '1526650265877',
    description: 'revise chapter 10',
    created: '20180518',
    due: null,
    category: null,
    completed: null } ]

```

* **Explanation:** `task-ls michael` chalane par DynamoDB se sara fetch kiya gaya data terminal par JSON array ki shakhal mein list ho jata hai.

```bash
# Step 5: Task ko Completed mark karna
mwittig:chapter13 michael$ node index.js task-done michael 1526650262330
task completed with tid 1526650262330

```

* **Explanation:** Specific Task ID (`1526650262330`) ko specify karke us task ka status complete mark kar diya gaya.

---

> ### Examples are 100% covered by the Free Tier
> 
> 
> Is chapter mein di gayi tamam practical exercise AWS ke **Free Tier** mein 100% free hain. Bas is baat ka dhyan rakhein ke aap ke AWS account mein koi aur heavy resources na chal rahe hoon. Chapter ke aakhir mein hum saare resources delete/clean up bhi karenge taake koi bill na aaye.

---

## Programming a to-do application

Aaiye ab dekhte hain ke DynamoDB ke sath kaam karne ke liye ek to-do application kaise program ki jati hai.

DynamoDB ek **Key-Value Store** hai jo aap ke data ko **Tables** (dabon ya registers) mein organize karta hai.

* **Example:** Aap ke paas ek table apne **Users** ka data store karne ke liye ho sakti hai aur doosri table un ke **Tasks** (kaam) ko store karne ke liye.
* **Item (Record):** Table ke andar mojood har record ko hum **Item** kehte hain. Isay aap aam relational database (jaise MySQL) ki ek **Row** (line) ki tarah samajh sakte hain. Har Item ki ek unique key hoti hai jisse uski pehchan hoti hai.

Kisi programming language ki ziyaada mushkilat se bachne ke liye, hum **Node.js/JavaScript** ka istemal karke ek choti si terminal app banayenge jiska naam **`nodetodo`** hai. Yeh app aap ke local machine ke terminal par chalti hai aur piche DynamoDB ko database ke tor par istemal karti hai.

Is `nodetodo` application ke andar yeh tamam features hain:

* Naye users banana aur purane users delete karna.
* Naye tasks banana aur unhein delete karna.
* Tasks ko "Done" (kam mukammal) mark karna.
* Alag alag filters ke sath sabhi tasks ki list nikalna.

Is app ka Command-Line Interface (CLI) bohot aasan aur samajhne mein easy banane ke liye **docopt** namak tool/language ka istemal kiya gaya hai. Below di gayi listing mein `nodetodo` ke supported commands aur unke parameters bataye gaye hain:

---

### Listing 12.1 CLI description language docopt: Using nodetodo (cli.txt)

```text
nodetodo

Usage:
  nodetodo user-add <uid> <email> <phone>
  nodetodo user-rm <uid>
  nodetodo user-ls [--limit=<limit>] [--next=<id>]
  nodetodo user <uid>
  nodetodo task-add <uid> <description> [<category>] [--dueat=<yyyymmdd>]
  nodetodo task-rm <uid> <tid>
  nodetodo task-ls <uid> [<category>] [--overdue|--due|--withoutdue|--futuredue]
  nodetodo task-la <category> [--overdue|--due|--withoutdue|--futuredue]
  nodetodo task-done <uid> <tid>
  nodetodo -h | --help
  nodetodo --version

Options:
  -h --help        Show this screen.
  --version        Show version.

```

#### Code aur Commands Ka Breakdown:

* **`nodetodo user-add <uid> <email> <phone>`:** Is command se naya user add hota hai. Is mein User ID (`uid`), Email, aur Phone number teeno dena compulsory hai.
* **`nodetodo user-rm <uid>`:** Kisi user ki `uid` de kar usay system se remove (delete) kiya jata hai.
* **`nodetodo user-ls [--limit=<limit>] [--next=<id>]`:** Sabhi users ki list dikhata hai. Square brackets `[...]` ka matlab hai ke `--limit` (kitne users dikhane hain) aur `--next` (aglay page par jane ke liye ID) optional parameters hain.
* **`nodetodo user <uid>`:** Specific user ki mukammal details dikhata hai.
* **`nodetodo task-add <uid> <description> [<category>] [--dueat=<yyyymmdd>]`:** User ke liye naya task add karta hai. User ID aur Task Description zaroori hain, jabke Category aur Due Date (`dueat`) optional hain.
* **`nodetodo task-rm <uid> <tid>`:** Specific User ID (`uid`) aur Task ID (`tid`) ke zariye task delete karta hai.
* **`nodetodo task-ls <uid> [<category>] [--overdue|--due|--withoutdue|--futuredue]`:** User ke tasks list karta hai. Pipe `|` ka matlab "Ya yeh, Ya woh" (either/or) hota hai, yani aap aik waqt par ek hi condition/filter laga sakte hain (jaise overdue tasks ya future due tasks).
* **`nodetodo task-la <category> [--overdue|--due|--withoutdue|--futuredue]`:** Kisi khaas category ke tamam tasks ko filters ke sath list karta hai.
* **`nodetodo task-done <uid> <tid>`:** Task ko complete mark kar deta hai.
* **`nodetodo -h | --help` aur `--version`:** Help menu aur app ka version dikhane ke liye options hain.

> **Mahaam Fark (DynamoDB vs SQL):** DynamoDB kisi traditional SQL database jaisa nahi hai jismein aap SQL queries (`SELECT * FROM...`) likhte hain. DynamoDB ke sath baat karne ke liye hum **AWS SDK** istemal karte hain jo AWS ke REST API par requests bhejta hai. Aap purani SQL app ko direct DynamoDB par nahi chala sakte; aap ko hamesha code likhna padta hai!

---

## Creating tables

DynamoDB mein har table ka ek unique naam hota hai aur us mein **Items** ka collection hota hai.

* **Attribute:** Item ke andar mojood alag alag data fields ko hum **Attribute** kehte hain. Yeh hamesha **Name-Value Pair** ki shakal mein hote hain (e.g., `Name: Emma`).
* **Attribute Values Ki Types:**
1. **Scalar (Single value):** Number, String, Binary, Boolean.
2. **Multivalued (Ek se zyada values):** Number Set, String Set, Binary Set.
3. **JSON Document:** Complex objects ya arrays.



Table ke alag alag items ke paas bilkul alag attributes ho sakte hain, kyun ke DynamoDB mein koi sakht (enforced) schema nahi hota.

---

### Figure 12.3 Detailed Breakdown

Figure 12.3 mein DynamoDB ke basic components ko visualize kiya gaya hai:

<div align="center">
  <img src="./images/03.png" width="600"/>
</div>

* **Table Box:** Poori Table ko represent karta hai.
* **Horizontal Rows (Items):** Table mein mojood har horizontal line ek **Item** hai.
* **Columns (Primary Key & Attributes):**
* Sab se pehle column ko **Primary Key** mark kiya gaya hai, jo har item mein lazmi mojood hota hai.
* Baqi boxes **Attributes** ko zahir karte hain.


* **Items can have different attributes (Sab se zaroori point):** Figure mein wazeh dikhaya gaya hai ke pehle Item ke paas 3 extra attributes hain, doosre ke paas sirf 2 attributes hain, aur teesre ke paas alag attributes hain. Is ka matlab yeh hai ke DynamoDB mein fixed schema nahi hota—har item apne alag attributes rakh sakta hai.

> Bhalay DynamoDB mein fixed schema nahi hota, lekin table banate waqt aap ko yeh batana laazmi hota hai ke kaun sa attribute **Primary Key** ke taur par kaam karega.

---

## Users are identified by a partition key

User ka data save karne ke liye hum ne yeh simple JSON data structure choose kiya hai:

```json
{
  "uid": "emma",        // Unique user ID
  "email": "emma@widdix.de", // User ka email address
  "phone": "0123456789"    // User ka phone number
}

```

Is information ki bunyaad par DynamoDB table kaise banayein?

1. **Table Ka Naam:** Hamesha apni application ka naam prefix ke taur par lagana chahiye. Hum is table ka naam **`todo-user`** rakhte hain taake future mein kisi aur app ke table se name collision (naamon ka takraao) na ho.
2. **Primary Key Choosna:** Hum `uid` ko primary key chunte hain kyun ke har user ki `uid` unique hai.
3. **Partition Key Kya Hai?** Jab hum sirf ek hi attribute ko primary key ke taur par istemal karte hain, toh DynamoDB usay **Partition Key** kehta hai.

AWS CLI command `aws dynamodb create-table` chalate waqt **4 zaroori options** hote hain:

* **`table-name`:** Table ka naam (jo baad mein badla nahi ja sakta).
* **`attribute-definitions`:** Primary key mein istemal hone wale attributes ke naam aur unki types. Types yeh ho sakti hain: `S` (String), `N` (Number), ya `B` (Binary).
* **`key-schema`:** Primary key ke structure ki wazahat. `HASH` Partition Key ke liye hota hai aur `RANGE` Sort Key ke liye.
* **`provisioned-throughput`:** Table ki speed settings (`ReadCapacityUnits=5,WriteCapacityUnits=5`).

Execute karein yeh CLI command `todo-user` table banane ke liye:

```bash
aws dynamodb create-table --table-name todo-user \
  --attribute-definitions AttributeName=uid,AttributeType=S \
  --key-schema AttributeName=uid,KeyType=HASH \
  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5

```

Table banne mein kuch seconds ka time lagta hai. Jab tak status **ACTIVE** na ho jaye, hum table par kaam nahi kar sakte. Status check karne ki command neechay di gayi hai:

---

### Listing 12.2 Checking the status of the DynamoDB table

```bash
$ aws dynamodb describe-table --table-name todo-user

```

**Output JSON Breakdown:**

```json
{
  "Table": {
    "AttributeDefinitions": [
      {
        "AttributeName": "uid",
        "AttributeType": "S"
      }
    ],
    "TableName": "todo-user",
    "KeySchema": [
      {
        "AttributeName": "uid",
        "KeyType": "HASH"
      }
    ],
    "TableStatus": "ACTIVE",
    "CreationDateTime": "2022-01-24T16:00:29.105000+01:00",
    "ProvisionedThroughput": {
      "NumberOfDecreasesToday": 0,
      "ReadCapacityUnits": 5,
      "WriteCapacityUnits": 5
    },
    "TableSizeBytes": 0,
    "ItemCount": 0,
    "TableArn": "arn:aws:dynamodb:us-east-1:111111111111:table/todo-user",
    "TableId": "0697ea25-5901-421c-af29-8288a024392a"
  }
}

```

* **`AttributeDefinitions`:** Yeh batata hai ke table ki key ke taur par `uid` ek String (`S`) set hai.
* **`TableName`:** Table ka naam `todo-user` hai.
* **`KeySchema`:** Key type `HASH` hai, matlab `uid` humari Partition Key hai.
* **`TableStatus`:** Status `"ACTIVE"` dikha raha hai, matlab table ab istemal ke liye 100% tayar hai.
* **`CreationDateTime`:** Table kis date aur time par bani thi.
* **`ProvisionedThroughput`:** Read aur Write capacity units dono 5, 5 set hain.
* **`TableSizeBytes` & `ItemCount`:** Abhi table khali hai is liye bytes aur items ki tadaad 0 hai.
* **`TableArn` & `TableId`:** AWS ka Unique Resource Name aur Internal Identifier ID.

---

## Tasks are identified by a partition key and sort key

Users ki table banane ke baad, ab humein **Tasks** ko store karne ke liye ek table chahiye. Aise Task ka data structure yeh hai:

```json
{
  "uid": "emma",               // Kis user ka task hai
  "tid": 1645609847712,        // Task ID (Time in milliseconds)
  "description": "prepare lunch" // Task kya hai
}

```

### Partition Key + Sort Key (Composite Primary Key) Kyun Chuni?

Agar hum sirf `tid` (Task ID) ko primary key banate toh masla yeh hota ke hum `task-ls` (user ke saare tasks list karne waali command) tezi se nahi chala sakte the.

Is liye hum ne **`uid` aur `tid` ke combination** ko primary key banaya:

* **Partition Key (`HASH`):** `uid` (User ID).
* **Sort Key (`RANGE`):** `tid` (Task ID).

> **Niyam (Rule):** Partition key akele unique hona zaroori nahi, aur Sort key bhi akele unique hona zaroori nahi. Lekin **Partition Key + Sort Key ka jod (combination) 100% unique hona chahiye!**

> **Ahem Limitation (Pabandi):** Is design ki waja se ek user ek hi millisecond ke andar 2 tasks create nahi kar sakta, kyun ke `uid` + `tid` duplicate ho jayenge. Lekin chunke time milliseconds mein hai, toh aam use mein aisa masla nahi aata.

### Unordered Hash Index Aur Sorted Index Kaise Kaam Karte Hain?

DynamoDB internals ko bilkul aasan misal se samjhein:

1. **Partition Key (`uid`)** ka koi khas order nahi hota (Unordered Hash Index). Data alag alag partitions par bikhra hota hai.
2. **Sort Key (`tid`)** har Partition Key ke andar tartiib se (Sorted Order mein) arranged hoti hai.

Is ko is data set example se samjhein:

```text
["john", 1] => { "uid": "john", "tid": 1, "description": "prepare customer presentation" }
["john", 2] => { "uid": "john", "tid": 2, "description": "plan holidays" }
["emma", 1] => { "uid": "emma", "tid": 1, "description": "prepare lunch" }
["emma", 2] => { "uid": "emma", "tid": 2, "description": "buy nice flowers for mum" }
["emma", 3] => { "uid": "emma", "tid": 3, "description": "prepare talk for conference" }

```

* `"john"` ke bucket/partition mein Task 1 aur 2 ek line se sorted hain.
* `"emma"` ke bucket/partition mein Task 1, 2, aur 3 sorted hain.
* `"john"` aur `"emma"` ke partitions ka aapas mein koi tartiib (order) nahi hai.

### Table Create Karne Ki CLI Command:

Execute karein yeh command `todo-task` table banane ke liye:

```bash
aws dynamodb create-table --table-name todo-task \
  --attribute-definitions AttributeName=uid,AttributeType=S AttributeName=tid,AttributeType=N \
  --key-schema AttributeName=uid,KeyType=HASH AttributeName=tid,KeyType=RANGE \
  --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5

```

#### Command Ka Breakdown:

* **`--table-name todo-task`:** Table ka naam `todo-task` set kiya.
* **`--attribute-definitions`:** Do attributes define kiye: `uid` (Type `S` = String) aur `tid` (Type `N` = Number).
* **`--key-schema`:** `uid` ko `HASH` (Partition Key) banaya aur `tid` ko `RANGE` (Sort Key) banaya.
* **`--provisioned-throughput`:** Performance limits 5 Read / 5 Write capacity units set ki.

Aap `aws dynamodb describe-table --table-name todo-task` chala kar tab tak wait karein jab tak is ka status **ACTIVE** na ho jaye. Ab humari dono tables (`todo-user` aur `todo-task`) tayar hain!

---