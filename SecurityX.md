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



    




