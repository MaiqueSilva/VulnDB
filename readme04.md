Authorization Bypass via LocalStorage Project ID Manipulation Grants Unauthorized Project Ownership

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

### Summary
An authorization bypass vulnerability was discovered in the LitmusChaos platform where an authenticated user can gain full access to another user’s project—with owner-level permissions—by manipulating the projectID stored in the browser’s LocalStorage. This vulnerability stems from the lack of backend verification of project ownership and authorization.

### Details
The issue occurs because the application stores critical access context—specifically the projectID—in the browser’s LocalStorage, which is entirely under the control of the client.

Steps to reproduce the vulnerability:

1. A new user account named test-maique was created.
2. This user had access to only one project: "test-maique-project".
3. By inspecting the browser’s LocalStorage, it was observed that the projectID used to define the currently active project was stored locally.
4. The projectID was then manually replaced with the ID of a different project ("admin-project") that belongs to another user (admin).
5. After refreshing the browser, the application accepted the new value from LocalStorage without revalidating access.
6. As a result, the user test-maique gained full owner-level access to the "admin-project".

### PoC
For this test, a user named "test-maique" was created with restricted access to the project "test-maique-project". During the application inspection, it was observed that the ID of this project was stored directly in the browser's Local Storage.

![1](https://github.com/user-attachments/assets/67fd3fba-04cb-4b2a-bbbc-dd621bbe68a1)

Based on this discovery, the exploit consisted of manually modifying the value of the project ID in Local Storage. The project ID "test-maique-project" was replaced with the project ID "admin-project", which belongs to the user "admin" and, by inference, has higher privileges.

![2](https://github.com/user-attachments/assets/7d84236d-e302-4e09-8c5b-f72fe1ec70b1)

After manipulating Local Storage and subsequently reloading the page, the user "test-maique" gained unauthorized access to the "admin-project", including owner permissions.

![3](https://github.com/user-attachments/assets/f055d267-13d1-4ec8-9741-66d64b62da72)

This vulnerability allowed a low-privilege user to escalate their permissions and access resources that should be exclusive to an administrator, demonstrating poor access control.

![4](https://github.com/user-attachments/assets/ca785a4f-0daa-4fd2-aec5-6dd42df5138b)



### Impact

This vulnerability allows any authenticated user to assume full control of another user's project, including:

- Unauthorized access to sensitive workflows and experiments;
- Full permissions to modify, delete, or export project data;
- Bypassing isolation between tenants/users.
