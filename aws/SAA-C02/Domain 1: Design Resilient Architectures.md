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


CLB - supports layer 4 and layer 7
    fixed target hostname

ALB - Application load balancer
    layer 7 - http
    load balance multiple http apps to different target groups (backend)
    load balance multiple apps to single machine target group - but containerized
    support redirects - from https to http
    routing table can be based on
        path URL
        hostname in URL
        Query string in headers
    ALB is great fit for containerized
    has a port mapping feature to redirect to dynamic port
    target groups can be multiple
        EC2 instances - even instances managed by auto-scaling group
        ECS tasks
        Lambda functions
        Private IP addresses
    Health checks/probes are on target group level
    note: app servers do not see the client's IP, only the load balancer IP
    to see client IP, need to look at header X-forwarded-for

NLB - layer 4 (TCP / UDP)
    high performance - millions of request per second
    supports 1 static IP per AZ (for whitelisting)
    supports assigning of EIP
    Target groups can be:
        Ec2 instances
        private IP addresses
        ALB
    
GWLB - Gateway load balancer
    Deploy, scale and manage 3rd party network appliance (firewall, IDPS)
    requests > GWLB > 3rd party net appliance (analyze) > return to GWLB > route to backend/target group
    note: if there is a GENEVE protocol on port 6081 - it means its GWLB

Sticky sessions in ELB
    reroute the client to previously redirected instance
    feature of CLB and ALB
    stickiness is controlled by "cookies"
        Application based cookies - Custom cookie is generated by application (can control the duration and name and other attributes) - dont use cookie names already used by ELB. Application cookie is generated by ELB, no customization.
        Duration based cookies - generated by ELB

Cross-Zone load balancing
    scenario:
        traffic is load balanced between 2 target groups (A, B) that is from 2 different AZ
        A has 2 backend Ec2, B has 8 backend Ec2

        With cross-zone load balancing
        traffic is ditributed evenly on all backend ec2 - A Ec2's will get the same load as the B Ec2's eventhough A is only 2 instance
        Without cross-zone load balancing
        traffic is ditributed on a fixed downstream effect - A group receives 50% B group 50% - therefore the 2 instances of A group will be receiving 25% of the load vs the 6.25% of the other instances of B group

    On ALB, always enabled, and cannot be disabled - no fee for inter-AZ data transfer
    on NLB, disabled by default, can be enabled, and charges for inter AZ data transfer has a charge $
    on CLB, disabled by default, can be enabled, no charges for inter AZ data transfer

SSL/TLS - SSL certs allows traffic between client and load balancer to be encrypted (in flight encryption)
    TLS is the newer version
    SSL public cert are issued by known CA

    in AWS/ELB - this means that certs are on the ELB
    clients connects to LB > then LB forward the request to target/backend via HTTP since its already private network
    Manage Certs via ACM (AWS Certificate manager)

    SNI - Server name indication - solves loading of multiple SSL certs into one web server (to serve multiple websites)
    its a newer protocol, requires the client to indicate the hostname of the target server in the initial handshake
        only works on ALB and NLB, cloudFront

    SSL certs on 
        CLB - supports only 1 SSL cert
        ALB and NLB - supports multiple listeners with multiple SSL certs

ELB connection draining - delay for connection to target/backend to be terminated (when it becomes unhealthy or to be taken offline)
    time to complete "in-flight requests" 
    naming on CLB - connection draining
    naming on ALB and NLB - deregistration delay
    default timing is 300
    set time depends on max request time completion


ASG - Auto Scaling Group
    Scale out or Scale in
    define a min and max and initial/desired capacity of Ec2 instances
    Rules can be defined by target number of
        CPU usage
        # of requests on the ELB per instance
        average network IN
        average network OUT
        or scheduled
        custom metric: 
            1. Send custom metric from application on EC2 to CloudWatch (PutMetric API)
            2. Create CloudWatch alarm to react to low/high values
            3. Use the CloudWatch alarm as the scaling policy for ASG

    ASG can use Launch configuration or Launch template (newer)
    to update an ASG, provide new launch template
    IAM roles attached to ASG will be assigned to EC2 instances 
    ASG's are free, pay only for the underlying resources

    Dynamic Scaling Policies
        Target tracking scaling
            Most simple/easiest to setup
            Ex. Set target of CPU to stay at around 40%
        Simple/Step Scaling
            when cloudWatch alarm is triggered, (ex. CPU 70%) add 2 units
        Schedule actions
            Anticipate scaling based on schedule
        Predictive scaling
            forecast scaling based on historical graph
    
    Metrics to scale on
        CPU utilization
        RequestCountPerTarget
        Average Network IN/OUT
        any custom metrics you setup in CloudWatch
    
    Scaling cooldown
        after scaling activity, the ASG will be in scaling cooldown - no scaling will happen, to allow the metrics to stabilize
        default to 300seconds
    
    ASG as a Solutions architects
        Default termination policy - how ASG controls the termination of the instances
            Ex. AZ1 has 4 instances and AZ2 has 3 = ASG will first terminate instances in AZ1 to balance, it will also term the older launch config
        Lifecycle Hooks - perform extra activity (ex. run script, etc) before an EC2 instance goes to InService when scaling out / Terminated when scaling in
            So, scale out > instance pending > [lifecycle hook (pending wait > pending proceed)] > InService
            So, scale in > instance terminating > [lifecycle hook (run script to save files)] > terminated
        Launch Template / Launch configuration
            Both - allows to choose AMI, instance type, keypair, SG, user-data, tags
            Launch configuration - (legacy) - must be re-created everytime when updating parameters
            Launch template - 
                can have multiple versions
                create parameters subsets
                provision using both on-demand and spot instances  (or mix)


AWS Fundamentals
    RDS + Aurora + ElastiCache


RDS - Relational Database Service
    AWS fully managed SQL based DB Service
    
    RDS backup 
        one full day backup on maintenance period/time you defined
        transaction logs every 5mins
        7 days retention (can increase to 35days)
    DB Snapshot
        manual backup triggered by user
        rentention of this is as long as you want
    Storage auto scaling
        if enabled, RDS will automatically if the storage needs to be scaled
    RDS read replicas - for read replicas
        can scale up to 5 replicas
        withhin AZ, across AZ and cross region
                    








    






    


