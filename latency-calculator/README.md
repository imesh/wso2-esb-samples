## Latency Calculator

```XML
<?xml version="1.0" encoding="UTF-8"?>
<proxy xmlns="http://ws.apache.org/ns/synapse"
       name="LatencyCalculator"
       transports="https,http"
       statistics="enable"
       trace="disable"
       startOnLoad="true">
   <target>
      <inSequence>
         <property name="MEDIATION_START_TIME" expression="get-property('SYSTEM_TIME')"/>
      </inSequence>
      <outSequence>
         <property name="ENDPOINT1_NAME" value="Endpoint 1"/>
         <property name="ENDPOINT1_START_TIME" expression="get-property('SYSTEM_TIME')"/>
         <send/>
         <property name="ENDPOINT1_END_TIME" expression="get-property('SYSTEM_TIME')"/>
         <property name="MEDIATION_END_TIME" expression="get-property('SYSTEM_TIME')"/>
         <property name="ENDPOINT1_LATENCY" expression="number(number(get-property('ENDPOINT1_END_TIME')) - number(get-property('ENDPOINT1_START_TIME')))" type="DOUBLE"/>
         <property name="MEDIATION_LATENCY" expression="number(number(get-property('MEDIATION_END_TIME')) - number(get-property('MEDIATION_START_TIME')))" type="DOUBLE"/>
         
         <log>
            <property name="Mediation Start Time"
                      expression="get-property('MEDIATION_START_TIME')"/>
            <property name="Endpoint1 Start Time"
                      expression="get-property('ENDPOINT1_START_TIME')"/>
            <property name="Endpoint1 End Time"
                      expression="get-property('ENDPOINT1_END_TIME')"/>
            <property name="Mediation End Time"
                      expression="get-property('MEDIATION_END_TIME')"/>
            <property name="Endpoint Latency" expression="get-property('ENDPOINT1_LATENCY')"/>
            <property name="Mediation Latency" expression="get-property('MEDIATION_LATENCY')"/>
         </log>
      </outSequence>
      <endpoint>
         <address uri="http://localhost:9000/services/SimpleStockQuoteService"/>
      </endpoint>
   </target>
   <publishWSDL uri="http://localhost:9000/services/SimpleStockQuoteService?wsdl"/>
   <description/>
</proxy>
```

This is a sample proxy service implemented for calculating mediation and endpoint latency. Please note that in this sample the timestamps have been taken inside the IN and OUT sequences and it would not include the time that message would take to mediate through other layers of the ESB (please see [ESB architecture diagram](https://docs.wso2.com/display/ESB481/Architecture) for more details). Therefore the latency values calculated in this sample would exclude some Tn time. If more acurate latency values are needed please use [JMX Monitoring](https://docs.wso2.com/display/ESB481/JMX+Monitoring) feature.

### How to run:

1. Add above LatencyCalculator proxy service to the ESB.
2. Build SimpleStockQuoteService found in samples folder. This service is used as the backend endpoint in the LatencyCalculator proxy service:

    ```bash
    cd $ESB_HOME/samples/axis2Server/src/SimpleStockQuoteService
    ant 
    ```

3. Start Axis2 server found in samples folder:

    ```bash
    cd $ESB_HOME/samples/axis2Server
    ./axis2server.sh
    ```

4. Log into ESB management console and navigate to Manage -> Services -> List.
5. Click on "Try this service" link of "Latency Calculator" proxy service.
6. Add 1 to the request parameter and press the "Send" button:

    ```
    <body>
    <p:getFullQuote xmlns:p="http://services.samples">
        <!--0 to 1 occurrence-->
        <ax21:request xmlns:ax21="http://services.samples">
            <!--0 to 1 occurrence-->
            <xs:symbol xmlns:xs="http://services.samples/xsd">1</xs:symbol>
        </ax21:request>
    </p:getFullQuote>
    </body>
    ```

7. Check the ESB log, a log entry similar to following will get printed with the latency values (unit = milliseconds):

    ```bash
    [2016-10-28 09:55:06,965]  INFO - LogMediator To: http://www.w3.org/2005/08/addressing/anonymous, WSAction: , SOAPAction: , MessageID: urn:uuid:548642f1-b8ed-4fe7-9c77-e7f348a22e1b, Direction: response, Mediation Start Time = 1477628705731, Endpoint1 Start Time = 1477628706834, Endpoint1 End Time = 1477628706965, Mediation End Time = 1477628706965, Endpoint Latency = 131.0, Mediation Latency = 1234.0
    ```