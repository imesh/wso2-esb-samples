# WSO2 ESB Echo API

This is a sample API implemented using payload factory mediator for generating a response message using a URI parameter and server IP address.

## How to run:

1. Start the ESB and copy sequences and API definition found inside echo-api folder into following folders:
    ```
    ESB_HOME/repository/deployment/server/synapse-configs/default/api/
    ESB_HOME/repository/deployment/server/synapse-configs/default/sequences/
    ```
2. Make a CURL request to the echo API:

    ```bash
    curl -v http://<esb-hostname>:8280/echo/WSO2
    ```

3. A sample response similar to the following should be received:

    ```bash
    > GET /echo/WSO2 HTTP/1.1
    > Host: 192.168.1.2:8280
    > User-Agent: curl/7.49.1
    > Accept: */*
    >
    < HTTP/1.1 200 OK
    < Host: 192.168.1.2:8280
    < Content-Type: application/json
    < Accept: */*
    < Date: Fri, 28 Oct 2016 04:52:26 GMT
    < Server: WSO2-PassThrough-HTTP
    < Transfer-Encoding: chunked
    <
    * Connection #0 to host 192.168.1.2 left intact
    {"response":{"hello":"WSO2","server-ip":"192.168.1.2"}}
    ```