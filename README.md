# high-level-system-design-exercise-notes-2

## What is Twilio?
- A cloud communications platform that offers services in the form of APIs that developers can use to integrate voice, messaging, and video communication capabilities into web and mobile applications.
- Twilio focusses more on SMS and voice than email

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

## Rate Limiter

2. Problem 2: REST API Rate Limiter

Question: Design a function to simulate a rate limiter that restricts incoming API requests to a defined threshold per user.
Approach: I explained the use of data structures like hash maps to track user requests and algorithms such as sliding windows or leaky buckets to handle the logic efficiently.


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

Create a metrics aggregation platform Hiring Manager round


## General Questions
Situation when you failed on a project:

I introduced an issue in prod by introducing a new WAF that the security team had me add. I tested it thoroughly, but didn't consider that we also needed to test with clients outside of the Regions internal whitelisted IPs. Just shows you need to dedicate proper time to testing and run it by other people before something goes to production. 




## OK TIME TO ACTUALLY DO SOME HANDS ON WITH TWILIO API 

