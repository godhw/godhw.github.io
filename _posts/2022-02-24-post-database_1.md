---
title: "[DataBase Systems] Disk Space Management"
categories:
  - database
tags:
  - database
  - lacture
  - computer science
last_modified_at: 2022-03-13T22:00:00 +09:00
---
Let`s talk about disk space manager.
{: .notice--accent}

{% include toc %}

Disk
=====
- A lot of databases still use magnetic disks
    * SSD is really expensive!
    * 4TB SSD : 400$, 4TB HDD : 110$
    * The larger the capacity, the wider the price difference
    
Simmilarly, **DRAM** is not used because of its price and volatility, but volatility is addressed when **NVRAM** is developed.<br>
Of course, In-memory Database is actually used a lot. In particular, used for part of servers that utilize temporary data.
{: .notice--warning}

- Magnetic disk is stable than SSD
    * HDD is so weak in external crash
    * but this is in static status(ex. data center)
- Magnetic disk is very slow
    * **HDD with 7200rpm** has an average speed of 120MB/s for Read and Write
    * On the other hand, **SATA3 SSD** is 500MB/s, **NVMe SSD** is 3000-5000MB/s
    * In particular, the random read performance of the SSD is overwhelmingly good.
    * Key to lower I/O cost : reduce seek<span class="comment"> (moving arms) </span>and rotational<span class="comment"> (waiting for block to rotate under head) </span> delays
    
![center-aligned-image](/images/database/2022-02-24-disk_image.png){: .align-center}

Looking at it this way, SSD seems to be superior, but at coarse-grain (1-2 MB) random writes, the speed is significantly slower at 120 MB/s. Magnetic disks are still advantageous in that SSDs have to bear at least four times the cost.
{: .notice--warning}

**Trade-off** : When design DB or OS etc., there is no perfect system. There are always *pros and cons*, sacrifice some property for others. Developers have to **make a rational choice** based on their thoughts or after discussion with colleagues.
{: .notice--accent}

Disk Space Management
====

- DBMS interfaces to storage at **Block Level**<span class="comment"> (generally 64KB - 128KB) </span>
- Read and Write large chunks of sequential bytes
- This is the lowest component of DBMS
- Higher levels call upon this layer to
    * read/write a page
    * allocate/de-allocate logical pages

### Block and Page
They are different concept but fairly standard to treat them as synonyms
- Block : Unit of transfer for disk read/write => **physical data**
- Page : Fixed size contiguous chunk of memory => **logical data**
    * assume same size as block
    * Refer to corresponding blocks on disk

### Implementation Strategy
- Talk to the storage device directly
    * very fast but very complex
    * must deal all situation and devices
- Run over filesystem
    * simple but slower than the above strategy
    * use DB file
    * I assume that use this strategy

It is necessary to divide one file into multiple pages. This process is called **pagination**, and each page has a unique `page_id` that used by upper layer(logical).
- Disk Space Manager can only translate `page_id` to real location and manage these pages.
- This is processing requesting(read) or returning(write) a page by the upper layer.
- In addition, it plays a role in managing empty pages(`free_page`). That is, the capacity of the db file is also managed by this.
- To manage `free_page`, `header_page` that stores metadata is used, and generally, the first page (at the beginning of the file) is designated as this.

![center-aligned-image](/images/database/2022-02-24-overview_of_DSM.png){: .align-center}



    
## Disk API
- READ: from disk to ram
- WRITE: from ram to disk
- ALLOC: used when a new `free_page` is needed to insert a value in the upper layer(allocate)
- FREE: used when return a page that will not be used in the future from the upper layer(de-allocate)

**Read and Write calls are really slow!!**  
other APIs are logically used, but they are **also slow** because they require modification of `header_page`. 

Disk Manager API
```c
void file_read_page(table_id, page_id, *dest);
void file_write_page(table_id, page_id, *src);
void file_alloc_page();
void file_free_page(table_id, page_id);
```

In order to effectively use the OS's file system, **file descriptor** is used.<br>
Also, need to utilize the system call.<br>
If you google it, you can immediately find out what header files and functions you need.
{: .notice--warning}

Commonly used functions are `open()`, `close()`, etc.. <span class="comment">not `fopen()`, `fclose()`!!!</span>
Also, because of the characteristics of db, `write()` function must be called and `fsync()` function must also be called. The `fsync()` function causes the OS to flush the data left in the kernel buffer to disk.

# Relative Post
1. [[DataBase Systems] introduction]({% post_url 2022-01-25-post-database_0 %})
2. This post!
3. Add more...
