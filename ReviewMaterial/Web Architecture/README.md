![alt text](images/image.png)
```
source :https://medium.com/storyblocks-engineering/web-architecture-101-a3224e126947
```

### 1. Domain Name System (DNS)

**DNS** acts as the "address book of the internet." 

* **The Problem:** Computers communicate using numerical IP addresses (e.g., a 32-bit IPv4 address like `192.168.1.1`), which are extremely difficult for humans to memorize. For example, it is much easier for us to remember `zajel.najah.edu` rather than a complex string of numbers.
* **The Solution:** When a client/browser requests a website using its domain name, the request goes directly to the **DNS Server**. The DNS maps that human-readable name into its corresponding IP address and sends it back to the client.

Once the browser receives the IP address, the DNS's job is complete, and the browser can establish a direct connection with the web server.

### 2. Load Balancer

Before diving into the Load Balancer, we need to understand the core problem it solves: **Scaling**.

If an application relies on a single server, an increase in user traffic will eventually overload the server, causing it to go down. To solve this bottleneck, there are two primary approaches:

1. **Vertical Scaling (Scaling Up):** Adding more power (CPUs, RAM, Storage) to the existing server. However, this has a hardware limit and represents a single point of failure.
2. **Horizontal Scaling (Scaling Out):** Adding more server instances (nodes) to distribute the load across multiple machines. This approach ensures **High Availability** and **Fault Tolerance**, as the failure of one node does not crash the entire system.

#### Enter the Load Balancer
When running multiple servers, a critical question arises: *Who will distribute the incoming client requests among them, and how?* This is exactly where the **Load Balancer** comes into play. It acts as a traffic controller positioned in front of the servers, routing requests based on specific routing algorithms:

* **Random:** Forwarding requests to any server randomly.
* **Round Robin:** Distributing requests sequentially across the server pool.
* **Least Connections / Idle Servers:** Directing traffic to the server with the lowest active load or traffic to prevent overloading.

### 3. Web Application Servers

The **Web Application Server** is the core brain of the application, responsible for handling incoming client requests and sending back the appropriate response (such as HTML pages, JSON data, or assets).

* **The Orchestra Leader:** It doesn't work in isolation. Instead, it acts as an orchestra leader that manages and orchestrates various backend infrastructure components underneath it (like Databases, Caching systems, Search Engines, etc.).
* **Smart Decision Making:** The application server contains the **Business Logic**. It decides which component to utilize based on the request requirements:
  * If a request requires retrieving or storing user records, it queries the **Database**.
  * If a request demands high performance and quick data retrieval for frequently accessed data, it hits the **Cache**.
* **Tech Stack:** This is where the backend code lives. It can be built using various programming languages and frameworks, such as **Node.js (Express)**, **PHP (Laravel)**, **Python (Django)**, or **Ruby (Rails)**.


### 4. Database Servers

It needs no introduction. Every modern application needs a place to store data permanently. While a web server can maintain its state temporarily in memory while running, that data is lost the moment the server restarts. To store data forever (persistently), the **Database Server** comes to the rescue.

In today's tech world, *"Data is Power,"* and we rarely delete it. The database safely holds everything from user registration details to e-commerce order histories.

Generally, there are two main types of databases:

1. **Relational (SQL):** (e.g., *MySQL, PostgreSQL, Microsoft SQL Server*)
   * Stores data in structured **tables** consisting of rows and columns.
   * These tables are linked together using logical **relationships** (hence the name).
2. **Non-Relational (NoSQL):** (e.g., *MongoDB, CouchDB*)
   * Stores data in a more flexible format, such as **documents** or JSON-like structures.
   * Ideal for handling unstructured or massive amounts of big data efficiently.

#### The Role of DBMS:
It is worth noting that the Web Application Server does not communicate with the database files directly on the storage disk. Instead, it interacts via a **DBMS (Database Management System)**—the software engine that manages the data, handles optimization, security, and executes queries.


### 5. Caching Service

Let's be honest: making the database repeat the exact same heavy query 10,000 times for 10,000 different users asking for the exact same result makes absolutely **no sense**. It’s just an execution sentence for your database's performance. 

So, what is the secret recipe for ultra-fast websites? **Caching!**

