Insecure Direct Object Reference (IDOR) via Project ID in URL Grants Unauthorized Access to Other Users’ Project Information

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

### Summary
A critical IDOR vulnerability was identified in the LitmusChaos platform, allowing a low-privileged user to access sensitive information from projects belonging to other users by manipulating the projectID in the URL. This access control flaw enables unauthorized disclosure of internal project data across user boundaries.

### Details
The platform uses the projectID as a parameter in the URL to determine which project data to load. However, it fails to validate whether the requesting user has permission to access the project identified by the provided ID.

Test scenario:

1. The user test-maique has access to only one project: "test-maique-project", which contains only one member (himself).
2. The admin user has access to a different project ("admin-project") that contains two members.
3. While navigating through the platform, it was observed that the project ID is embedded in the URL
4. While logged in as test-maique, the user replaced the project ID in the URL with the ID of the "admin-project".
5. After refreshing the page, the platform successfully loaded sensitive information about the "admin-project", including member details, without any server-side access check.

### PoC
To demonstrate the flaw, the following scenario was established:

The user "test-maique" has access to a project with only one member.

![1](https://github.com/user-attachments/assets/42e79ded-b688-4c2e-9ffa-9819472a66ee)



The user "admin" has access to a project with two members.

![2](https://github.com/user-attachments/assets/62ce9be4-ed75-4786-bbe0-4c8588fe9c98)



With the user "test-maique" logged in, the project ID displayed in the URL was manually replaced with the project ID belonging to the user "admin".

After modifying the ID in the URL and reloading the page, the user "test-maique" was able to view the project information of the user "admin", confirming the data exposure and the access control flaw. This vulnerability allows an unauthorized user to access and view information that should be restricted to other users.

![3](https://github.com/user-attachments/assets/900510e8-9761-439b-9a24-ea5d2759d9b1)


### Impact
This vulnerability allows any authenticated user to access confidential project metadata of other users, leading to:

1. Unauthorized data exposure;
2. Enumeration of internal user roles, emails, or team structures;
3. Reconnaissance for further privilege escalation or social engineering attacks.
