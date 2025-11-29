# Module 1 : Goverance, Risk, and Compliance 
## Implementing Appropriate Governance Components

# Security Program Documentation
- Situational awareness is crucial for maintaining a strong security posture, as it empowers
employees to detect and mitigate threats before they can cuase significant harm

Security Program Management
    - Determine the provider requirements
    - Condstruct the governance scheme
    - Assess cloud vendor
    - Build the architecture
    - Conduct cloud migration

# Goverance Risk and Compliance (GRC) Tools
Governance Risk and Compliance Tools : help orgs manage their goverance, risk management, and compliance processes in an integrated and ststematic manner.
    - GRC mapps tools that align with business as well it frameworks.

# Risk Management Activites 
# Impact Analysis

Industry Standards
- PCI Data Standard (PCI DSS): deals with credit card and payments
- ISO/IEC 2700 family : information security management for INFOSEC
- DMA : UE law for digial platforms.

Security and Reporting Frameworks
- SOC 2 - assurance of security controls
- NIST - cybersecurity Framework

# Threat Actor Characteristics
Attack patterns techniques that describe how adversaaries exploit systems or software vulnerabilities

mitre
cape.mitre
Cyber Key Chain - Lockheed martin 
OWASP Top Ten


# Attack Surface Determination
- Threat-Modeling : asessing the structural design of systems and applications to identify potential security risks

- Trust Boundaries are critical points where the level of trust or control changes between systems, applications, or users
    - Boundaries represent areas where security polices change, making them potential targets for attackers eeking to exploit access or data flow weaknesses

- Code Reviews provides a method for identifying vulnerabilities early in software development lifecycle
    - Inside Code Reviews we look for four common criteria's
        1. Identify Security Vulnerabilities
        2. Enforce Security Standards
        3. Reduce Maintenace Costs
        4. Protect the Application's Attack Surface

User Factors understand how users behave, access, and roles impact organizational security.
    - Factors Include :
        1. Access Mangement
        2. User Behavior Monitoring
        3. Priviledge Controls 
        4. Secure Onboarding and Offboarding

Orgs changes can introduce a wide variety of new threats and vulnerabilites including, Mergers and Acquistions

Threat Modeling Enumeration and Discovery involve identifying internal and external assests, connections, and vulnerabilities that collectively form an org attack surface

# Threat-Modeling Methods
- Attack Trees a model of how malicious actor might seek access to an IT asset from perspective of posssbile successful attacks 


# Modeling Applicability to the Environment
Enterprises can effectively model the applicability of threats to existing systems
    1. Inventory the Current Environment
        - Start by taking stock of all existing assets
    2. Analyze Threats Using Established Frameworks
        - Leverage frameworks, provide a structured approach to identifying common tactics, techniques, and procedures used by attackers
    3. Evaluate and Model THreats Based on Current Architecture
        - Understnad the security posture and identify the most impactful areas for threat
    4. Select Appropriate Controls
        - Based on analysis, select and implement appripriate security controls to address identified vulnerabilites 
    5. Continuous Monitoring and Improvement
        - 
Without an existing system in place 
    1. Define Security Objective and Requirements
    2. Identify Potential Threats
    3. Develop a Conceptual Architecture
    4. Analyze THreats Based on Conceptual architecture
    5. Select Appropriate Controls
    6. Iterate and Refine


Develop a Conceptual Architecture
- Create a high-level conceptual arhcitecture of the proposed system

# Security Challenges with AI
# Legal and Privacy Implications
Protentail Misuse includes 
- Privacy Violations
- Data Misuse
- Automated Manipulation
- Biased Decision-Making
- Over-collection of Personal Data 

Explainable vs Non-Explainable Models
- Transparency and Accountability
    - Exaplainable : Model such as decision trees and linear regression are transparent and allow users to understand how input are processed to generate outputs. Individual have the right to understand decisions that affect them.
    - Non-Explainable modesls : more accurate but opaque in their reasoning. More difficult to regulate.

AI systems must adhere to data protection laws
User Consent and Transparency : policies should enforce practices that ensure users are informed about how their data is collected, stored, and processed by AI models

