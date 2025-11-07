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

# Training Data Poisioning



