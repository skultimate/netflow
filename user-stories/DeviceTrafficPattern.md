
Monitoring Interface Traffic Pattern
==============================================================


Who is the user?
---------------------
NoC operator in a datacenter



What do they need?
---------------------
As an operator in a NoC, I would like to monitor **Utilization** of interfaces of all critical devices  (X switches, X routers, X hosts etc.)  in my branch location(s) conneced to LAN/ WAN network based on various KPI’s (IN Utilization (%), OUT Utilization (%), Max Utilization(%) etc.) calculated every 5 mins in near real-time, so that they can proactively trigger **"Excessive Utilization"** alerts if the interface utilization crosses above threshold of 50 percent. The subscribers of "Excessive Utilization" alert could be the following downstream service(s):

- **RCA Service** correlating  "Excessive Interface Utilization" to failures/exceptions in the network. 
- **Topology Service** identifing tenant services and applications.  
- **Reporting Service** reporting Utilization KPI's and Alerts in a dashboard (scatter plot, time series chart, tabular format, heat map etc.)


*Note: still need to add more details to the user story on how RCA and Topology service leverages Utilization KPI.*

KPI's need to be tracked per Device (based on device name, IP address) and/or per Location (location name):

1. IN Utilization(%) and OUT Utilization (%) calculated by averaging Speed IN(bps) and Speed Out(bps) metrics and emitting average value in a  5 min sliding window.
2. Max of IN and OUT Utilization(%) KPI (calculated in step 1) for the last hour.
3. Standard Deviation of IN and OUT Utilization(%) KPI (calculated in step 1) for the last hour.
4. 95th percentile of IN and OUT Utilization(%) KPI (calculated in step 1) for the last hour.

What do the customers currently do?
--------------------------

Typically in most of current SAS deployments, customers (Verizon, BT etc.) use 3rd party traffic monitoring tools  (provided by competitor products like HP, Sevone) or MnR to monitor network traffic. 





Why do they need a solution from Dell EMC?
---------------------
Customers need a end to end solution complimenting SAS RCA technology which can correlate the performance KPI's and metrics, and pinpoint the actual cause of  of the excessive utilization in the network. Also, allow customer to identify the impacted tenants, service and application supported by SAS topology.



What is the suggested solution?
--------------------------------

KPI Engine called as **"SAS KPI Analytic Engine"** will be delivered as  add-on component to SAS providing operators ability to define Utilization KPI tracking Interface Utilization of critical devices in the network based on Netflow or sFlow or IPSLAdata. For the suggested solution to work, Netflow or sFlow or IPSLA need to be enabled on critical devices.


What are the development User Stories? 
----------------------------


### User Story #1 - Calculate Utilization IN(%) and Utilization OUT(%) (every 5min)###

1. Capture Speed IN, Speed OUT metrics from Netflow.
2. Calculate Utilization per 5 min interval
      * Utilzation IN(%) =   PERCENTAGE(DIV (AVG(Speed IN for last 5 mins)/Interface Speed)))
      * Utilization OUT(%) = PERCENTAGE(DIV(AVG(Speed OUT for last 5 mins)/Interface speed)))
3. Aggregate Utilization IN(%) and Utilization OUT(%) based on **location**. 
4. Aggregate Utilization IN(%) and Utilization OUT(%) based on **device**.

*Note: Interface Speed if not available in Netflow, need to be provided as input KPI engine from an alternate data source*

### User Story #2 - Calculate Max, Min, Avg, Standard Deviation and 95th percentile of Speed IN/ Speed OUT Utlization (every 1hour)###

1. Capture Utilization IN(%)/ Utilization OUT (%) KPI calculated per 5min and Utilization OUT(%) per 5 min.
2. Compute Max(Utilization IN(%) per 5min) and Max (Utilization OUT(%) per 5 min) for last hour.
3. Compute Avg(Utilization IN(%) per 5min) and Avg (Utilization OUT(%) per 5 min) for last hour.
4. Compute 95th percentile(Utilization IN(%) per 5min) and 95th percentile(Utilization OUT(%) per 5 min) for last 1 hour.
5. Compute Standard Deviation (Utilization IN(%) per 5min) and 95th percentile(Utilization OUT(%) per 5 min) for last 1 hour.

### User Story #3 - View KPI's in Dashboard ###
1. Show data points of IN and OUT Utilization(%) traffic for last hour in a table and time series dotted plot for specific Interface in a device.
2. Show  Avg, Max, IN and OUT Utilization(%) traffic for last 1 hour in a table for or specific Interface in a device.


###User Story #4 - Create new KPI Definition ###

1. KPI Admin logs into the KPI Engine.
2. Home screen shows *KPI Activity Feed* with the list of KPI Definitions and KPI Streams available in the KPI engine.
2. KPI Admin Clicks on *Create KPI Definition* button.
3. KPI Definition screeen opens with list of options  and KPI admin feeds input to calcuate the KPI : 
      - **Name** - name of the KPI definition (e.g. Interface Uilization)
      - **Window** - Window options for Utilization(%) KPI calculation (e.g Sliding Window, Sliding Period in ms, Window Size in ms)
      - **Grouping** - Specifies Grouping option on the KPI to be dimension (e.g. Device or Location)
      - **Metrics** - Selects metrics for KPI calculation (e.g. Speed IN bytes/sec, Speed OUT bytes/sec)
      - **Expression** - Specifies the Utilization KPI formulae (e.g. PERCENTAGE(DIV(AVG(Speed OUT for last 5 mins)/Interface speed)))
