Client-Side Validation Bypass Allows Insertion of Special Characters in User Data

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0



### Summary
A validation flaw was discovered in the LitmusChaos platform that allows an attacker to bypass client-side restrictions and submit special characters ´!@#$%¨&)´ in user profile fields, such as the display name. The backend does not enforce the same restrictions, leading to inconsistent input handling and potential downstream issues.



### Details
The application enforces input restrictions (e.g., disallowing special characters in user names) only at the frontend level, using JavaScript or HTML5 validation.

However, these restrictions can be trivially bypassed by manipulating requests directly, allowing unexpected data to be stored and displayed.

Test Scenario:

1. Logged in as the admin user.
2. Attempted to change the display name in the user profile using special characters  ´!@#$%¨&´
3. The frontend displayed a validation error, preventing the submission.
4. The HTTP request for the profile update was intercepted using Burp Suite.

- Endpoint: ´POST /auth/update/details´

5. The name field was manually modified to include the special characters:
6. The request was forwarded to the backend.
7. A successful response was received from the server, and the changes were reflected in the user interface.


### PoC
In the test scenario, the administrator account was used to attempt to change the user name by inserting special characters. Initially, the frontend application restricted this action, indicating primary validation.

![1](https://github.com/user-attachments/assets/81a50b1c-fe05-4d39-a11d-a26295fe8d2a)


However, this validation was easily bypassed when intercepting the request to change user data, specifically for the ´/auth/update/details endpoint´.

![2](https://github.com/user-attachments/assets/9b1570cb-c2d7-4eee-8b06-9e7a017ce20d)


In the intercepted request, the field referring to the user name was modified to include the desired special characters (for testing and proof of concept purposes).

After sending the modified request, the backend returned a success message.

![3](https://github.com/user-attachments/assets/dc41d7c3-b0c2-4d97-bd67-e4ed33ccb289)


And in the application interface, the user name was successfully changed, displaying the special characters.

![4](https://github.com/user-attachments/assets/bcbddc10-62ce-4736-8a4f-333793e5ad14)


This demonstrates that input validation was applied only on the frontend, allowing the manipulation of the request to bypass the restrictions and compromise the integrity of the data.



### Impact
While this issue may seem minor at first glance, the lack of proper input validation on the server side opens the door to:

- Inconsistent application behavior;
- Potential injection vectors (e.g., if this data is later rendered in contexts like HTML, logs, or emails without sanitization);
- Broken data integrity.
