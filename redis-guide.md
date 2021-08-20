# Redis Guide

[toc]

**Reference:** [Link](https://medium.com/javascript-in-plain-english/getting-started-with-redis-and-how-to-use-it-in-node-js-8d4f1a3f7369)

Redis is an in-memory database, that works with key values. So for each value, there is one key. Because of the speed of reading and writing data with Redis, and the simple key-value system, Redis can be used wonderfully for e.g. caches. Permanent data such as user information or images, however, should not be stored with Redis because of its volatility.



### Start Redis server

```bash
systemctl start redis
```



### CLI Redis commands

````bash
# set key value
SET name Paras

# get value
GET name

# delete key
DEL name

# append value to key
APPEND name " arora"

# set expire time while setting
SET name Paras EX 60  # 60s

# set expire after setting key value
EXPIRE name 60

# get time remaining in expire for a key
TTL name

# check if key exists
EXISTS name

````



### Usage in Node 

```bash
npm install redis
```



```javascript
const redis = require(‘redis’)
const redisPort = 'redis://localhost:6379'; // 6379 (default port)
const redisClient = redis.createClient();

// set
redisClient.set("name", "paras")

// get
redisClient.get("name", (err, value) => {
   console.log(value);
})

// delete
redisClient.del("name");

// append
redisClient.append("name", " arora");

// set expire time
redisClient.set("name", "paras", "EX", 60); // in sec

// set expire after set
redisClient.expire("name", 60);

// remaining time to expiry
redisClient.ttl("name", (err, value) => {
   console.log(value);
});

// check if key exists
redisClient.exists("name", (err, value) => {
   if(value) console.log("key does exist");
   else console.log("key doesn't exist");
});

// working with json and objects
// set 
client.set('name', 
  JSON.stringify({ firstname: 'paras', lastname: 'arora'})
);

// get
client.get('name', (err, val) => {
  console.log(JSON.parse(val))
});

```



