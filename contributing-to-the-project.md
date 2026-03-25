## Contributing to NaaVRE
We welcome contributions to NaaVRE! To ensure your new functionality integrates smoothly, we recommend one of the following design patterns:

### Jupyterlab extension
Use this approach if your functionality can operate as a standalone JupyterLab extension (i.e., it does not require deep integration with NaaVRE’s core components).

- Develop your extension. Follow the [JupyterLab extension documentation](https://jupyterlab.readthedocs.io/en/latest/extension/extension_dev.html).
- Once your extension is ready, test it in NaaVRE by installing it in a virtual lab environment using pip.

### Microservice architecture
Use this approach if your functionality requires complex interactions with existing NaaVRE components (e.g., APIs, databases, or backend services).
In this case we recommend testing on a virtual machine.
Request a small virtual machine (VM) from LifeWatch ERIC VLIC. Contact them to explain your needs and request access.