# Threats to the Models 
Prompt Injection : form of injection attack aimed at langurage models that respond to text-based prompts
    - Attacks includes  adversaries create specific inputs intended to modify the model's respones or trigger unintended actions
    - As a result of Prompt Injection : 
        Confusing or Misleading Prompts
        Embedded Commands or Instructions
        Injection of Sensitive or Unauthroized Data

Insecure Output handing is a weakness in how an AI model predictions or outputs are managed shared, and protected
    - If not handled securely outptus can expose sensitive info, reveal model vul, or facilitake unauthorized access
    - Big risk where models outputs shared with external users or integrated into other systems.
    Includes : Exposure of Raw Models
               Sensitive Information in Output
               Improper Integration with External Systems 

Training Data Poisioning - where adversaries intentionally introduce malicious or altered data into the trainning set
    - Can lead to inaccurate predictions or enabling specific backdoor behaviors.

Model theft is where attackers attempt to copy or recreate proprietary models 
    - Such Attacks include : 
        - Repeated Querying for Model Behavior Replication
        - Creating a Surrogate Model
        - Reverse Engineering via Model Extraction

Model inversion attacks allow adversaries to extract sensitive information froma  model's predictions
    - By analyzing output patterns or propbility distributions, attackers can reverse-engineer sensitive info that the model has learned
    - Such Attacks Inlcude :
        - Querying the Model with Targeted Inputs
        - Analyzing Probability Distributions
        - Reconstructing Sensitive Information

Insecure plug-in design is a significatn concern in AI-enabled attacks
    - Plugins are modular components that extend the functionality of software applications.

Deepfakes are a form of digital media manipulation that leverages machine learning to create highly realistic images, vidoes, and audio that mimic real individusls

AI pipeline injections cyberattack that target the various stages of an AI pipeline, which includes data ingestion, model training, and deployment.
    - Ingest malicious code

Data poisoning is done by introducing manipulated or malicious data during the data ingestion or preprocessing stages

AI systems operate based on pre-trained models and predefined rules

Implementing guardrails for ai-enabled assistants and digital workers
    - Guardrails are policies, guidelines, and technical controls designed to manage AI behaviros, data handing, and user interactions with predefined boundaries.

Key Points for Guardrails
1. Ensuring Ethnical and Compliant AI Behavior
2. Mitigating Security Risk and Protecting Data Intergrity
3. Access Control for Sensitive Data
4. Preventing Unauthorized Actions 


# Module 2 : Security Architecture
# Next-generation Firewall

Ciso Secure Firewall ASA security device protects corporoate networks and data centers of all sizes.

# Network Access Control (NAC)
- Network Access (Admission) Control (NAC) is a security initiative that verifies and grants access to devices trying to connect to a network.
    - enforces polices ased on criteria such as device type, user identity and security rules.

Web Application Firewall (WAF) goes on top the load balancer.

CDN: system of geographically distributed servers that stored cached copies of dyanmic content.

Typical AWS environment

Web Request -> Route 53 or Global Accelerator -> AWS WAF -> CloudFront -> Load Balancers -> AZ1 / AZ2 including EC2 instances in eahc AZ with different subnets.


Scanner, Proxies, Taps, and Collectors
- Proxy : A system or a process that ensures only authorized users can access specific resources.
- Tap : hardware device used to create a copy of network traffic for monitoring and analysis without interrupting the main data flow.
- Collectors : Tools or systems designed to aggregate data from multiple sources, such as logs, network packets, and system events.


# Securing the Devops Lifecycle
# Security Requirements Definition

Static Application Security Test (SAST) - is a method of security testing that analyzes source code, bytecode, or binary code to identify vulnerabilities in software applications
    - SAST is performed early in software development lifecycle
        - Allows developers to detect and fix security issues before the application is deployed.


Dynamic Application Security Testing (DAST) - is a method of security testing that evaluates applications during runtime.
    - DAST interacts eith application in its operational environment to identify vulnerabilities that could be exploited by attackers.

Interactive Application Security Testing (IAST)- method of security testing that combines elements of both SAST and DAST
    - anaylze applications in real-time by monitoring applications behavior and interactions while its running.

