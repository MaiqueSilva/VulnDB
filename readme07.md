Privilege Escalation via localStorage Manipulation Allows Viewer to Become Project Owner

Plataform: Litmus Chaos

Repository: https://github.com/litmuschaos/litmus

Github version: https://github.com/litmuschaos/litmus/releases/tag/3.19.0

Version: 3.19.0

### Summary
A privilege escalation vulnerability was identified in the LitmusChaos platform. A user with Viewer permissions can elevate their privileges to Owner by manipulating a localStorage key in the browser, without any backend validation.

### Details
During analysis of the LitmusChaos platform, it was found that user permission levels are stored on the client side using localStorage. This data is easily accessible and modifiable via browser developer tools.

The key projectRole determines the user's access level within a project. By changing the value from "Viewer" to "Owner" and reloading the page, the user interface updates accordingly and grants access to restricted features—such as creating new experiments—without any server-side validation.

### Key Observations:
Permission checks appear to occur only on the frontend.

The backend accepts and executes privileged actions without validating the user's actual role.

This violates the security principle of never trusting client-side input.




### Proof of Concept (PoC)
![1](https://github.com/user-attachments/assets/a17f20e2-3f2b-479c-a77a-86c7f5ec410d)

Initial analysis showed that the user "maique" had viewer permission on the project,

![2](https://github.com/user-attachments/assets/92e8b5fe-92fc-424a-a675-169ac7c9d1f5)

As noted, the user is restricting the creation of new "experiments" because he does not have permission.

![3](https://github.com/user-attachments/assets/bdad7bd3-70bc-4082-8863-577ac0ac5fe3)

However, when inspecting localStorage, it was identified that the projectRole parameter was set to the value "Viewer".

![4](https://github.com/user-attachments/assets/746e2b14-5558-4744-8eea-e053304a08f6)

Changing this parameter to "Owner" and subsequently refreshing the page enabled the "New Experiment" button.

![5](https://github.com/user-attachments/assets/aac9752f-1c6d-4640-aa65-4fecb61ccb32)

With this modification, it was possible to create a new environment.

![8](https://github.com/user-attachments/assets/83a648f9-6222-4392-9554-f4fa1fc94ae8)

Consequently, successfully creating a "new experiment" using the user "maique", who originally only had viewing permissions.

### Impact
This vulnerability allows local privilege escalation by any authenticated Viewer user. The attacker can:

Create and modify experiments;

Interact with potentially sensitive environments;

Tamper with chaos workflows, affecting cluster integrity.
