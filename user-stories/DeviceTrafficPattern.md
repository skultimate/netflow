
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

### User Story #3 - View Utilization KPI's in Dashboard ###
1. Show data points of IN and OUT Utilization(%) traffic for last hour in a table and time series dotted plot for specific Interface in a device.

2.Show heatmap of interfaces in the network (for last 5 mins/ 1 hour)

![HeatMap of Interface Utilization](https://github.com/skultimate/netflow/blob/master/dashboard-examples/Interface-Utilization-HeatMap.png)
3. Hovering on the tile of the heatmap shows the summarized information of Interace Name (e.g. Gigabit Ethernet 0/1, Associated Device (e.g. Router-Datacenter-IPSA-ASA), Status: (Active, Unknown)
4. Clicking on the tile shall show the traffic pattern of the Interface in TimeSeries chart for last 1 hour averged every 5 min.
![Interface Utilization Details](https://github.com/skultimate/netflow/blob/master/dashboard-examples/Interface_Utilization_Details.png)
5. On the same chart, show the interface utilization traffic based on various categories
![Interface Utilization Details Categorized](https://github.com/skultimate/netflow/blob/master/dashboard-examples/Utilization_Details_Categorized.png)

**Top N Interfaces by Speed**

![Interface Utilization Details Categorized By Speed](https://github.com/skultimate/netflow/blob/master/dashboard-examples/TopNInterfacesBySpeed.png)

**Top N Interfaces by Utilization**

![Interface Utilization Details Categorized By Speed](https://github.com/skultimate/netflow/blob/master/dashboard-examples/TopNInterfacesByUtilization.png)


### Deliverable(s) ###

1. SAS KPI Analytic Engine components (Flink, K4M Engine and associated components) available as docker container(s).
2. SAS KPI Analytic Engine will be bundled with few out-of-box Netflow KPI's blueprints.
3. CRUD of KPI definitions and KPI Streams will be done using GUI interface.
4. Alternatively, users could access REST Service for CRUD operation of KPI definitions and streams.
5. Demos will be pre-configured for specific use cases and delivered as docker containers.
6. Database and Dashboard may be delivered as container(s) as part of demo.


### Tasks ###

1. Netflow Data Collector publishes flow data on the KAFKA bus.
2. Netflow data shall be made available on "PERFORMANCE" topic for subscription. Example of Netflow data on Kafka bus shown below:

```json
{
  "groupName" : "group",
  "variable" : "NbFlowPerExporter1.1.1.10numFlowsNF",
  "timestamp" : 1526370271,
  "value" : 30.0,
  "action" : "\u0000",
  "properties" : {
    "unit" : "nb",
    "vstatus" : "active",
    "ip" : "10.31.119.134",
    "name" : "numFlowsNF",
    "source" : "Netflow",
    "bunit" : "Default",
    "direction" : "0",
    "customer" : "Default"
  },
  "metrics" : {
    "numFlowsNF" : {
      "properties" : {
        "unit" : "nb",
        "name" : "numFlowsNF"
      },
      "value" : 30.0
    }
  },
  "relations" : [ ],
  "initialized" : true,
  "forceRefresh" : false
}
```

3. K4M queries Topology Service to get "Max Interface Speed" for the specific device based on IP address

```json
    {
        "aggregateTags": [
        ],
        "dps": {
            "1525305600": 5944000
        },
        "metric": "ifOutOctetsNF",
        "tags": {
            "ip": "10.31.119.134",
            "name": "ifOutOctetsNF",
            "unit": "bytes"
        },
        "topology": {
            "creationDate": "2018-05-04T18:43:39+05:30",
            "hierarchy": "Interface,NetworkAdapter,ICIM_NetworkAdapter,EMC_NetworkPort,CIM_NetworkPort,CIM_LogicalPort,ICIM_LogicalDevice,CIM_LogicalDevice,CIM_EnabledLogicalElement,ICIM_LogicalElement,CIM_LogicalElement,ICIM_ManagedSystemElement,CIM_ManagedSystemElement,ICIM_ManagedElement,CIM_ManagedElement",
            "name": "Interface::IF-10.31.117.5/10101",
            "topoProperties": {
                "Description": "GigabitEthernet0/1",
                "DisplayName": "IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134]",
                "MaxSpeed": "10000000",
                "Name": "IF-10.31.117.5/10101"
            },
            "type": "Interface"
        }
    },
```
4. K4M consumes Netflow data to compute In and Out Utilization KPI's every 5 min time window
5. K4M annonates computed KPI's with additional metadata (IP address, Device Name etc.) as tags
6. K4M publishes aggregated computed KPI back to KAFKA bus on a specific peformance topic.

```json
{
  "groupName" : "group",
  "variable" : "IN Utilization",
  "timestamp" : 1526370271,
  "value" : 80.0,
  "metrics" : {
    "inUtilization" : {
      "properties" : {
        "unit" : "percentage",
        "name" : "inUtilization"
      },
      "value" : 80.0
    }
  },
  "relations" : [ ],
  "initialized" : true,
  "forceRefresh" : false
}
```

7. TSDB Service stores raw metrics and K4M computed KPI's stores them TSDB datastore

```
TS#     			Metric      		Value	Tags          
---------------------------------------------------------------------------------------------------------
1526370271	   inUtilization    80.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
1526370470	   inUtilization    82.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
1526370670	   inUtilization    84.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
1526370271	   outUtilization   70.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
1526370470	   inUtilization    72.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
1526370670	   inUtilization    74.0   		Name=IF-10.31.117.5/10101 [Gi0/1] [10.31.119.134], IP=10.31.119.134
```
8. OpenTSDB is configured to do the following:
   - Store K4M computed 5 min KPI
   - Downsample K4M computed 5 min KPI's to every 1 hour, 1 day and 1 week
   - Downsample raw metrics from the collector every 1 hour, 1 day and 1week
   
9. Grafana is configured to show Utilization Traffic Pattern dashboard

10. Alerting rules in Grafana is configured to trigger WARNING Alert when Out Utilization > 50% for 30 mins for last hour.




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





