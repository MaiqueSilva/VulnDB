Broken Access Control via Response Manipulation Grants Unauthorized Access to Other Users’ Projects

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

### Summary
A broken access control vulnerability was identified in the LitmusChaos platform, where a user can gain unauthorized access to another user's project by tampering with the project ID in the response of the login request. This occurs due to improper validation and trust in client-side data after authentication.

### Details
During authentication, when a user logs in through the /auth/login endpoint, the response includes:

- Accessible project IDs;
- The user's role within project.

This information is stored client-side and determines which projects are visible and accessible to the user on the frontend.

The vulnerability was confirmed through the following steps:

1. The user maique has access only to "maique-project" and "admin-project".
2. The user admin has exclusive access to a separate project named "teste Broken Access Control".
3. When maique logs in, the response to the /auth/login request contains his accessible projects and permissions.
4. This response was intercepted and modified in-transit (via a proxy tool like Burp Suite).
5. The ID of one of maique's projects was replaced with the project ID of the admin-only project.
6. After submitting the manipulated response, the frontend accepted the data, and the project "teste Broken Access Control" became visible and accessible to maique.

This shows that the platform improperly trusts the project list returned in the login response and does not validate ownership or permission server-side in subsequent requests.

### PoC
To perform this test, the following sanctions scenario was established:

- The user "maique" has restricted access to the projects "maique-project" and "admin-project".
- The user "admin" has exclusive access to the project "test Broken Access Control".
![1](https://github.com/user-attachments/assets/8973e959-2c90-4015-a0a1-27bb0c85bd99)

During the authentication process of the user "maique", the response of the request made to the /auth/login endpoint revealed sensitive information: the ID of the project to which the user "maique" belonged and its permission level.

![2](https://github.com/user-attachments/assets/040b98d3-30fc-454d-95bd-591a9274d061)

Based on this observation, it was possible to intercept and modify the response to the authentication request. Specifically, the project ID in the response was changed to match the ID of the project "Broken Access Control test", which should only be accessible to the "admin" user.

![3](https://github.com/user-attachments/assets/bbddebb5-3886-400e-bb7b-8152ffbcdb1d)

After manipulating the request, the user "maique" gained unauthorized access to the "test Broken Access Control" project, originally exclusive to the user "admin".

![4](https://github.com/user-attachments/assets/a2280e40-4065-4b2f-be42-adbac177ebb2)

This characterizes a Broken Access Control failure, where a user with lower privileges was able to escalate their permissions and access unauthorized resources.

![5](https://github.com/user-attachments/assets/95cff63a-4c68-4344-a089-ab0041cadd28)



### Impact
This flaw enables an attacker with access to their own account to escalate privileges by accessing other users' private projects, resulting in:

- Unauthorized access to sensitive data;
- Potential modification or deletion of projects not owned by the attacker;
- Total breach of access control across tenant boundaries.
