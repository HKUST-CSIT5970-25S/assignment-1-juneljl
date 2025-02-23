[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)

# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: LIAN Junle
### Student Id: 21070797
### Email: jlianad@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

   > In Phoronix Test Suite, I choose SysBench which can test CPU, memory and other performance metrics.
   >
   > For testing CPU: **sysbench cpu --cpu-max-prime=20000 run**
   >
   > **--cpu-max-prime=20000**
   > This parameter defines the upper limit for calculating prime numbers, which means SysBench calculates all prime numbers between 2 and 20000. 
   >
   > Reasons:
   >
   > - Test complexity:
   >   Calculating prime numbers is a typical CPU intensive task, and the larger the upper limit of computing prime numbers, the greater the computational load and the higher the CPU load. Setting 20000 is a moderate value that can effectively test CPU performance without prolonging the testing time.
   >   - If set too small (such as 1000), the test may not fully exploit the performance of the CPU, and the results may not be accurate enough.
   >   - If set too large (such as 100000), the testing time will significantly increase, but it may not bring more performance information.
   >
   > - Balance testing time and accuracy:
   >   20000 is an experience value that can complete the test within a reasonable time (usually a few seconds to tens of seconds), while providing sufficient computing power to reflect the performance of the CPU.
   >
   > Result:
   >
   > ```
   > CPU speed:
   >     events per second:  874.78
   > ```
   >
   > This value represents the number of events completed per second. The higher the value, the better the computing performance of the CPU.

   

   > For testing memory: **sysbench memory --memory-block-size=1K --memory-total-size=100G run**
   >
   > **--memory-block-size=1K**
   > This parameter defines the size of the data block for each memory operation. 
   >
   > Reasons:
   >
   > - Simulate real load:
   >   The block size of memory operations can affect the load characteristics of the test. 1K is a smaller block size that is suitable for simulating common memory access patterns such as small object allocation, cache line access, etc.
   >   - If the block size is set too large (such as 1M), the test may reflect more on the performance of large block memory operations rather than common small block memory operations.
   >   - If the block size is set too small (such as 1B), the test may be affected by memory management overhead, resulting in inaccurate results.
   >
   > **--memory-total-size=100G**
   > This parameter defines the total amount of memory operation data in the test. 
   >
   > Reasons:
   >
   > - Ensure the adequacy of testing:
   >   100G is a large value that ensures sufficient memory operations are covered during testing, reflecting the system's memory performance under long-term, high load conditions.
   >   - If set too small (such as 1G), the test may not fully squeeze the memory subsystem and the results may not be accurate enough.
   >   - If set too large (such as 1T), the testing time will significantly increase, but it may not bring more performance information.
   >
   > Result:
   >
   > ```
   > 5315.42 MiB transferred(531.38 MiB/sec)
   > ```
   >
   > TThis value represents the transfer speed and total transfer amount of memory. MiB/sec represents the amount of data transmitted per second, with higher values indicating better memory performance.

   

2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?

In order to answer this question, you need to complete the following table by filling out blanks with the measurement results corresponding to each instance type.

| Size        | CPU performance | Memory performance |
| ----------- | --------------- | ------------------ |
| `t2.micro` | events per second:  874.78 | 531.38 MiB/sec |
| `t2.medium`  | events per second:  883.85 | 532.87 MiB/sec |
| `c5d.large` | events per second:  2476.53 | 6035.30 MiB/sec |

> Overall, the performance of EC2 instances is not always directly proportional to an increase in the number of vCPUs and memory resources. In some cases (such as CPU performance increasing from t2.medium to c5d. large), the performance improvement is significant, which is consistent with the increase in resources. However, in other cases (such as memory performance changing from t2.micro to t2.medium), the performance improvement is not significant, which may be influenced by other factors such as instance architecture, network performance, etc.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | `t3.medium` - `t3.medium` | 3430           | 0.308    |
    | `m5.large` - `m5.large`   | 4790           | 0.246    |
    | `c5n.large` - `c5n.large` | 4780           | 0.213    |
    | `t3.medium` - `c5n.large` | 4340           | 0.609    |
    | `m5.large` - `c5n.large`  | 4550           | 0.784    |
    | `m5.large` - `t3.medium`  | 4270           | 0.319    |

    > - Same type instances:
    >
    >    Network performance is usually better between instances of the same type, manifested as higher TCP bandwidth and lower RTT. Especially between c5n.large instances, the TCP bandwidth is the highest, reaching 4780 Mbps, and the RTT is the lowest, only 0.213 ms.
    >
    > - Different types of instances: 
    >
    >   Network performance may decrease between different types of instances, manifested as lower TCP bandwidth and higher RTT. For example, the TCP bandwidth between t3.medium and c5n.large is 4340 Mbps, and the RTT is 0.609 ms, which is worse than the performance between c5n.large instances.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Mbps) | RTT (ms) |
    | ------------------------- | -------------- | -------- |
    | N. Virginia - Oregon      | 470            | 60.758   |
    | N. Virginia - N. Virginia | 4770           | 0.151    |
    | Oregon - Oregon           | 4780           | 0.204    |

    > - Bandwidth: The bandwidth within the same region (such as N. Virginia to N. Virginia, or Oregon to Oregon) is significantly higher than the bandwidth across regions (such as N. Virginia to Oregon). This indicates that network connections within the same area typically have higher data transmission rates.
    > - Delay: The RTT within the same region is significantly lower than the inter regional delay. For example, from N. Virginia to N Virginia's RTT is 0.151 ms, while N The RTT from Virginia to Oregon is 60.758 ms. This indicates that network connections within the same area not only have higher bandwidth, but also lower latency.
