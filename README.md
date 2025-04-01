# high-level-system-design-exercise-notes-2

## What is Twilio?
- A cloud communications platform that offers services in the form of APIs that developers can use to integrate voice, messaging, and video communication capabilities into web and mobile applications.
- Twilio helps businesses communicate with their customers
- Twilio focusses more on SMS and voice than email

## React/NodeJS

https://github.com/mfkimbell/react-typescript-notes

## Conflict Interview Story

#### SITUATION AND TASK

I have a consulting group with my friends Jayden and Michael, they're developers at Motion and Apple.

We had a client ask us to build an embeddable iframe that helps marketing people learn to use ai to help apply business decomposition documents

They said it would take around 4 hours and they would pay $600. I guessed it would take 3 times longer, which was fine, and I had the other two developers from my consulting gorup come help.

Now we were making good progress, but jayden wanted to do a full CICD pipeline. By the time me and micheal had finished the project (around 6-8 hours in) jayden was still working on his CICD pipeline. He knew it was going to take longer and he wanted to ask for more money.

#### ACTION

I wanted to maintain professionalism and a good reputation with the client and NOT ask for more money, but i also felt for jayden and didn't want him to feel like we were taking advantage of him. My solution was to our relationship with the client, so i talked to our other developer micheal and asked if he would be ok splitting the payout by hours worked. He agreed, and the problem was solved.

#### RESULT AND WHAT I LEARNED

I took a pay cut, but the client ended up being very happy with the work, and said he would recommend us and reach out in the future.


1. we need a full cicd template for fast projects, which was have since made for AWS and Google Cloud.
2. we needed way better requirement documents, and I needed to work closer with the client to make that happen
3. Its always better to prioritize relationships with people over money
4. I realized it's important to consider how timelines and budgets can affect projects and strain relationships, because money is very important to both clients and engineers. We had to navigate product quality versus funding and time. My team overperformed because we didn't want to produce low quality products, even for a demo.

## What is an API?
- an api is a system of rules that allows different software services to interact

