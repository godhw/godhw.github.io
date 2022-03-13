---
title: "[DataBase Systems] introduction"
categories:
  - database
tags:
  - database
  - lacture
  - computer science
last_modified_at: 2022-01-31T00:00:00 +09:00
---

**This is the first post, and I wrote this as a review of the lectures I learned last semester.**


Overall, English is not good and there will be many grammatical errors.<br>
English writing practice is also part of writing this blog. So, I hope everyone who reads this article understands.
{: .notice--warning}

{% include toc %}

# Reference

- I took the database system in 2021-fall, Hanyang Univ. **ITE2038** taught by Prof. Hyungsoo Jung. Most of the content will be based on this lecture.  
- Also, some of the materials from <a href= "https://www2.eecs.berkeley.edu/Courses/CS186/" target="_black">CS186, UC Berkeley</a> can be utilized.  
- Textbook is <a href="https://pages.cs.wisc.edu/~dbbook/" target="_blank">Database Management Systems, 3rd Edition, by Ramakrishnan and Gehrke</a>  

# Contents

I will not deal with `sql`, but will develop the contents according to the **layered architecture of DBMS**<span class="comment">How is a SQL query executed</span>.  
But I didn't fully understand the query optimization and relational operations. So I won't cover or mention it last.

- Disk Space Management
    * Translate page requests into physical bytes on one or more device(s).
- Buffer Management
    * providee the illusion of operating in memory
- File and Index Management
    * Organize tables and Records as groups of pages in a logical file
- Concurrency Control<span class="comment">(Transaction)</span>
    * storage and memory management
- Recovery Protocol
    * storage and memory management
- Others...(add later)

I will provide simple codes used in the project of the course I took. Although it's not perfect code, it's good enough for some examples.
{: .notice--accent}



In the next post, I will explain the basics of layered architecture and disk space management.
![center-aligned-image](/images/database/2022-01-31-layered_architecture.png){: .align-center}

# Relative Post
1. This post!
2. [[DataBase Systems] Disk Space Management]({% post_url 2022-02-24-post-database_1 %})
3. Add more...