Runtime Application Self-Protection (RASP) - realtime protection against attacks
    - Proactive approach helps to neutralize vulnerabilites and prevent exploitation by attackers.

Vulnerability Analysis LifeCycle : Discovery, Analysis, Prioritization, Remediation, Verification

# Continuous Integration/ Continuous Deployment CI/CD
Linting - the process of using tools (linters) to analyze code for potential errors, style issues, and adherence to coding standards

Branch Protection - ensure the integrity of the branch
    - Restricting Direct Pushes : Preventing direct pushes to critical branches main and master. Ensures that all changes go through a review process.
    - Pull/Merge Request Workflow
    - Automated Testing and Re-testing - Integrating automated tests into the CI/CD pipeline ensures that code changes are tested for functionality.
    - Code Quality Checks - Enforcing code quality checks, sucha as liniting and static analysis helps ensure that the code adheres to coding standards and best practices.

Canary Testing
    - Planning the Canary Release : Before deploying the new code, determine the criteria for slecting the initial group of users (the canary group)

    -Deploying the Canary Group: Release the new version of the software to the canary group
            - This can be done using feature flags, load balancers

CI/CD Unit Testing 
- Purpose 
- Automation
- Isolation
- Test Coverage 
- Tools

Hardware Assurance : process to ensure that hardware components are free from know vulneeerabilities throughtout their lifecycle

# Secure Architecture and Control Design
Hardening
Defense in Depth

Managing Legacy Components
    - Isolation
    - Access Control

Hybrid Infrastructure Security Solutions
1. Identify and Access Management
2. Data Encryption
3. Network Security
4. Endpoint Security
5. SIEM Systems
6. Cloud Access Security Brokers
7. Zero Trust Architecture

# Designing Access, Authentication, and Authorization

# Access Control Systems 
Acess Control is control by using STIGS.

# Federation and SSO
Federated access refers to a framework that allows organizations to establish trust relationships with external parties
    - Enables users from one domian to access resources in another domain wihout needing spearate credentials for each service or domain. Works together with SSO
        - Utilizes trusted identity provider that authenticste users allowing them access to resources.

    Trusted Identity Providers (IdPs)in a Single Sign-ON (SSO) are systems that authenticate users and issue security tokens confirming their identity.
        - These tokens are then used by Service Providers (SPs) to grant acces to various applications and services without requireing users to re-enter their credentials.
    Trusted Service Providers (Sps) in single Sign-On are the applications or services that users want to access
        - These SPs rely on Trusted Identity Providers (IdPS) to authenticate users and provide the necessary security tokens

Conditional Access is a security strategy that uses context-based policies to manage access to systems and data
    - Evaluates additional conditions such as the user's device,location, time of access, and device configuration to make more informed access control decisions.
    - Conditional Access Techniques:
        User-to Device Binding
        Geographic Location
        Time-based Access
        Configuration Settings

Attestations refers to the process of verifying and validating the integrity and security posture of device or systme before granting access.

Access Control Models
- Role-Based Access Control assign permissions to roles instead of users.
Rule-Based Access Control : security model where access permissions are determined by predefined rules
    Based on Criteria of Time-Based, Location, Event-Drive, Temporary Access.

Attribute-Based Acces Control (ABAC): complex rule sets sets.
    - Use Case: Enterprise Env's , Healthcare, Educational Institutions, Governemnt Agencies, and Financial Services.

# Cloud Control Strategies
-Taking Proactive Controls such as : Automation, Monitoring and Detection, and Incident Response

- Detective Cloud control stategy focuses on identifying and responding to security incidents and potential threats within a cloud environment


# Severless Cloud Solutions
Container Security
- Ensures the container images are secure is vital
- Includes trusted sources for images, regulary scanning images for vulnerabilities, and maintaining an updated image registry.
- Runtime Security: detect and respond to anomalies, ensuring that containers run with least prvi and implementing security policies that control behavior


# Cloud Access Security Brokers (CASB)
- A cloud access security broker is a security policy enforcement point positioned between enterprise users and cloud service providers.
    - API-based Cloud Access Security Broker integrates directly with cloud service providers using their APIs to provide security and visibility into cloud applications

