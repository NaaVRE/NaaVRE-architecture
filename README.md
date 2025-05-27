# NaaVRE Architecture

This repository documents the Architecture of NaaVRE.
It describes what is already implemented, and provides guidance for adding new components.

This architecture aims at de-coupling the code base as much as possible, across functionalities, and between backend and frontend.
To that end, we adopt a microservice architecture for the backend.
For the frontend, we develop independent JupyterLab extensions for separate functionalities.

The microservices and JupyterLab are deployed on Kubernetes.
To facilitate integration, we prescribe a model for the communication between components and for authentication.

The architecture is described in the following documents:

1. [Overview](./overview.md)
2. [Integration: CI-pipeline, deployment](./ci-and-deployment.md)
3. [Current implementation](./implementation.md)
4. Guides
   - [Create API service](./guide-api-service.md) (TODO)
   - [Create UI component](./guide-ui-component.md) (TODO)
