# System Design Questions #
- [URL Shortener](#url-shortener)
- [Distributed Cache](#distributed-cache)
- [Web Crawler](#web-crawler)

<a id="url-shortener"></a>
## URL Shortener ##

### Requirements ###
````
  - Given a URL it should be able to generate and return a shortened URL
  - Given a shortened URL it should be able to redirect user to mapped URL
````
### Non functional Requirements ###
````
  - Highly Performant
  - Highly Available
  - Scalable
````
### Estimations ###
````
  DAU -> 100M
  Daily URL creations(Writes) - > 10M
  Write RPS -> 120/s
  Daily Reads -> 90M
  Read RPS -> 1000/s
  Memory Required per day -> 40M * 1KB -> 40GB
  URL retention 5 YR -> 40GB * 365 * 5 ->70000GB -> 70TB
````
### Diagram ###
 ![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/url-shortener.png)

### Short URL Generation ###
````
To generate alpha numeric short URL from a auto-incremented ID provided by the database we need to convert the Base10 id to Base62 string.
Here it is Base62 because we have 62 characters in shortURL ([0-9][a-z][A-Z]).
To retrieve the record based on shortURL we can convert the shortURL from Base62 to Base10 which will tell us the exact record in the DB.

Steps : 
  - Insert
      - Hash an input long url into a single integer;
      - Locate a server on the ring and store the key--longUrl on the server;
      - Compute the shorten url using base conversion (from 10-base to 62-base) and return it to the user.
  - Retrieve
      - Convert the shorten url back to the key using base conversion (from 62-base to 10-base);
      - Locate the server containing that key and return the longUrl.
````
<a id="distributed-cache"></a>
## Distributed Cache ##
![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/distributed-cache-lesson.png)

### Diagram ###
![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/distributed-cache.png)

### Cache Writing Policies ###
````
 - Write-through cache: The write-through mechanism writes on the cache as well as on the database.
Writing on both storages can happen concurrently or one after the other.
This increases the write latency but ensures strong consistency between the database and the cache.
 - Write-back cache: In the write-back cache mechanism, the data is first written to the cache and
asynchronously written to the database. Although the cache has updated data, inconsistency is inevitable
in scenarios where a client reads stale data from the database. However, systems using this strategy will have small writing latency.
 - Write-around cache: This strategy involves writing data to the database only. Later, when a read is triggered for the data,
it’s written to cache after a cache miss. The database will have updated data,
but such a strategy isn’t favorable for reading recently updated data.
````
### Eviction Policies ###
````
 - Least recently used (LRU)
 - Most recently used (MRU)
 - Least frequently used (LFU)
 - Most frequently used (MFU)
````
<a id="web-crawler"></a>
## Web Crawler ##
### Seed URLs ###
````
Seed URLs(Starting URLs) can be selected based on following criterias
- Location based : Location where crawler is running
- Category Based : Based on the content we want to scan
- Popularity Based : Based on popularity of URLs

````
### Storing ###
````
System should be able to scan and store the extracted content into a blob store so that it can be later processed for Indexing and Ranking by search engines.
````
### Scheduling ###
````
Crawler should repeat in a scheduled manner.
````
### Components ###
- SeedURL Queue
- URL Selector based on priority and recrawl frequency
- HTML Downloader
- DNS to fetch IPs for domains to be crawled
- HTML content parser
- Deduplication Service
- URL validation and filtering to exclude URLs not eligible for crawling
- Blob store to save parsed html content(key words for search ranking/indexes)

