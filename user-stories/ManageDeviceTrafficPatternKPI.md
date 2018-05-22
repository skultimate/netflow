
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

###User Story #1 - Create new KPI Definition ###

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

###User Story #2 - View KPI Definition ###
1. KPI Admin logs into the KPI Engine.
2. Home screen shows *KPI Activity Feed* of pre-defined KPI Defintions and KPI Streams available for use.
3. KPI Admin clicks on the KPI Definition or KPI Stream to view them.


### User Story #3 - Copy existing KPI Definition ###
1. KPI Administrator logs into the KPI Engine.
2. Home screen shows *KPI Activity Feed* list of KPI Definitions and KPI Streams available in the KPI engine.
3. KPI Admin clicks on the KPI Definition or KPI Stream to view them.
4. KPI Admin clicks on the Clone or Copy KPI definition 
5. Saves the KPI definition under a new name.

### Deliverable(s) ###

1. SAS KPI Analytic Engine components (Flink, K4M Engine and associated components) available as docker container(s).
2. SAS KPI Analytic Engine will be bundled with few out-of-box Netflow KPI's blueprints.
3. CRUD of KPI definitions and KPI Streams will be done using GUI interface.
4. Alternatively, users could access REST Service for CRUD operation of KPI definitions and streams.
5. Demos will be pre-configured for specific use cases and delivered as docker containers.
6. Database and Dashboard may be delivered as container(s) as part of demo.




