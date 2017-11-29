# webfux-issue around error handler

At first, run the spring-boot app.

    ./mvnw spring-boot:run

Then, access too many times using wrk or other performance testing tool.

    $ wrk http://localhost:8080
    Running 10s test @ http://localhost:8080
      2 threads and 10 connections
      Thread Stats   Avg      Stdev     Max   +/- Stdev
        Latency     4.86ms    9.28ms 135.18ms   92.36%
        Req/Sec     2.00k     1.02k    4.30k    59.00%
      39751 requests in 10.02s, 3.87MB read
    Requests/sec:   3968.69
    Transfer/sec:    395.34KB

Then, you'll get the stacktrace like this:

