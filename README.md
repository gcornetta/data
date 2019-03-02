![NEWTON BANNER](/docs/images/newton.png)

# About
<p align="justify">
  This data has been collected by <em>Javier Mateos</em> and <em>Gianluca Cornetta</em> within <b>NEWTON</b> project. <b>NEWTON</b> is a large scale Integrated Action, started in March 2016 and scheduled to end in summer 2019, funded by the European Commission under the Horizon 2020 Researh and Innovation Programme with grant agreement n. 688503.
</p>

# Fab Lab Modules: Experimental Data
<p align="justify">
This repo contains the experimental data of the NEWTON Fab Lab Platform.
</p>

```
 __ cloudsim __
|              |
|              |____ 10 (Simulation results with 10 threads concurrency level)
|              |
|              |____ 50 (Simulation results with 50 threads concurrency level)
|
|
|__ locust   __
|              |
|              |___ 50  (Load test results with 50 concurrent users)
|              |
|              |___ 100 (Load test results with 100 concurrent users)
|              |
|              |___ 150 (Load test results with 150 concurrent users)
|
|
|__ ab       ______ (Apache Benchmark measurements)
|
|
|__ iperf3   ______ (iperf3 measuremnts)
```

# Some remarks on Apache Benchmark measuremnts
<p align="justify">
For each measurement scenario, Apache benchmark reports many Document length errors. This indeed is not an error but simply a mismatch in the response length probably due to some server misconfiguration that does not affect the correct operation of the NEWTON Fab Lab infrastructure.  Analyzing more in detail the measurement report, we also observe that, in all the measuremnts we performed, Apache benchmark also reports a large number of non-2xx responses. Interestingly, the number of non-2xx responses is approximately equal to the number of Document length errors. In order to investigate the causes of this anomalous behaviour, we run a fast analysis with the following flags:
</p>

```
$ ab –k –n 1000 –c 10 –v 2 http://api.cloudhub.paas.uspcloud.eu
```

<p align="justify">
The –v flag with verbosity level 2 allows printing the details of the reponse header sent by the server. Inspecting the measurement log file one can realize that all the non-2xx responses detected by Apache benchmark are 302-Found responses. An excerpt of the measurement output is shown in the sequel.
 </p>
 
```
 1  LOG: header received:
 2  HTTP/1.1 302 Found
 3  Cache-Control: no-cache
 4  Content-length: 0
 5  Location: https://api.cloudhub.paas.uspcloud.eu/
 6  Connection: close
 7  WARNING: Response code not 2xx (302)
 8
 9  LOG: header received:
10  HTTP/1.0 200 OK
11  Access-Control-Allow-Headers: Origin, X-Requested-With, Content-Type, 12  Accept, Authorization, Authentication, newtonUser
13  Access-Control-Allow-Methods: GET,PUT,POST,DELETE,PATCH,OPTIONS
14  Access-Control-Allow-Origin: *
15  Content-Length: 2
16  Content-Type: application/json; charset=utf-8
17  Date: Fri, 16 Nov 2018 05:47:49 GMT
18  Etag: W/"2-vyGp6PvFo4RvsFtPoIWeCReyIC8"
19  X-Powered-By: Express
20  {} 
```

<p align="justify">
Lines 1 to 7 show the log when the server is issuing 302-Found response code. A 302-Found response code indicates that the requested resource should temporarily be accessed at a different URL. This is not a real issue since the client browser automatically manages the redirection  because the server includes in the response a special Location header. This Location header indicates the new URL where the requested resource can be found. In our specific case, the server redirects a request to <strong>api.cloudhub.paas.uspcloud.eu</strong> using HTTPS protocol. Conversely, lines 9 to 20 show the log file in the case of correct server response. More precisely, lines 10 to 19 are the response header, whereas line 20 is the response body (an empty JSON in our case). It is clear that the mismatch between the headers for 200 (OK) and 302 (Found) responses makes Apache benchmark to flag an error.  This behaviour is probably due to a misconfiguration  in some of the cloud servers managed by Amazon.
 </p>

# License
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.
