## NaaVRE architecture overview

NaaVRE is built on top of JupyterLab.
This means that most of the frontend consists of JupyterLab extensions, which are backed by backend services outside Jupyter.
When running NaaVRE as a service, JupyterHub dynamically spawns a JupyterLab instances for each user.
JupyterHub is deployed on Kubernetes using [z2jh](https://z2jh.jupyter.org).


### Component types

NaaVRE components are classified in three categories:

- 💻 UI component: frontend, mostly JupyterLab frontend extensions,
- 🧮 API service: microservices, mostly FastAPI or Django,
- 🚀 Third-party services: any external tool, generally not maintained by the NaaVRE team.

![Components type](./diagrams/architecture-components.png)

#### UI components

UI components are the main way through which users interact with NaaVRE.
Most of them are pure frontend JupyterLab extensions, which is the recommended approach because it allows us to provide everything in one place.
UI components may be standalone websites, but only if there is a good reason to do so.
Currently, the only example is the [NaaVRE-PaaS-frontend](https://github.com/NaaVRE/NaaVRE-PaaS-frontend/), because it is the place from which users start JupyterLab instances.

JupyterLab extensions are created using the [JupyterLab extension template](https://github.com/jupyterlab/extension-template).
While this template allows to create _server_ extensions, NaaVRE UI components should not make use of this feature.
Instead, they should use API services where a backend is needed.
Using the template, we build _frontend_ extensions in TypeScript.
We release them as [_prebuilt extensions_](https://jupyterlab.readthedocs.io/en/4.4.x/extension/extension_dev.html#overview-of-extensions): the TS source is built into a JavaScript bundle, embedded into a Python package, and published to PyPI.

JupyterLab extensions communicate with API services through the [NaaVRE communicator](https://github.com/NaaVRE/NaaVRE-communicator-jupyterlab/).
The communicator is the only JupyterLab Server extension in NaaVRE.

Example UI components on JupyterLab:
- [NaaVRE-containerizer-jupyterlab](https://github.com/NaaVRE/NaaVRE-containerizer-jupyterlab)
- [NaaVRE-workflow-jupyterlab](https://github.com/NaaVRE/NaaVRE-workflow-jupyterlab)

#### API services

API services perform the core functionalities from NaaVRE, and constitute the backend for the UI components.
They can be implemented using any API framework.
In practice, we use FastAPI (when no database is needed) or Django REST framework (when a database is needed).

API services should expect to receive authenticated queries from the UI components.
They are responsible for validating the authentication against the identity provider (IdP, Keycloak), as described in the [Authentication](#authentication) section.

API services are released as a Docker image and as a Helm chart to deploy the service on Kubernetes.

Example API services:
- [NaaVRE-catalogue-service](https://github.com/NaaVRE/NaaVRE-catalogue-service)
- [NaaVRE-containerizer-service](https://github.com/NaaVRE/NaaVRE-containerizer-service)
- [NaaVRE-workflow-service](https://github.com/NaaVRE/NaaVRE-workflow-service)

#### Third-party services

Third party services provide additional functionalities to the API services.
Usually, they are not maintained by the NaaVRE team.
They can be self-hosted, or external SaaS.

Example third-party services:
- Argo Workflows
- [k8s-secret-creator](https://github.com/QCDIS/k8s-secret-creator) (the only one managed by us, because it must be deployed close to Argo)
- GitHub services: git repo, actions, container registry, etc.

### Communication model

UI components are part of the user's personal JupyterLab instance, which is spawned by JupyterHub.
UI components communicate with API services through the 🔗 Communication hub, which takes care of authentication.
API services validate the authentication against the central IdP (Keycloak).
Finally, API services may connect to third-party services.
Third-party services may be operated alongside NaaVRE (e.g. Argo Workflows engine), or be external SaaS (e.g. GitHub).

A UI component may communicate with multiple API services.
An API services may be contacted by multiple UI components.
API services may not communicate with one another.

### Authentication

Authentication is managed by a centralized IdP (Keycloak), and uses OpenID connect (OIDC).

**JupyterHub.**
When users connect to JupyterHub, they are prompted to log in through the IdP.
After login, JupyterHub obtains an OIDC access and refresh token in JWT format on behalf of the user.
These tokens are passed to the user's personal JupyterLab instance.

**JupyterLab extensions and communication hub.**
When a UI component (i.e. a JupyterLab extension) connects to an API service through the communication hub, the communication hub performs the following actions:
- makes sure that the domain of the API service is allowed,
- checks whether the access token is expired, and renews it using the refresh token if needed,
- adds the access token to header of the initial query,
- sends off the initial query to the service.

**API services.**
The API service receives the query relayed by the communication hub.
They should expect the query to include an OIDC access token in JWT format in the `Authorization` header, using the `Bearer` scheme.
For instance:

```HTTP
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpYXQiOjE3NDgyNzE5ODMsIm1zZyI6IlRoaXMgaXMgYW4gZXhhbXBsZSBKV1QsIGJ1dCBpdOKAmXMgbm90IGEgdmFsaWQgT0lEQyBhY2Nlc3MgdG9rZW4ifQ.m98GiyxvyQ_rfzlRAO21L-ag0xRLEz0SmhxbAF0_1AM
```

The API service should validate the access token against the IdP (Keycloak).
This is achieved by verifying the JWT's signature with the IdP's public key.
Here is an [example implementation](https://github.com/NaaVRE/NaaVRE-containerizer-service/blame/eacb5eee5262b285e936a32a17ce873d024d9277/app/utils/openid.py) of the token validation.

**Third-party services**
There are no prescriptions for the authentication between API services and third-party services.
In some cases, the authentication scheme is imposed by the 3rd party service itself.
When possible, it is recommended to use OIDC through the IdP (e.g. between the NaaVRE-workflow-service and Argo Workflows).

![Component authentication](./diagrams/architecture-components-communication.png)