## HTTP Methods: 
GET, POST, DELETE, PUT(creates resource if it doesn't exist)/PATCH(doesn't create if it doesn't exist)

## Monolithic vs Decoupled architechture

Monolithic = keeps things simple and designed for the specific task

Decoupled = scalable, modular, better for system reuse, more fault tolerant

## Javascript review
refer to "react-typescript-notes"

## SQL basics refresher

Remember to mention I use ORMs a lot, and I use some AI tools to help craft raw SQL queries when i have to, but we don't normally write RAW SQL since we use entity framwork core and I also like using SQLalchemy. However, I am pretty comfortable with PostgreSQL/AWS RDS and MongoDB/DynamoDB and recently used CockroachDB which is a cloud based Postgres database. 

ORDER BY orders things by a specific column

```sql
SELECT
    customer_id,
    SUM(amount) AS total_spent
FROM
    transactions
WHERE
    transaction_date >= CURRENT_DATE - INTERVAL '1 year'
GROUP BY
    customer_id
ORDER BY
    total_spent DESC
LIMIT 10;
```

Inner join

<img width="464" alt="Screenshot 2025-03-28 at 11 15 52 AM" src="https://github.com/user-attachments/assets/521e3219-0b3d-4bcd-87de-bd113590d310" />

We can use `GROUP BY` to perform our operations uniquely based on a column such as a player ID

<img width="365" alt="Screenshot 2025-03-28 at 11 17 53 AM" src="https://github.com/user-attachments/assets/261af93c-dc35-41ad-b750-ef3e10ec1a56" />

<img width="226" alt="Screenshot 2025-03-28 at 11 18 52 AM" src="https://github.com/user-attachments/assets/71fccdeb-8ee7-4a86-b212-b4a038b49fd0" />

## SMS example

#### How do you prepare for system failure?
Design a notification system based on some criteria

In this interview on a Hackerrank link that provides a white a board, you have to draw how you would design an application that uploads file media and allows to search the files. You have to start from selecting the front end, back end, database, and other required components, and it will be asked to provide more details on specific parts of the diagram. 

Design a system that sends/receives messages

## LRU Cache example
How would you design a LRU cache? 
Design a system with cache

Four Kinds of Cache:
* browser cache
* CDN cache
* **Server cache** (redis, memcached)
* Database cache

### Cache Invalidation

We use **TTL** or Time to Live to tell the system when to remove that cache entry from redis.

### Cache Strategies

#### Write-Through / Write-Behind:

Write-through: Data is written to the cache and the underlying data store at the same time. Simplifies consistency but can add latency to writes.

Write-behind (or “write-back”): Updates are written to the cache first and then asynchronously to the underlying store. Improves write performance but increases risk of data loss or lag if the cache fails before flush.

#### Lazy Loading

 The application checks the cache first, if there’s a miss, the app fetches from the database and populates the cache. Common approach to reduce overhead when data isn’t always requested.

 ### LRU and LFU

 LRU = Least Recently Used
 LFU = Least Frequently Used (really really popular old youtube videos would never get removed)

 ### Refresh Ahead

The cache preemptively refreshes items that are about to expire. This can reduce latency spikes if your system has predictable access patterns.

## Designing the LRU redis cache for database SQL resource calls

1. make a redis table, set LRU as strategy

You set LRU or LFU in Redis’s configuration, either in the `redis.conf` file or dynamically at runtime with `CONFIG SET`

 Redis doesn’t actually store an explicit “LRU” column in a user-readable table, but under the hood it maintains **metadata** (like last-access timestamps or usage counters) to handle eviction.

| Key                                  | Value (serialized)                | TTL (sec) | **LRU / Usage Info** (conceptual)          |
|--------------------------------------|-----------------------------------|-----------|--------------------------------------------|
| `db_query:md5("SELECT ...")`        | JSON blob of the query result     | 3600      | Last accessed: 2025-03-30 10:52:36         |
| `db_query:md5("SELECT ... 2")`      | JSON blob of another query result | 3600      | Last accessed: 2025-03-30 10:59:02         |


- **Key**: The hashed SQL query (e.g., `db_query:a197022c8ea3032d0797...`).  
- **Value**: The serialized query result (often JSON, MsgPack, etc.).  
- **TTL**: How long Redis will keep the key before it expires automatically (e.g., 3600 seconds).  
- **LRU / Usage Info**: A conceptual column showing the key’s last access time (or usage counter). Redis uses this info *behind the scenes* to decide which key to evict first once memory constraints are reached.


2. make our backend route attempt to use the cache first

hashes query -> attempts redis data grab -> if hit, update -> if miss, queries real database -> updates hash

```python
def get_data(query: str, redis_client) -> Any:
    """
    Attempts to get data from Redis first. On a miss, queries the DB,
    stores the result in Redis, and returns it.
    """
    import hashlib
    
    # 1) Hash the query to build a unique key
    query_hash = hashlib.md5(query.encode('utf-8')).hexdigest()
    redis_key = f"db_query:{query_hash}"

    # 2) Check Redis
    cached_data = redis_client.get(redis_key)
    if cached_data is not None:
        # Cache HIT
        return deserialize(cached_data)

    # 3) Cache MISS => fetch from DB
    db_result = get_data_from_db(query)
    if db_result is not None:
        # 4) Serialize and store in Redis with a TTL (e.g., 3600 seconds)
        redis_client.setex(redis_key, 3600, serialize(db_result))

    # 5) Return the fresh data
    return db_result
```



## Rate Limiter

see
https://github.com/mfkimbell/twilio-message-system-notes/blob/main/README.md

## Leetcode

An DFS question and a binary tree find the common nodes.

Meeting room 1 and 2

Problem 1: Interval Merging
Question: Given a list of intervals, merge overlapping ones and return the non-overlapping intervals.
Approach: I shared my thought process of sorting intervals by their start times, iterating through them, and merging overlaps by comparing the end points of adjacent intervals.

DSA Question 1: Graph Cycle Detection
Question: Detect if a cycle exists in a directed graph.


**Unusre if this is a javascript leetcodee question??**
1. Determine if available phone numbers exist for certain desired vanity codes (words in phone numbers). 2. Split a string message into SMS standard length and add a fractional tag at the end with the current segment over total segments ( e.g. (1/5) )



Given two arrays of strings, one containing the prefixes (area code) and one with a long string of numbers (phone numbers), return the longest prefix corresponding to all phone numbers. Input is a string of characters that represents a text message. You need to segment this message into chunks of messages each of length 160 characters and add suffix "(1/5)" (representing pagination) at the end of each segmented message (Length of "(1/5)" is included in 160 length limit).


(implement queue using stack)



## System Design

Design IMDB Database

Design the cloud architecture for Instagram


## General Questions
Situation when you failed on a project:

I introduced an issue in prod by introducing a new WAF that the security team had me add. I tested it thoroughly, but didn't consider that we also needed to test with clients outside of the Regions internal whitelisted IPs. Just shows you need to dedicate proper time to testing and run it by other people before something goes to production. 





