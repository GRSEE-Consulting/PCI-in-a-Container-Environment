# PCI-in-a-Container-Environment
A high level guide for maintaining PCI DSS in a containers environment.

## Technological Differences That Affect Compliance
Setting up PCI within a container environment presents unique challenges. The following QSA-reviewed solutions can help navigate those challenges to achieve PCI compliance. These solutions aim to address the most common issues. Every scenario is potentially unique and it’s important to consult with your Qualified Security Assessor before implementing any of our recommendations. 
## Fundamental Differences
PCI requirements and guidelines generally focus on legacy infrastructure. Container services do not have specific guidelines that dictate how to build a PCI compliant application within a container environment. These environments have characteristics not found in standard infrastructure, such as dynamic expansion and shrinking, sharing a hosting environment, temporary storage, and short uptime.
#### The infrastructure requirements for compliance include but are not limited to:
- Build and Maintain a Secure Network
- Protect Card Holder Data
- Maintain a Vulnerability Management Program
- Implement Strong Access Control Measures
- Regularly Monitor and Test Network
- Maintain an Information Security Policy
## Key Discussion Topics
#### Container Segmentation
Orchestrated container environments are more dynamic, so standard auditing for IP-based rules is not enough.
#### Dynamic Hosting
Environments where all pods can be initiated on all nodes might include other machines into the PCI scope.
#### Container Scanning
External resources require security testing. Otherwise, frameworks and services might hold known vulnerabilities within the environment.
#### Log Collection
PCI requires you to maintain a full audit trail of user interactions with the service, even after the container is gone.
## Container Segmentation
Containers can create a false sense of segmentation because they run in both virtual environments and networks. While segmented in the virtual environment, they are not necessarily segmented on the network layer. Container orchestration tends to work in a similar format to NAT where port assignment is predetermined, which can undermine segmentation. Base internal communication is allowed between pods under the same host. Any service on the hosting server is allowed communication with any pods on that host.

Orchestrated environments offer great flexibility, but PCI segmentation rules create strict limitations on what communication is allowed into the environment. This false segmentation provides attackers with access to the entire network interface once they’ve accessed a pod on the network or a service on the hosting machine. The general rule of thumb is to block everything that has no business justification.
## Micro-Segmentation
One way to circumnavigate the container segmentation issue is to use micro-segmentation and assign pods to nodes. Assigning pods to nodes can be done in nodeSelector, which is a field in the PodSpec that specifies a map of key-value pairs. For the pod to be eligible to run on a node, the node must have each of the indicated key-value pairs as labels. This limits where the pods can run. You can then designate certain nodes to manage PCI data without fear of introducing pods that are irrelevant to the environment.
## Dynamic Hosting
The default setting for pods is to allow any other pod on the same node to communicate with each other, aggregating more machines into the PCI scope. These settings break segmentation between environments and external pods can put other more secure pods at risk.
## Isolation Via Label Selectors
Limiting all PCI pods to the same node and preventing other pods from being loaded in that node can eliminate the dynamic hosting issue. This can be done in several ways, but most of them rely on label selectors.
#### nodeSelector
NodeSelector is a field of PodSpec. It specifies a map of key-value pairs. For a pod to be eligible to run on a node, the node must have each of the indicated key-value pairs as labels. It can have additional labels as well. The most common usage is one key-value pair.
#### nodeRestriction
This is a simple way to constrain pods to nodes with particular labels. NodeRestriction is an admission plugin that prevents kubelets from setting or modifying labels with a node-restriction.kubernetes.io/prefix. The affinity/anti-affinity feature greatly expands the types of constraints you can express.
#### nodeName
NodeName is the simplest form of node selection constraint but is not typically used because of its limitations. When this field of PodSpec is non-empty, the scheduler ignores the pod, and the kubelet running on the named node tries to run the pod. If nodeName is provided in the PodSpec, it takes precedence over the above methods for node selection.
#### Taint
Node affinity is a property of pods that attracts them to a set of nodes, either as a preference or a hard requirement. Taints are the opposite. They allow a node to repel a set of pods. Tolerations are applied to pods and allow but do not require the pods to schedule onto nodes with matching taints. Taints and tolerations work together to ensure pods are not scheduled onto inappropriate nodes. When one or more taints are applied to a node, it indicates that the node should not accept any pods that do not tolerate the taints. 
## Container scanning
Embedding foreign code or services into your service can expose your product to attacks of great impact such as injection of malicious code. This is one reason code review and automated security testing are mandatory on most security standards. The product owner is responsible for the published product on all aspects, including third-party libraries. PCI requires automated application vulnerability security assessment tools or methods like image scanning solutions and static code analysis.

Running code review is useless if the reviewer is not trained in secure coding practices. Therefore, it is required that developers are trained in secure coding practices based on industry best practices, such as OWASP TOP 10, so they can identify vulnerabilities in foreign code embedded into a product. Platforms like Secure Code Warrior provide user-friendly interfaces and gamification of the training to keep developers engaged.
## Static Code Analysis and Other Scanning Solutions
- https://github.com/quay/clair#clair
- https://github.com/anchore/anchore-engine#anchore-engine-
## Additional Image Security Tools
- https://github.com/docker/docker-bench-security#docker-bench-for-security
- https://github.com/cilium/cilium
- https://github.com/eliasgranderubio/dagda#dagda
## Log Collection
Local log collection, on the pod, is deleted once the pod is destroyed. Collecting logs for longer periods is therefore problematic due to the short uptime behavior.. PCI requires you to store full audit trails of user and service activity for at least one year because it can take that long for an attack to be identified. In such a case, you would need those logs to trace attacker activity. PCI also requires monitoring or identifying attacks in real time, so an external, long-term service is essential.
## Designated Log Storage
Some popular container services that are designated for storage and parsing of large quantities of date include:
- Kibana
- Greylog
- Splunk
- ElasticSearch
- Falco
While dedicated SIEM/log collection services like Greylog or Kibana are harder to set up than the general data aggregation services like Splunk or Elasticsearch, parsing log data is usually easier on the SIEM services.
## Hooks
Orchestration platforms offer hooks into the container lifecycle. Using hooks, such as the preStop hook in k8s, at the end of the container lifecycle allows a container to export its generated logs into safe, non-volatile storage. Before utilizing this solution, consider certain edge cases like orchestration platform crashes that would prevent the hooks from being executed.
## Your Thoughts and Solutions
We welcome your feedback, opinions, and ideas. Let us know if you disagree or have used better solutions and architectures for your PCI in a container environment.