- Proxy-based Cloud Access Security Broker sits between the user and the cloud application acting as a gateway

# Cloud Connectivity, Integration, and Adoption
* A cloud-based managed Site-to-Site VPN is a fully managed service that creates a secure connection betwewn your data center or branch office and your resources using IP security (IPSec tunnels)

Cloud VPN Gateway services simplify the process of setting up and managing VPN connections

SD-WAN (Software-Defined Wide Area Network) - solutions can be used to securely connect multiple branch offices to cloud providers
    Cloud Peering : peering services that enable direct network connections between your network and the cloud provider's network.


# Zero Trust Concepts
- Zero trust Move defenses from static, network-based perimeters to focus on users, assets, and resources.
Three core principles :
* Verify Explicitly  : Always authenticate and authorize based on all available data points, including user identity, location device health, service or workload, data classification and anomalies

* Use Least Privledge

* Assume Breach : Minimize blast radius for breaches and prevent lateral movement by segmenting access by network, user, devices, and application awareness.

Data perimeters are boundaries around sensitive data to ensure it is only accessible to authorized users and systems.

Security Boundaries
- Segmenting the network into secure zones will limit the movement of potential threats and reduce the attack surface
    - This involves using technologies like firewalls, instrusion detection and prevention systems (IDS/IPS) and network segmentation

# Continuous Authorization and Context-based Reauthentication
- Dynamic context valuation involes continuously assesing the context 
in which access requests are made

# API Ingdbfzgion and Validation
- Includes:
    - Authentication
    - Authorization
    - Input Validation
    - Rate Limiting : to control the number of API requests made within a specific time frame. Helps prevent abuse and mitigating the risk of DoS attacks
    - Logging and Monitoring

* Zero Trust Asset Attestation
- Asset Attestation involves key practices to ensure that all assets, including devices, applications and data are verified and trusted before they are granted accesss to the network.
    - Verify integrity and secure posture before they are allowed to interact with the network.

# Lesson 12: Engineering and Troubleshooting IAM
- Service Access Control - ensuring that servies interacting with other services or users have the appropriate access rights

# DNS Security
Unauthorized Access : 
- A DNS zone transfer is a bulk transfer of DNS zone data from one DNS server to another, used to keep multiple authoritative servers synchronized

- DNS Sinkholing is a technique used to redirect malicious traffic to a controlled IP address, preventing users from accessing harmful domains
    - Often in a honeypot or honeynet
    - Prevents malware from communicating with command-and-control servers
    -  Collects data on malicious traffic and compromised devices
    - Protects the network by preventing users from accessing harmful websites.

- DNSSEC adds security by enabling DNS responses to be validated as genuine.
    * DNSSEC Records
        - RRSIG (Resource Record Signature) 
            - Description : Contains a cryptographic signature
            - Purpose : Verifies the authenticiity of DNS data
        - DNSKEY:
            - Description: Contains a public signing key
            - Purpose: Used to verify RRSIG records
        - DS (Delegation Signer):
            - Description: Contains the has of a DNSKEY record
            - Purpose: Establishes a chain of trust between parent and child zones

NSEC : Next Secure Record : points to the nexr record in the zone
CDNSKEY and CDS :Used for child zones to request updates to DS records in the parent zone.

New solution is OpenDNS

# Email Security
* S/MIME standard for public-key encryption and signing of MIME data
    - provides cryptopgrahic security services for electronic messaging applicaitons.
* Troubelshooting S/MIME :
    - Certificate Issues:
        - Invalid or expired certs, result users may be unable to encrypt or decrypt emails, leading to communication disruptions
    - Compatibility Problems : email client may have different levels of s/mime.

    - DKIM responsible for transmitting a message by signing it with a cryptographic signature 

    - DMARC email protocol that helps protect email domains from unauthorized use, such as phishing and email spoofing

# TLS and PKI
## Main TLS Configuration Errors
- Incorrect Certificate Configuration
- Cipher Suite Mismatch
- TLS Version Mismatch
- Misconfigurred Intermediate Certificates

HSM : physical computing device that safeguards and manages digital key for strong authentication and cryptographic procesing

