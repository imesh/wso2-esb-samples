# WSO2 ESB Transaction Handler Sample

This sample illustrates how database transactions can be handled in WSO2 ESB. Have a look at the [transaction_api_in_seq](TransactionApiESBConfig/src/main/synapse-config/sequences/transaction_api_in_seq.xml) for the mediation logic.

## How To Run

1. Start a MySQL database instance using Docker by executing the following command:
   
   ```bash
   docker run -p 3306:3306 —name customers-db-mysql -e MYSQL_ROOT_PASSWORD=mysql -e MYSQL_USER=mysql -e MYSQL_PASSWORD=mysql -d mysql:5.5
   ```

2. Execute the below commands for creating a sample customers table and inserting a record:

   ```bash
   mysql -h 127.0.0.1 -u root -p
   
   CREATE DATABASE customers;
   USE customers;
   CREATE TABLE `customers` (
      `id` int(11) NOT NULL,
      `name` varchar(45) DEFAULT NULL,
      PRIMARY KEY (`id`)
   ) ENGINE=InnoDB DEFAULT CHARSET=latin1;

   INSERT INTO customers VALUES(1, 'Customer 1');
   ```

3. Add the below data source configuration to the ESB-HOME/repository/conf/datasources/master-datasources.xml file:
   
   ```xml
   <datasource>
       <name>customers_ds</name>
       <description>The datasource used for registry and user manager</description>
       <jndiConfig>
           <name>jdbc/CustomersDS</name>
       </jndiConfig>
       <definition type="RDBMS">
           <configuration>
               <url>jdbc:mysql://localhost:3306/customers</url>
               <username>root</username>
               <password>mysql</password>
               <driverClassName>com.mysql.jdbc.Driver</driverClassName>
               <maxActive>80</maxActive>
               <maxWait>60000</maxWait>
               <minIdle>5</minIdle>
               <testOnBorrow>true</testOnBorrow>
               <validationQuery>SELECT 1</validationQuery>
               <validationInterval>30000</validationInterval>
           </configuration>
       </definition>
   </datasource>
   ```

4. Build TransactionApiESBConfig project using Maven:

   ```
   cd TransactionApiESBConfig
   mvn clean install
   ``` 

5. Build TransactionApiCAR project using Maven:

   ```
   cd TransactionApiCAR
   mvn clean install
   ``` 

6. Copy TransactionApiCAR/target/TransactionApiCAR_1.0.0.car file to ESB-HOME/repository/deployment/server/carbonapps folder and start the ESB.

7. Execute the below curl command to send a request message to the transaction API:

   ```bash
   curl -v http://127.0.0.1:8280/transaction/
   ```

8. If everything went well customer name should be updated to the message id in the database and ESB should print a log something similar to below:

   ```bash
   TID: [-1234] [] [2017-01-11 17:51:59,603]  INFO {org.apache.synapse.mediators.builtin.LogMediator} -  log = Updating customer 1..., message-id = urn:uuid:485cfa0f-f7e6-48b8-8dbd-bb13ddb1842b {org.apache.synapse.mediators.builtin.LogMediator}
   ```