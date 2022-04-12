## Yarn Architecture

![](../../../pictures/yarn.gif)

### 1. ResourceManager (RM)

RM is a global resource manager responsible for resource management and allocation of the entire system. It mainly consists of two components:

1. Scheduler
2. Application Manager (ASM)

#### Scheduler

The scheduler allocates resources in the system to each running application according to constraints such as capacity and queues (ex. assigning certain resources to a queue, executing a maximum number of jobs, etc.). It should be noted that the scheduler is a pure scheduler, it no longer does any work related to the application, such as not responsible for restarts (failures due to application failure or hardware failure), which are left to the application. The associated ApplicationMaster completes. The scheduler only allocates resources according to the resource requirements of each application, and the resource allocation unit uses an abstract concept: **resource container**. Containers are dynamic resource allocation units, which combines memory, CPU, disk, network and other resources, encapsulated together, thereby limiting the amount of resources used by each task. In addition, the scheduler is a pluggable component, and users can design new schedulers according to their own needs. YARN provides a variety of directly available schedulers, such as Fair Scheduler and Capacity Schedule.

#### Application Manager

The application manager is responsible for managing all applications in the entire system, including application submission, negotiating resources with the scheduler for ApplicationMaster, monitoring the ApplicationMaster running status and restarting it if it fails, etc.

### 2. NodeManager (NM)

NM is a resource and task manager running on each node. On the one hand, it regularly reports the resource usage on the node and the running status of each Container to RM; on the other hand, it receives and processes Container startup from AM. /stop and other various requests.

### 3. ApplicationMaster (AM)

Each submitted job will contain an AM with key features including:

1. Negotiate with the RM for resources (represented by container);
2. Further assign the obtained tasks to internal tasks;
3. Communicate with NM to start/stop tasks;
4. Monitor the running status of all tasks. When a task fails, re-apply for resources for the task and restart the task.

MapReduce is a framework that natively supports ON YARN and can run MapReduce jobs on YARN. Many distributed applications have developed corresponding application frameworks for running tasks on YARN, such as Spark, Storm, Flink, etc.

### 4. Container

Container is a resource abstraction in YARN. It encapsulates multi-dimensional resources on a node, such as memory, CPU, disk, network, etc. When AM requests resources from RM, the resources returned by RM for AM are represented by Container. YARN assigns each task a Container and the task can only use the resources described in that Container.