Secure Enclaves : hardware secure enclave is a dedicated area within a processor that provides a secure environment for executing sensitive code and storing sensitive data
    - enclave is isolated from the rest of the system.

# Host-based Encryption and Self-encrypting Drive
- Host-based Encryption
    - Type of encryption ensures that data is protected at rest meaning it is encrypted when stored on the device's hard drive or other stored media. Uses software-based encryption

    Example is BitLocker and FileValut
* Self-encrypting Drivers (SED) - storage devices that automatically encrypt data s it is written to the drive and decrypt it when it is read
    - Uses hardware-based encryption

#  Advanced Cryptographic Concepts
# Post-quantum Cryptography
    - PQC : secure against attacks by quantum computers
    - Key stretching is a cryptographic technique that makes a weak key, like a password, stronger by applying an algorithm multiple times
    - Key splitting is the process of dividing a cryptographic key into multiple parts, called "shares," which are then stored or distributed separately
# Homomorphic and Envelope Encryption
- Homorphic encryption technique that allows computations to be performed on encrypted data without needing to decrpt it first.

- Envelope encryption combining both symmetric and asymmetric encvryption techniques

* Forward secrecy means that even if an attacker gains access to proivate key, they cannot decrypt past session keys and past communications.
    - Example of the apps that use it , Whatsapp, Signal.

AEAD (Authenticated Encryption with Associated Data)
    - technique that ensures both the confidentiality and intergrity of data

Secure Multipary Computation (SMC) - goal to enable multiple parties to jointly compute a function over their inputs while keeping those inputs private.
    - Used in Auctions, Voiting

# Appropriate Cryptographic Use Cases and Techniques
Data Masking : Replacing sensitive data with fictious but realistic data
Pseufonymization : Replacing private identiffiers with fake identifiers 

Software provenance refers to the veriable information regardinng the origin and history of software component

# Module 4: Security Operations
Aggregate data analysis involes combining and analyzing data from multiple sources to identify patterns, trendss, and correlations
 
Process of Incorporating Third-part Reports and Logs
- Data Collection
- Integration
- Normalization
- Correlation
- Analysis
- Alerting
- Reporting

Threat intelligence feeds are continous streams of data that provide real-time information about potentional cyber threats
    - Including Indicator of Compromise (IoCS), tactis, techniques, and procedures (TTPS) used by threat actors

IoCS - pieces of digital forensics that suggest a system or network might have been breached
    Ex. malicious IP, domains, URLS and file hashes

TTPS - describe bahviour and methods used by threat actors to carry out attacks
    - TTPS help or anticipate and defend against potential threats

Contextual Information : Provides data Context.
    Common Threat Intelligence Feeds
        - AlienVault Open Threat Exchange
        - Spamhasus
        - URLhasus
        - SANS Internet Storem Center

Vulnerability Alerting Activities
- Scanning 
- Detection
- Alert Generation
- Prioritization
- Notification
- Reporting
- Remediation Guidance
- Continous Monitoring

# Lession 21: Analyzing Vulnerabilities and Attacks

## Injection Attacks
Attacker will use a tool like Burp Suite to Discover the vulnerability.

# Request Forgery
Cross-Site Request Forgery (CSRF) : cyberattack where an attacker tricks a user's browser into making an unwanted action on a website where the user is currently authenticated

# Unsafe Memory Utilization
- Use Safe Memory Functions
- Input Validation : Validate all input data to ensure it meets expected formats and sizes
    - Helps prevent buffer overflows and other memory-related vulnerabilities

# Race Conditions
- Type of vulnerability that occurs when multiple threads or processes access shared resources concurrently, and the sequence or timing of their execution leads to unexpected or incorrect behavior
    ## Key Characterstics of Race Conditions
    - Concurrent Access : Multiple threads or processes simulataneously access shared resources such as varaibles, files, or memory.
    - Timing Dependence: The order and timing of operation affect the behavior and outcome of the program
    - Non-Deterministic Behavior : The program's behavior varies depending on the timing and sequence of events, making it unpredictable and potentially exploitable
    - TOCTOU (Time-of-Check to Time-of-Use )