The Caching Service comes to save the day (and the database's life) by storing frequently requested information in a super-fast environment:

* **The RAM Secret:** Unlike databases that look up data on slow hard drives, a Cache stores data directly in the **RAM**. This reduces the look-up time complexity to a blazing-fast **$O(1)$**. 
* **Data Structure:** It keeps things simple by saving data in a **Key-Value** format (just like a quick-access dictionary).
* **When to use it?** If the data doesn't change every single second (like a trending post or an e-commerce product category), the server fetches it from the database *once*, slaps a copy into the Cache, and serves the next 9,999 users from the RAM instantly.

#### Popular Technologies:
When your application screams for performance, technologies like **Redis** or **Memcached** are the ultimate heroes to look for.

### 6. Job Queues & Servers (Workers)

With the increasing number of users, web servers start receiving multiple heavy requests simultaneously. How can we handle all of these without ruining the **User Experience (UX)**? 

If the web server decides to process a heavy request (like processing a 4K video or generating a huge report) from start to finish synchronously, all other users will be stuck waiting for a very long time. This is where the desperate need for **Asynchronous Work** appears.

#### "Hey Web, take the request!"
There are different architectures that apply async concepts, and the most ubiquitous one is the **Job Queue**. 

The idea is simple: The web server accepts the request and politely tells the user: *"Hmm, I got your job! Just wait a minute and it will be done."* The user's connection is freed instantly, ensuring a smooth UX. Meanwhile, the web server drops that heavy job into a **Queue**.



#### Under the Hood: 
If you are familiar with **CPU Scheduling Algorithms** from your Operating Systems class, this will be a piece of cake to understand! The Job Queue can manage tasks using the same logical patterns:
* **FIFO (First-In, First-Out):** Processing jobs sequentially exactly in the order they arrived.
* **Priority Queue:** Crucial for production (e.g., jumping a premium/VIP user's job to the front of the queue, or prioritizing critical system alerts over low-priority marketing emails).

#### Who will do the job?
The **Worker** jumps in! Workers (or Job Servers) are dedicated processes running in the background. Their only mission in life is to constantly pull jobs from the queue and execute them quietly in the back-kitchen, without disturbing the main Web Server.

### 7. Full-Text Search Service

In today's tech world, we are drowning in massive amounts of semi-structured and unstructured data. If a user wants to search for a specific word, and you try to query it using standard relational databases, it’s going to take a painful amount of time. Let’s face it: modern users are deeply impatient, and absolutely no one will wait for your system to crawl through millions of records via a slow *Full Table Scan*.

To prevent your system from freezing, popular search engines look for a smarter solution: **The Inverted Index**.

#### How it works (The Dictionary & Posting List Approach):
Instead of storing data as standard rows, tools like **Elasticsearch** or **OpenSearch** break down texts into a structured dictionary. Each term gets its own **Posting List** (a list of IDs pointing to where this word appears).

Let’s take a quick example:
Instead of scanning every single image description to find the word *"sunset"*, the Full-Text Search Service looks up the term **"sunset"** in its pre-built index dictionary. It instantly finds the posting list pointing directly to `[Image_05, Image_90, Image_500]`. 

When the user types *"sunset"*, the system returns these images in milliseconds, regardless of how massive the dataset is!

#### Popular Technologies:
* **Elasticsearch** (The reigning king of enterprise search)
* **OpenSearch** (The open-source powerhouse)

#### How does the Search Engine stay updated? (Data Syncing)
A critical question in system design is: *How does Elasticsearch know we added a new image to our main database?* According to the architecture, there are two common ways to handle this:
1. **Synchronous Update:** The Web Server writes to the database and directly sends a copy to Elasticsearch at the same time.
2. **Asynchronous Update (The Smart Way):** The Web Server saves the data to the DB, fires a task into the **Job Queue**, and lets a background **Worker** handle the indexing. This keeps the user's request lightning-fast!

### 8. Services (Microservices Architecture)

As the user base grows, a single web server can no longer handle every single request or execute all business logic alone. To optimize performance, we must introduce **work standardization** to the architecture. 

Instead of forcing the main web server to handle all operations directly, we split the application into separate, decoupled **Services**. The web server’s role is optimized to act as a coordinator; it simply requests specific tasks from specialized internal service providers and returns the results to the user. This significantly decreases the computational load on the main server.



#### Key Architectural Benefits:
* **Decoupling:** By isolating components (e.g., Payment, Account, or Document generation), we break down a massive system into manageable, independent applications.
* **Fault Tolerance:** We successfully eliminate a *Single Point of Failure (SPOF)*. If one non-critical service goes down, the rest of the ecosystem continues running smoothly, ensuring a highly resilient system.
* **Independent Scalability:** We can scale resources only for the services experiencing a bottleneck, optimizing infrastructure costs.

### 9. Data Pipeline & Big Data Engineering

In today's tech landscape, **data talks**. Modern companies capture and store every single user action—every click, scroll, navigation path, like, and order. This continuous stream of events generates a massive mountain of semi-structured and unstructured data that standard relational databases simply cannot handle. 

#### Why do companies care so much about this data?
Data reveals everything about user behavior, preferences, and patterns. By capturing this data, companies can:
1. **Predict future user behaviors** and build smart recommendation engines (e.g., *"Since you liked this, you might love that"*).
2. **Perform advanced analytics and Business Intelligence (BI)** to guide executives toward making the right strategic decisions.

#### Enter Big Data Engineering (The 3 Main Phases):
To process and harness value from this massive wave of data, a **Data Pipeline** is deployed, following three core architectural phases:



1. **The Ingestion Phase:** As the website continuously generates millions of interaction events, this phase acts as a "firehose" (using streaming technologies like **Apache Kafka** or **AWS Kinesis**) to rapidly ingest and capture the raw data without crashing the production system.
2. **The Storage & Loading Phase:** The ingested data is loaded into centralized, highly scalable analytical warehouses or modern architectures. While classical systems use **AWS Redshift**, modern architectures heavily rely on platforms like **Snowflake** or **Databricks** (both of which are strategically built on top of cloud providers like AWS to manage massive datasets).
3. **The Transformation Phase:** This is where the real data engineering happens. Raw data is cleaned, structured, aggregated, and transformed (using distributed compute engines like **Apache Spark**). This converts chaotic, raw logs into highly optimized datasets ready to deliver valuable business insights and power Machine Learning models.

### 10. Cloud Storage

Storing heavy assets like files, videos, and images directly inside a standard relational database makes absolutely no architectural sense. Doing so completely degrades database performance, bloats storage costs, and severely hurts query execution times. 

To solve this, we must offload these large file types to external services specifically designed to scale and handle unstructured objects. This is where **Cloud Storage** comes into play.



#### How it Works:
* Instead of saving the actual file inside the database, the application uploads the file (image, video, PDF) directly to a cloud storage provider, with **AWS S3** being the most popular industry standard.
* The cloud storage service stores the file and returns a unique, lightweight **URL (link)**.
* We store only this lightweight URL string in our standard database. When a user requests the asset, the browser fetches it directly from the cloud storage link, keeping our primary database fast and efficient.

### 11. Content Delivery Network (CDN)

When a user requests a website or an asset that is hosted on a server located in a different country or continent, every single request has to travel massive physical distances. As discussed in Cloud Storage, even if our files are securely stored in AWS S3, if that storage bucket is in the US and the user is in the Middle East, fetching those assets will suffer from high **Latency** (delay).

The ultimate solution to this geographical bottleneck is a **Content Delivery Network (CDN)**.



#### How it Works & Cache Validation:
* **Edge Caching:** A CDN is a global network of proxy servers distributed worldwide. When a user requests a static asset (like an image, video, CSS, or JS file) for the very first time, the CDN fetches it from the original server (Origin) and delivers it to the user while simultaneously saving a copy locally on the nearest regional server (*Edge Server*).
* **Instant Delivery:** For all subsequent requests, the nearest CDN server handles the delivery instantly, bypassing the need to travel back to the origin server.
* **Smart Validation:** To prevent serving outdated content, the CDN performs a lightweight check with the origin server to see if the original file has any new updates or modifications. 
  * If **not modified**, it instantly delivers the cached copy from the CDN edge.
  * If **modified**, it fetches the fresh update from the origin, updates the CDN cache, and responds to the user.

Popular industry choices for this include **AWS CloudFront** and **Cloudflare**.