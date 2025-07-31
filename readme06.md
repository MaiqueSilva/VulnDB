Improper Authorization Allows Unauthorized Deletion of Other Users’ Projects

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

### Summary
A vulnerability was identified in the LitmusChaos platform that allows any authenticated user to delete projects belonging to other users by manipulating the project ID in a DELETE request. This occurs due to missing authorization checks on the backend.

### Details
During analysis, it was discovered that when a user deletes a project, the backend only verifies the project ID in the request and does not check whether the requesting user is the rightful owner of that project.

The vulnerability was validated through the following steps:

1. User maique created a project called "project-maique" and initiated its deletion.
2. The deletion request was intercepted using Burp Suite, a web proxy tool that allows traffic inspection and manipulation.
3. It was observed that the DELETE request sent to the backend contained a project ID.
4. Then, the account of a separate user (admin) was used to create another project named "test-delete" and retrieve its project ID.
5. Back in the maique session, the original DELETE request was replayed, but the project ID was replaced with the ID of the admin’s project.
6. The backend responded with a success message, indicating that the project had been deleted.
7. The "project-maique" project remained visible on the frontend, but after checking the admin account, it was confirmed that the admin’s project had been deleted.

This proves that any authenticated user can delete projects they do not own, simply by knowing or guessing the project ID.

### PoC
User maique creates "project-maique" and initiates deletion.

Request intercepted via Burp Suite shows:

`POST /auth/delete_project/<project_id>`

![1](https://github.com/user-attachments/assets/85653f41-2664-4687-903d-4d5903316640)

admin user project

![2](https://github.com/user-attachments/assets/2257c92d-84f2-48dc-9e64-cdf8af37cce2)

The ID is replaced with a valid ID from another user’s project.

![3](https://github.com/user-attachments/assets/ede9bb45-3798-4422-999d-61e6a8a20330)

Response:

`{ "message": "Successfully deleted Project ." }`

![4](https://github.com/user-attachments/assets/f99ff27d-e625-4cae-895a-6d08000fb7e4)

The project still appears on the maique account (indicating it wasn’t his).

![5](https://github.com/user-attachments/assets/3428342b-bd74-4138-8c19-ef508200295c)

On the admin account, the "test-delete" project is now gone.

![6](https://github.com/user-attachments/assets/8bf45691-4673-482c-8951-aa1dddf54da3)


### Impact
This flaw allows any authenticated user to delete projects created by other users, leading to:

- Loss of data and test environments;
- Compromise of project integrity;
- Possible denial of service through abuse.
