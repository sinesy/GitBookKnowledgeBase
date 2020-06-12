# Setting up a cluster

An application can be considered heavy from the point of view the resources consumption when there is a high consumption of:

* CPU - when there are many concurrent user connections or when there are many batches to execute at the same time
* RAM - when there are SQL queries which fetch a lot of data to maintain in memory or when there are many concurrent user connections and consequently, a lot of memory consumed by user sessions
* bandwidth - when there are many concurrent user connections

The first step in order to optimize the resource consumption is to analyze what has been developed:

* are there SQL queries which read more than a hundred records? a paginated reading should be always preferred
* are there many batches on execution? a queue based management should be always preferred in order to execute batches and the number of queues must be limited and predictable \(not depending on data on the database or on data inputs\) and never grater than a few dozens
* are there an unlimited number of API invocations from external systems: this should be always avoided, by setting each web service with an upper limit on the number of invocations per second

Once you have carefully tuned your application with regards to what reported above, the only dimensions still out of control should be the number of end users using your system.

Starting from this perspective, a cluster can be introduced, in order to manage peaks on the use of your application.

Application functionalities can be divided into two categories:

1. online functionalities, i.e. the ones used by end users, through a UI
2. batch functionalities, not directly managed by end users, rather than by scheduled processed and/or batches managed by queues

Google Cloud Platform can help managing a cluster, composed of a set of VMs \(i.e. GCE instances\), each belonging to a specific instance group.

The Google Load balancer can then distribute incoming HTTP\(s\) requests to specific VMs, in order to manage online functionalities.

### 

### A cluster composed of exactly two nodes: online and batch

The cheapest solution is:

* Load balancer
  * VM1 - manages online functionalities; **this VM has a fixed IP**
  * VM2 - manages batch functionalities \(scheduled processed and queues\); **this VM has a fixed IP**

This represents the cheapest solution; anyway, it contains two bottlenecks: the VMs are fixed: if they collapse for some reason, the corresponding functionalities are not available any more. 

Consequently, **this solution is good for non multi-tenancy environments** \(e.g. one customer ad hoc installation\), where the resource consumption is predictable and reasonably under control.

In order to set the configurations reported above, you have to configure Platform through the AppDesigner; in the global properties, set the following properties:

* SCHEDULER - Fix the main node used by scheduled processes
* QUEUES - Fix the main node used by process queues

Do **not** activate the "Cluster" check-box in the Platform installation; that is to say, the WEB-INF/web.xml file should have the tag cluster set to false:

```text
                <init-param>
                        <param-name>cluster</param-name>
                        <param-value>false</param-value>
                </init-param>
```

### 

### A cluster composed of a fixed batch node and at least one node for the online

A better solution is the one where there is an instance group of the online nodes, whereas the batch node is out of this instance group, i.e. it is managed entirely by a fixed node.

In such a scenario, you can have this architecture:

* Load balancer
  * online instance group
    * VM1...VMn - manage online functionalities; these can be created on demand, by the Load balancer, according to the resource consumption
  * VMx - manages batch functionalities \(scheduled processed and queues\); **this VM has a fixed IP**

This solution has one only bottleneck: the VM for the batch environment is fixed: if it collapses for some reason, the batch functionalities are not available any more. Anyway, this is rarely a blocking problem and there is all the time to re-start the VM manually. Administrators can be notified by this event through Stackdriver monitoring system.

Consequently, this solution can be considered acceptable \(even though not the best\)  **for multi-tenancy environments**.

In order to set the configurations reported above, you have to configure Platform through the AppDesigner; in the global properties, set the following properties:

* SCHEDULER - Fix the main node used by scheduled processes
* QUEUES - Fix the main node used by process queues

Do **not** activate the "Cluster" check-box in the Platform installation; that is to say, the WEB-INF/web.xml file should have the tag cluster set to false:

```text
                <init-param>
                        <param-name>cluster</param-name>
                        <param-value>false</param-value>
 
```

### 

### A cluster composed of 1 instance group for online and batch

An nearly optimized solution is the one where there is an instance group containing online and batch nodes, that is to say, there is not a clear distinction between batch and online. 

In such a scenario, you can have this architecture:

* Load balancer
  * instance group
    * VM1...VMn - manage online functionalities; these can be created on demand, by the Load balancer, according to the resource consumption; only one of them will become the main node, i.e. the node dedicated to batch functionalities only.

In order to configure the environment as reported above, you have to activate the "Cluster" check-box in the Platform installation; that is to say, the WEB-INF/web.xml file should have the tag cluster set to true:

```text
                <init-param>
                        <param-name>cluster</param-name>
                        <param-value>true</param-value>
                </init-param>
```

In any case, you do **not have** to set the SCHEDULER and QUEUES settings \(main node\) as described in the previous sections. 

In this way, when starting the Platform web applications , each Platform web app will compete to become the main node; moreover, periodically each of these node will check out for the main node availability and can replace it with itself, in case it does not respond any more.



### A cluster composed of 2 instance groups for online and batch

An optimized solution is the one where there is an instance group of the online nodes, whereas the batch node is out of this instance group, i.e. within another instance group.

In such a scenario, you can have this architecture:

* Load balancer
  * online instance group
    * VMo1...VMon - manage online functionalities; these can be created on demand, by the Load balancer, according to the resource consumption
  * batch instance group
    * VMb1..VMbn - manages batch functionalities \(scheduled processed and queues\); these can be created on demand, by the Load balancer, according to the resource consumption

In order to configure the environment as reported above, you have to distinguish between online and batch instance groups, each having its own template VM:

* the online template VM should have the cluster flag set to false
* the batch template VM should have the cluster flag set to true

In any case, you do **not have** to set the SCHEDULER and QUEUES settings \(main node\) as described in the previous sections. 

In this way, when starting the Platform web applications for  the batch instance group, each Platform web app will compete to become the main node; moreover, periodically each of these node will check out for the main node availability and can replace it with itself, in case it does not respond any more.



