# Fun-With-Artillery
Experimenting with the load / performance testing tool, Artillery.

# Backstory
For a project at work we've come to the point where we want to do load and performance testing on our endpoints to make sure are system can handle a reasonable amount of concurrent users.

In the past I've used Gatling and Locust for this, but my team now very much prefers things that are familiar to the node.js developer (ie. something that doesn't require install java or pip). 

I also just like trying new things, so I Googled for it...

...and I came upon Artillery!


# What Is It?
Basically, it's a command line tool distributed with npm that can run load tests for you.


# Built With Node, Works With Anything
I was originally looking for a "NodeJs load testing tool" because I was assuming there would be at least some code that you need to write in order to describe how the test(s) should run (how many concurrent times it hit the endpoints, over how many seconds they should be sent, etc).

The interesting thing about Artillery is that it's totally agnostic to your server implementation language!

Rather than writing "code" to describe the tests, Artillery just takes one single configuration yaml file.

The fact that Artillery is built with Node isn't all that important honestly. The key is that it's distributed via npm so in order to get it all you need to is install it globally:

```
npm i -g artillery
```

or if you prefer, add it as a dev dependency in your project:
```
npm i -D artillery
```

# A Look At The Config

| Weird Artillery Name      | What It Means   | Examples     |
| :---        |    :----:   |          ---: |
| target      | base url for all requests   | http://myapp.staging.local ws://127.0.0.1   |
| environments   | a list of environments      |  ci, dev, prod...   |
| phases   | allows you to break the load test down multiple sections with their own specified duration and frequency of requests | wamp up, ramp up, sustained, burst...      |
| payload   | describe the inputs for requests        | (stored in csv file)     |
| variables   | allows setting inputs from command line rather than csv         |         |
| defaults   | specify default headers for all requests        | Authorization...      |
| plugins   | for configuring plugins        | Cool that it's extentible, and there is a Datadog plugin...     |
| processor   | load custom JS code        |         |
| ensure   | Set success conditions for latency or error rates     | I think Steve will like this! 😋     |




# A Simple Config

Here's a relatively simple config that blasts the Aon home page 10 times (5 per second) over 2 seconds.

```
config:
  target: https://www.aon.com/home/index.html
  phases:
    - duration: 2
      arrivalRate: 5
      name: 10 request burst
  defaults:
    headers:
      x-api-key: "{{ $processEnvironment.SERVICE_API_KEY }}"
scenarios:
  - flow:
      - get:
          url: "/"
```

config:
  target: "https://shopping.service.staging"
  phases:
    - duration: 60
      arrivalRate: 5
      name: Warm up
    - duration: 120
      arrivalRate: 5
      rampTo: 50
      name: Ramp up load
    - duration: 600
      arrivalRate: 50
      name: Sustained load
  payload:
    # Load search keywords from an external CSV file and make them available
    # to virtual user scenarios as variable "keywords":
    path: "keywords.csv"
    fields:
      - "keywords"

<br/>

# Specifying Input Params

// TODO

<br/>

# Specifying Expected Responses

// TODO

<br/>


# Looking At The Performance Results

// TODO

<br/>


# Some Advantages Defining Load Tests With YAML
