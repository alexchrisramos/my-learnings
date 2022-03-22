Domain 1: Design Resilient Architectures
1.1 Design a multi-tier architecture solution
 Determine a solution design based on access patterns.
 Determine a scaling strategy for components used in a design.
 Select an appropriate database based on requirements.
 Select an appropriate compute and storage service based on requirements.
1.2 Design highly available and/or fault-tolerant architectures
 Determine the amount of resources needed to provide a fault-tolerant architecture across
Availability Zones.
 Select a highly available configuration to mitigate single points of failure.
 Apply AWS services to improve the reliability of legacy applications when application changes
are not possible.
 Select an appropriate disaster recovery strategy to meet business requirements.
 Identify key performance indicators to ensure the high availability of the solution.
1.3 Design decoupling mechanisms using AWS services
 Determine which AWS services can be leveraged to achieve loose coupling of components.
 Determine when to leverage serverless technologies to enable decoupling.
1.4 Choose appropriate resilient storage
 Define a strategy to ensure the durability of data.
 Identify how data service consistency will affect the operation of the application.
 Select data services that will meet the access requirements of the application.
 Identify storage services that can be used with hybrid or non-cloud-native applications.


AWS also has regions
also has AZ (availability zones)

Edge locations (AWS Points of presence)




Intro to IAM
users and groups
groups cannot contain groups
users can be a member of more than 1 group
permissions are defined by policies - you attach policies to groups
inline policy is a policy only attached to a user
IAM policy structure is JSON
    SID - statement ID
    Effect - allow or deny
    Principal - user/group that will take effect to
    Action - which API calls will be allowed/denied
    Resource - which resource/service
    Condition - when will policy will take effect

IAM MFA - you can activate MFA in IAM


How users can access AWS
    AWS Management console - protected by password and MFA
    AWS CLI - protected by access keys
    AWS SDK - protected by access keys

IAM roles - just like users but used by AWS services
    Common roles: ec2 role, lambda role, cloudformation role    

IAM security tools
    IAM Credentials report
    IAM Access advisor




Instances types
    explains different types

Ec2 instances purchasing options
    Ondemand instances  - short workoad, predictable pricing - highest cost but no upfront payment - recommended for short term
    Reserved (min 1yr) - 
        reserved instances - long workloads, up to 75% discount, longer year bigger discount, no/partial/all upfront - recommended for stady state instance like databases
        convertible reserved instances - long workloads with flexible instances, can change instance type, up to 54% discount
        scheduled reserved instances - example, every thurs between 3 and 6pm - still commitment over 1 to 3 yrs
    Spot instances - short workloads, cheap, can loose instances (less reliable) - can be up to 90% discount, but you can loose the instance if your max price is less than the current spot price - recommended for batch jobs, data analysis, image processing, distributed, flexible start and end time of workload
        Spot fleets - automatically request spot instances based on configured requirements
    Dedicated hosts - book an entire physical server, control instance placement - use case: compliance requirements and use existing server-bound software license, 3yr reservation period


EC2 SAA level
public, private and elastic IP
    public IP change when instance stop
    Use elastic IP so that IP will not change (associating EIP to instance will become the public IP address)


Ec2 placement groups
    Cluster - single AZ, low latency - if rack fails, all instance fails - high risk
    Spread - can span accross multiple AZ, min failure risk, use case - for critical applications
    Partition group - partition represents a rack in AWS, partition of ec2 in 1 AZ and other partition in other az - partition 1 and partition 2 in AZ-1 and Partition 3 in AZ-2 - uses cases are big data apps like cassandra, kafka

ENI - Elastic  network interface
    represents a virtual network card
    ex. Ec2 has eth0 - primary ENI
    eth1 - secondary ENI
    
    ENI's can have
    1 private IP, one or more secondary IP
    1 public ip
    1 ElasticIP per private IP
    1 Security group
    a Mac address

    ENI's are created independently, and can be attached/re-attached to EC2's
    ENI's are AZ bound

Ec2 Hibernate
    the in-memory state is preserved
    actually the RAM state is dumpped in the root EBS volume (needs to be encrypted)
    less than 150GB
    not more than 60days

EC2 Nitro
    New underlying platform for the next gen of ec2
    better performance (better networking, higher Speed EBS 64,000 IOPS vs 32,000 non nitro)
EC2 vCPU
    1 CPU = 1 Core
    2 thread = 2 thread (multithreading)
    Each thread is represented by a vCPU
    1 Core with 2 threads = 2 vCPU
    4 CPU with 2 threads per CPU => 8 vCPU total
EC2 capacity reservations
    if you need to make sure that a specific type of Ec2 instance would be available at a specific time, for a specific duration

EC2 AMI - customization of Ec2 instance
    AMI are region locked

EBS - Elastic block store volume is a network drive that you can attach to instances while they run
    AZ bound - to move to different AZ, do a snapshot
    1 instance per 1 EBS

EBS Volumes types
    gp2/gp3 - SSD, ggeneral purpose, systemboot, virtual desktops, dev/test environments - up to 16,000 IOPS
        gp3 - iops and throughput can be increased independent of each other
        gp2 - increasing volume also increases iops (not independent)
    io1 / io2 - high performance ssd - great for database workloads - greater than 16,000 IOPS
        MAX PIOPS of 64,000 for nitro and 32,000 for non nitro
        can increase PIOPS independently of storage size
        io2 has more durability and more IOPS per GiB at same price of io1
    io2 Block express - MA PIOPS of 256,000 with an IOPS per GiB ratio of 1000:1
    st1 - low cost HDD for frequently accessed
    sc1 - lowest cost HDD for less frequentlly accessed

EBS Multi attach - for io1 and io2 family
    attach 1 EBS volume to multiple Ec2 instances
    still AZ locked (same AZ)

EBS Encryption
    KMS aes-256
    data inside, intransit, and other action are encrypted
    can encrypt a volume by - create a snapshot > copy the snapshot (enable encryption during copy) > create a volume from the encrypted snapshot (or simply create a volume from snapshot with encyrption enabled)

EBS snapshots - make a backup (snapshot)
    can copy snapshots across AZ

EC2 instance Store - high performance hardware disk, ephemeral storage, use case for buffer storage

EFS - Elastic File System - NFS v4.1 protocol
    compatible to linux based AMI only (POSIX file system)
    file system scales automatically, pay-per-use, no capacity planning
    Managed network file system, can be mounted on any Ec2 on any AZ
    highly available, scalable and expensive
    uses security group to control access to EFS
    encryption at rest using KMS
    only pay what you use (stored)

    Performance mode
        general purpose - latency-senstive (web server, CMS)
        Max I/O - higher latency, throughput, highly parallel (big data, media processing)
    Throughput mode
        Bursting - (1TB = 50Milb/s + burst of up to 100mib/s) (increase througput together with size)
        Provisioned - set throughput regardless of storage size
    
    Storage Tiers (lifecycle management feature - move file after N days)
        Standard - frequently accessed files
        Infrequent access (EDS-IA) - cost to retrieve files, lower cost to store


EFS vs EBS



ELB - Elastic Load Balancer
    Classic Load Balancer (CLB) - v1 (old generation) - Deprecated
    Application Load Balancer (ALB - v2) - http,https,websocket
    Network Load Balancer (NLB - v2) - TCP, TLS, UDP
    Gateway Load Balancer - GWLB - operates at layer 3 (IP protocol)
    