# PCI-in-a-Container-Environment
A high level guide for maintaining PCI DSS in a containers environment.

## Technological Differences That Affect Compliance
Setting up PCI within a container environment presents unique challenges. The following QSA-reviewed solutions can help navigate those challenges to achieve PCI compliance. These solutions aim to address the most common issues. Every scenario is potentially unique and it’s important to consult with your Qualified Security Assessor before implementing any of our recommendations. 
## Fundamental Differences
PCI requirements and guidelines generally focus on legacy infrastructure. Container services do not have specific guidelines that dictate how to build a PCI compliant application within a container environment. These environments have characteristics not found in standard infrastructure, such as dynamic expansion and shrinking, sharing a hosting environment, temporary storage, and short uptime.
