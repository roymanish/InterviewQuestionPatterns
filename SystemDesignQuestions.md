# System Design Questions #
- [URL Shortener](#url-shortener)

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
