# SME Feedback: Load Balancing and High Availability
**Module:** Week 6, Class 11 — Load Balancing and High Availability  
**Reviewer:** Sumit Rahman  
**Documents Reviewed:** README.md (student-facing), Instructor Guide

---

## Overall Assessment

This is the clearest conceptual module in Unit 3. The supermarket analogy is well-constructed and maps cleanly to technical components. The lesson correctly distinguishes the LoadBalancer
Service (traffic distribution) from the ReplicaSet (pod replacement), and the `kubectl logs --previous` callout is exactly the right forensic tool to highlight. The mermaid sequence
diagram in Section 3 is the most instructionally effective diagram in the course.

The primary structural issues are the recurring lab placeholder (tenth module) and a meaningful technical gap: the PodDisruptionBudget is not introduced in the lesson despite
being a core HA primitive that students encounter in the lab. Additionally, the lesson's two diagrams in Section 1 show redundant information and could be consolidated.

---

## 1. Structural Issues


**Issue 1.1 — Single Reflection Question**

All previous modules have 2–3 reflection questions. This module has one. The questionis strong (round-robin vs. variable latency) and maps to a Stretch problem in the lab
notebook, but two additional questions would complete the set. Suggested additions:
- *"Why does Kubernetes wait for the readiness probe to pass before routing traffic to a new pod, rather than routing traffic immediately and letting errors self-resolve?"*
- *"How does a PodDisruptionBudget protect availability during voluntary disruptions such as cluster upgrades, and how is it different from a ReplicaSet?"*

**Issue 1.2 — Redundant Diagrams in Section 1**

Section 1 contains two separate Mermaid diagrams showing essentially the same load-balanced cluster topology (one with 3 nodes + failed node, one with 3 active
nodes). The first diagram is richer and includes the failover trigger. The second adds no new information. Recommend removing the second diagram.

---

## 2. Alignment with Syllabus and Competency Map

| Learning Objective | In README? | In Instructor Guide? | Notes |
|---|---|---|---|
| Define load balancing and HA concepts | ✅ Section 1 | ✅ Yes | Supermarket analogy is strong |
| Implement basic load balancing for AI apps | ✅ Section 2 | ✅ Yes | LoadBalancer Service YAML is correct |
| Analyse logs to verify performance under load and failover | ✅ Section 3 | ✅ Yes | `--previous` flag highlight is exactly right |

All three LOs are fully addressed. LO 3 is the strongest section — the sequence diagram correctly shows the load balancer removing the failed pod from the pool
before the replacement is scheduled, which is the key ordering students need to understand.

---

## 3. Technical Content

**Issue 3.1 — PodDisruptionBudget Not Introduced**

The lab's Task 2 asks students to write a PodDisruptionBudget YAML. The term "PodDisruptionBudget" does not appear anywhere in the lesson. Students will
encounter the concept cold in the lab with no lecture framing.

A one-paragraph addition to Section 2 would suffice: explain that a PDB defines the minimum number of pods that must remain available during *voluntary* disruptions
(node drains, cluster upgrades), and that it complements the ReplicaSet's handling of *involuntary* disruptions (hardware failure, OOMKill). The voluntary/involuntary
distinction is the key concept.

**Issue 3.2 — `minikube tunnel` Not Explained**

Section 2 correctly states that `minikube tunnel` simulates an external load balancer IP, but does not explain why it is needed or what happens without it (the Service gets
stuck in `External IP: <pending>`). A single sentence explaining that cloud providers allocate IPs automatically but local Minikube requires a tunnel process to simulate this
would prevent the most common student confusion in the lab.

**Issue 3.3 — LoadBalancer vs. ClusterIP vs. NodePort Not Distinguished**

The lesson introduces `type: LoadBalancer` but does not contrast it with `ClusterIP` (internal-only) or `NodePort` (students used this in Lab 8). A one-row comparison table
would anchor the choice for students who wonder why this lab uses LoadBalancer when they previously used NodePort.

| Type | Reachable from | Use case |
|------|---------------|---------|
| ClusterIP | Inside cluster only | Internal microservice-to-microservice |
| NodePort | Node IP + port | Local dev/testing (Lab 8) |
| LoadBalancer | External IP (cloud or tunnel) | Production external exposure |

---

## 4. Instructor Guide

**Issue 4.1 — "Chaos Engineering" Mentioned Without Definition**

The instructor guide's delivery tips reference the "Chaos Engineering mindset" without defining it. Students who are not already familiar with the term will not know whether
this means something specific (Chaos Monkey, fault injection tools) or is a general attitude. Either define it briefly or remove the term — given this is a foundational
module, the simpler framing ("expect failure, design for it") is more appropriate.

**Issue 4.2 — `--previous` Flag in Troubleshooting Table But Not in Tips**

The troubleshooting table correctly documents `kubectl logs --previous` for the "Cannot find logs after crash" issue. However, the delivery tips section does not
mention it. Given that the lesson's Section 3 and the lab notebook both centre on this flag, it should appear in the teaching tips as a demonstration point:
*"During the live demo, manually delete a pod, wait for the replacement to start, then show both `kubectl logs <new-pod>` and `kubectl logs --previous <new-pod>`
side by side to make the difference concrete."*

---

## 5. Theory-to-Lab Alignment Recommendations

**Recommendation 5.1 — Add PDB Paragraph to Section 2**
One paragraph on voluntary vs. involuntary disruptions and the PDB mechanism would make Task 2 approachable without instructor explanation.

**Recommendation 5.2 — Add `minikube tunnel` Note to Section 2**
One sentence explaining why the external IP is `<pending>` without `minikube tunnel` would pre-empt the most common lab setup issue.

**Recommendation 5.3 — Add Service Type Comparison Table**
A three-row table contrasting ClusterIP, NodePort, and LoadBalancer would place Lab 11's LoadBalancer Service in context relative to Lab 8's NodePort Service.

**Recommendation 5.4 — Connect OOMKill to Recurring Postmortem Thread**
The lesson does not connect OOMKill to resource limits — students who have been through Labs 8, 9, and 10 have now seen the same OOMKill three times. A sentence in Section 1
or 3 noting that OOMKill is a common HA failure mode for AI workloads (due to variable inference memory requirements) would make the recurring data pattern feel deliberate
rather than coincidental.

---

## Summary of Action Items

| Priority | Issue | Type | Owner |
|---|---|---|---|
| High | Provide lab YAML files and notebook — remove placeholder | Fix | Content Author |
| High | Add PodDisruptionBudget introduction to Section 2 | Fix | Content Author |
| High | Add `minikube tunnel` explanation for `<pending>` IP | Fix | Content Author |
| Medium | Add Service type comparison table (ClusterIP/NodePort/LoadBalancer) | Recommendation | Content Author |
| Medium | Add two additional reflection questions | Fix | Content Author |
| Medium | Add `--previous` demo to instructor delivery tips | Fix | Content Author |
| Medium | Define "Chaos Engineering" or replace with plain language | Fix | Content Author |
| Low | Remove redundant second diagram in Section 1 | Fix | Content Author |
| Low | Connect recurring OOMKill pattern to resource limits discussion | Recommendation | Content Author |
