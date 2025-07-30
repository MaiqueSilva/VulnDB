Privilege Escalation via Tampering with Role Value in Project Listing Response

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

CVSS:3.1/AV:N/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H

Description

Summary

A critical privilege escalation vulnerability was identified in the LitmusChaos platform, which allows a low-privileged user to gain elevated privileges (from Viewer to Owner) over a project by tampering with the role value in the response of the /auth/list_projects endpoint. This flaw results in unauthorized actions being performed on resources that should be read-only for the user.

Details

During testing, it was observed that the /auth/list_projects endpoint returns a list of projects that the authenticated user can access, along with the respective roles (Viewer e Owner).

Test Scenario:

The user test-maique has viewer-only access to the admin-project.
As expected, when attempting to perform any modification (e.g., delete an experiment), the platform displays a message indicating insufficient permissions.
Upon inspecting the network traffic, the following request was made:
´GET /auth/list_projects´

This response was intercepted and the role value was manually changed from Viewer to Owner using a proxy (e.g., Burp Suite).
After modifying the response and refreshing the UI or interacting with the system, the user test-maique was granted owner-level privileges, which enabled actions that should be restricted.
To confirm the exploitability, the user successfully deleted an experiment within the admin-project, an action previously blocked by permission controls.
PoC
Test Scenario
The test scenario was configured as follows:

The user "test-maique" has permission to view (Viewer) the project "admin-project".

<img width="1917" height="837" alt="image" src="https://github.com/user-attachments/assets/d5a96366-69e9-42a7-a8de-7b7f24227dd3" />


Any attempt to act on the project by the user "test-maique" results in a "permission denied" message.

<img width="1584" height="700" alt="image" src="https://github.com/user-attachments/assets/93e9b2b5-7239-4286-a146-532a9c97c263" />


Exploitation Details
When analyzing the platform's behavior, it was observed that, when the user requests the list of projects to which he has access, a request is made to the /auth/list_projects endpoint. This request returns a list of all projects accessible to the user, along with their respective permissions. For the "admin-project" project, the user "test-maique" had the role set to "Viewer".

<img width="1903" height="770" alt="image" src="https://github.com/user-attachments/assets/c84ea87c-98f4-493e-aa16-c55b3657ae1e" />


Aware of this information, a request to /auth/list_projects was intercepted and the value of the role field for the "admin-project" was changed from "Viewer" to "Owner".

<img width="1903" height="773" alt="image" src="https://github.com/user-attachments/assets/f313946f-7b1c-4ffd-bd78-60faf6c87deb" />


Vulnerability Result
After sending the modified request, the user "test-maique" obtained permissions to interact with the project "admin-project", as if he were an owner.

<img width="1911" height="835" alt="image" src="https://github.com/user-attachments/assets/75992488-253e-4b2a-bc87-6b9da628d52f" />


To validate the flaw, an experiment within the project was deleted, an action that the user "test-maique" should not have been able to perform with his original permissions. This exploit demonstrates a serious access control flaw, allowing privilege escalation from a regular user to owner.

<img width="1912" height="840" alt="image" src="https://github.com/user-attachments/assets/d6c671cf-ed10-4100-9773-50030b9c3182" />

Impact
This vulnerability allows a malicious user to:

Escalate their privileges without any authorization;
Compromise the integrity and availability of sensitive projects;
Tamper with experiments, configurations, or data belonging to other users.
This undermines the core access control mechanisms of the platform and can lead to complete project takeover.