4. KPI Admin Saves the KPI Definition and message pops up stating **KPI is sucessfully saved**.
5. Newly defined KPI is shown in the Activity Feed of KPI Definitions.

*Note- client side validation need to be available to assist the user*

###User Story #5 - View KPI Definition ###
1. KPI Admin logs into the KPI Engine.
2. Home screen shows *KPI Activity Feed* of pre-defined KPI Defintions and KPI Streams available for use.
3. KPI Admin clicks on the KPI Definition or KPI Stream to view them.


### User Story #6 - Copy existing KPI Definition ###
1. KPI Administrator logs into the KPI Engine.
2. Home screen shows *KPI Activity Feed* list of KPI Definitions and KPI Streams available in the KPI engine.
3. KPI Admin clicks on the KPI Definition or KPI Stream to view them.
4. KPI Admin clicks on the Clone or Copy KPI definition 
5. Saves the KPI definition under a new name.

### USer Story #7 - Create new KPI Stream ###
1. KPI Administrator logs into the KPI Engine.
2. Home screen shows **KPI Activity Feed** shows list of KPI Definitions and KPI Streams available in the KPI engine.
3. KPI Admin clicks on **Create KPI Stream** button
4. KPI Stream Definition screen shows pre-configured KPI Definition  (e.g. Interface Utilization)
5. KPI Stream Definition screen **shows various input components (e.g. KAFKA, File, Influx DB, Cassandra DB)** available as input to KPI computation.
5. KPI Stream Definition screen **shows various output components (e.g. KAFKA, File, Influx DB, Cassandra DB)** available as destinations to publish the computed KPI's.
6. KPI Admin **drags and drops relavent input components, KPI Definition(s) and output components** for wiring KPI Stream for computing Interface Utilization KPI. KPI admin feeds input:
       - **Input Component** - NetFlow Data Source available on KAFKA Topic Name (NetFlow Performance), Format of Data (NetFlow Format) and Kafka Broker Details (Host Name or IP address, port etc.)
       - **KPI Definition** - Utilization KPI Definition 
       - **Output Component** - Cassandra or Influx or KAfka Component with relavent details
7. KPI Admin **wires Components** (NetFlow Performance) -> KPI Definition (Utilization) -> Output Component to create stream definition.
8. KPI Admin also Inputs a name of the Stream Definition
9. KPI Admin inputs any User Defined Tags to tag the computed KPI's (Optional)
8. KPI Admin clicks **Deploy** button to deploy the stream.
9. Message shown "Sucessfully Deployed" or "Error Message..."???

### Deliverable(s) ###

1. SAS KPI Analytic Engine components (Flink, K4M Engine and associated components) available as docker container(s).
2. SAS KPI Analytic Engine will be bundled with few out-of-box Netflow KPI's blueprints.
3. CRUD of KPI definitions and KPI Streams will be done using GUI interface.
4. Alternatively, users could access REST Service for CRUD operation of KPI definitions and streams.
5. Demos will be pre-configured for specific use cases and delivered as docker containers.
6. Database and Dashboard may be delivered as container(s) as part of demo.


### Demo Scenario ###
1. Show heatmap of interfaces in the network (for last 5 mins/ 1 hour)

![HeatMap of Interface Utilization](https://github.com/skultimate/netflow/blob/master/dashboard-examples/Interface-Utilization-HeatMap.png)
2. Hovering on the tile of the heatmap shows the summarized information of Interace Name (e.g. Gigabit Ethernet 0/1, Associated Device (e.g. Router-Datacenter-IPSA-ASA), Status: (Active, Unknown)
3. Clicking on the tile shall show the traffic pattern of the Interface in TimeSeries chart for last 1 hour averged every 5 min.

![Interface Utilization Details](https://github.com/skultimate/netflow/blob/master/dashboard-examples/Interface_Utilization_Details.png)


### Non-Functional User Stories (not covered) ###

1. Check health of the KPI stream post deployment.
2. Single Node Deployment of KPI engine.
3. Multi-Node Deployment of KPI engine.
4. HA and DR deployment of KPI engine.
5. Upgrade KPI engine
6. Scaling of KPI Engine

    
       
       	
Notes
----------------------------
1. The user story will be delivered based on NetFlow data.
2. Need sample Netflow data **representative** of real world "excessive utilization" scenario so that we can fundamental asummptions based on the dataset.
3. Until Netflow sample is available, K4M team will mockup the data using simulator.
3. To show E2E use case, topology need to be discovered in SAS reflecting the Netflow Data.
4. The data model for Netflow on KAFKA will be similar to the one used in MARS.
5. We will use Influx or Open TSDB to show 1 hour KPIs.
6. KPI Dashboard will be based on Grafana.
7. Mockups will be provided for the UI user stories.
8. TSDB need to have capability to trigger alerts.
9. KPI engine is for calcuating KPI's
10.KPI Engine Flink could be leveraged for alerting. Trigger only when breach has happened for a specific period of time.
11. 





