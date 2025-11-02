CCSP : Certified Cloud Security Professional

Multi-tenancy : Function where physical or virtual resources are aollocated 
in a way that partitions guests and their applications and data from one
another 

Resource Pooling : allows a cloud provider's physical or virtual resources to be aggregated to serve one or more cloud service customers


# Cloud Building Block Technologies
* - Virtualization the process of running a simulated instance of a computer system in a layer abstraction simulateously 
    - Virtual Machine manager are called hypervisors: special OS/app runs and orchestrates the virtual machines.

Cloud Service Providers run large clusters to support multi-tenancy
- Type 1: bare metal or native
    - Run directly on the underlying hardware(KVM, Hyper-v)
-Type II - hosted
    - Runs on the OS installed on the hardware (Oracle VirtualBox , VMware)

# Cryptography and Key Management
* - Non-repudiation : Ensure orginal sender cannot deny sending data or engaging in a digital transaction.

# Network and Virtualization Security
VNET - VCN is at the top.

CSP Virtual Router sits at the top

Subnet A : Public Subnet 
Includes : Route Table that includes a route to the Internet Gateway

Subnet B : Private subnet : Route Table traffic is routed through a VPN Gateway (Office Site.)

Zero Trust : trust but verify by granting no implicit trust
    -subjects use attribute-based access controls 

* - VM sprawl : number of VM's overtakes the admins ability to manage resources
 * - VM scape : wher a process running in the guest VM interacts directory with the host OS

 * - Hyperjacking : An attack to take control of the hypervisor often using a rootkit installed on a VM
    - Ephemeral Computing : process of creating a virtual computing env on a ad-hoc basis and then disposing if it when the resources are no longer necessary.
        - Pay for what is used Ex. Use Case - Function as a Service with AWS Lambda or Azure Fucntions


# Cloud Security Hygiene
- CIS Critical Security Controls - ranked set of countermeasures to counter most common cyber attacks against networks.


Cloud Return-on-Investment (ROI): measurement that characterizes the impact that a cloud investment has on the enterprise.
    - Cloud Data portability  to shift data or containers from one cloud service to another without neededing to re-enter the data or re-program
    - Cloud application portability ability to migrate an app from one cloud service to another or between customers environment
    - Cloud interoperabiity is the ability of a consumers system to interact with a cloud service by exaching data based upon an agreed practice to get anticipated results
    - Vendor lock-in where consumer is unable to preserve, migrate, or transfter to another sevrice provider due to tech or non tech contstraints
        - Ex. Common with Software service

# Security Considerations for Cloud Service Types