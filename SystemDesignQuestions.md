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
 ![](https://github.com/roymanish/InterviewQuestionPatterns/blob/main/images/url-shortener.jpg)
