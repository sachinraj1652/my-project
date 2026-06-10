# Generic CI AMI — POC (Proof of Concept)
<img width="354" height="354" alt="image" src="https://github.com/user-attachments/assets/674494fa-d1ee-4f7f-9014-eced6fb60508" />

---


## Author Details

| Author | Created | Version | Last Updated By | Last Edited On | L0 Reviewer | L1 Reviewer | L2 Reviewer |
| -------------- | ---------- | ------- | --------------- | -------------- | ----------- | ----------- | ----------- |
| Sachin Rajput | 2026-05-13 | 1.0 | Sachin Rajput | 2026-05-13 | Anitha Annem | Shreya Jaiswal | Ashwani Singh |

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Scope](#2-scope)
3. [Prerequisites](#3-pre-requisites)
4. [POC steps](#4-poc-steps)
5. [Success criteria](#5-success-criteria)
6. [Risks and limitations](#6-risks-and-limitations)
7. [Conclusion](#7-conclusion)
8. [Contact Information](#8-contact-information)
9. [References](#9-references)

---

## 1. Introduction

The **Generic CI AMI POC** validates that a preconfigured Amazon Machine Image (AMI) can be used to run CI jobs in a consistent, repeatable way. The POC answers: Can we build the AMI, connect it to our CI system, and run real build/test jobs with acceptable startup time and reliability? This document defines the POC scope, prerequisites, steps, success criteria, and risks. For the broader concept and design, see [Generic CI AMI](../README.md)

---

## 2. Scope

| In scope | Out of scope |
|----------|--------------|
| One CI system (e.g. Jenkins or GitLab) | Multiple CI systems in one POC |
| One or two sample pipelines (e.g. Java, Node) | All existing pipelines |
| Building one Generic CI AMI and using it for agents/runners | High availability or multi-region |
| Measuring job success and startup time | Full production hardening (e.g. compliance, SSO) |
| Documenting how to update the AMI | Automated AMI refresh pipelines (can be phase 2) |

---

## 3. Pre-requisites

| Requirement | Description |
|--------------|-------------|
| **AWS account** | Access to create AMIs, launch EC2 instances, and use IAM roles for the CI agent. |
| **CI system** | Jenkins, GitLab, or similar with the ability to launch agents/runners on EC2 (e.g. EC2 Fleet plugin, GitLab Runner autoscale). |
| **Base AMI** | A supported base (e.g. Amazon Linux 2, Ubuntu) in the target region. |
| **Sample repos** | At least one repo (e.g. Java or Node) with a simple build and test that can run on the POC AMI. |

---

## 4. POC steps

**Step 1 — Define scope**

- Choose one CI system (e.g. Jenkins).
- Choose one or two representative pipelines (e.g. one Java build, one Node build).
- Agree on the runtimes and tools that must be on the AMI (e.g. Java 17, Node 20, Git, Docker CLI).

**Step 2 — Build the AMI**

- Start an EC2 instance from the chosen base AMI (e.g. Amazon Linux 2).
- Install required runtimes, tools, (e.g. java , nodejs , docker ).
- Harden minimally (e.g. security updates, no unnecessary services).
- Create a new AMI from the instance; tag it (e.g. `generic-ci-ami-poc-v1`).
- Remove unwanted files (`sudo apt clean && history -c`)

<details>
   <summary>TO VIEW AMI : CLICK HERE </summary>
  <img width="1566" height="699" alt="image" src="https://github.com/user-attachments/assets/52934eca-c2fd-462c-a2df-e6f21177c266" />

</details>

**Step 3 — Configure CI to use the AMI**

- In the CI system, configure the cloud/EC2 plugin to launch agents or runners from the new AMI (instance type, subnet, IAM role, security group).
- Ensure the agent can reach the CI controller (network, security groups, credentials or keys).

<details>
   <summary>TO VIEW Configuration  : CLICK HERE </summary>
 <img width="1277" height="900" alt="image" src="https://github.com/user-attachments/assets/e876865f-0902-469b-9ac9-24e98e31fd75" />

</details>

**Step 4 — Run sample jobs**

- Trigger the chosen pipelines (e.g. on a branch or PR).
- Confirm that instances launch from the AMI, register as agents/runners, and that the jobs complete successfully.
- Note job startup time (time from “job scheduled” to “job started”) and any failures or misconfigurations.

---

<details>
   <summary>TO VIEW Build  : CLICK HERE </summary>
 <img width="1919" height="953" alt="Screenshot from 2026-05-13 23-17-57" src="https://github.com/user-attachments/assets/7c09541f-4232-4e97-8c7e-67b96b7604c6" />

</details>

---

<details>
   <summary>TO VIEW agent logs  : CLICK HERE </summary>
<img width="1919" height="953" alt="Screenshot from 2026-05-13 23-16-43" src="https://github.com/user-attachments/assets/2782950f-f742-43a6-9188-1a9330cb3463" />

</details>

---

<details>
   <summary>TO VIEW AWS console : CLICK HERE </summary>
<img width="1919" height="953" alt="Screenshot from 2026-05-13 23-16-10" src="https://github.com/user-attachments/assets/ff8b5dd3-9f7e-4621-b755-1d7ccb8346a8" />

</details>

## 5. Success criteria

The POC is considered successful when:

| Metric              | Target                        |
| ------------------- | ----------------------------- |
| Agent startup time  | < 3 minutes                   |
| Build success rate  | > 95%                         |
| Instance cleanup    | 100% terminated after job     |
| Runtime consistency | Same versions across all runs |


If all of the above are met, the POC can be signed off and the approach can be extended to more pipelines or to production hardening.

---

## 6. Risks and limitations

| Risk / limitation | Mitigation |
|-------------------|------------|
| **AMI drift** | Document and automate AMI build; rebuild on a schedule. |
| **Cold start latency** | Accept for POC or use a small pool of warm instances if needed. |
| **Missing tools** | Start with a minimal set; add tools based on first runs and document them. |
| **Cost** | Use small instance types and ensure instances terminate when idle; monitor cost during POC. |
| **Single region/account** | POC can be single region/account; multi-region or multi-account can be phase 2. |

---

## 7. Conclusion

The Generic CI AMI POC successfully validated the use of a reusable AMI for running CI jobs in a consistent and automated environment. Jenkins dynamically launched EC2-based agents from the custom AMI, executed pipeline jobs, and automatically terminated the agents after job completion. The POC demonstrated improved environment consistency, simplified CI agent management, and efficient resource utilization.

---

## 8. Contact Information


| Name | Email Address |
| ------------- | ------------- |
| Sachin Rajput | [sachin.rajput.snaatak@mygurukulam.co](mailto:sachin.rajput.snaatak@mygurukulam.co) |

---

## 9. References

| Link | Description |
|------|-------------|
| [Generic CI AMI](generic_ci_ami.md) | Main document: what, why, workflow, advantages, best practices. |
| [AWS – Creating an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami.html) | How to create an AMI from an instance. |
| [Packer – Amazon AMI](https://www.packer.io/plugins/builders/amazon/ebs) | Automate AMI build with Packer. |
| [Jenkins – EC2 Fleet plugin](https://plugins.jenkins.io/ec2-fleet/) | Run Jenkins agents on EC2. |

---
