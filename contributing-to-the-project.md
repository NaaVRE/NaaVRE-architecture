## Contributing to NaaVRE
We welcome contributions to NaaVRE! To ensure your new functionality integrates smoothly, we recommend one of the following design patterns:

### JupyterLab extension
JupyterLab extensions can customize, enhance or add new functionality to any part of JupyterLab. 
Examples of extensions can be file viewers and editors, or notebooks features new items to the menu, keyboard shortcuts etc. 
You can find a list of examples of JupyterLab extensions in the [extension-examples](https://github.com/jupyterlab/extension-examples).

Use this approach if your functionality can operate as a standalone JupyterLab extension (i.e., it does not require deep integration with NaaVRE’s core components).

- Develop your extension. Follow the [JupyterLab extension documentation](https://jupyterlab.readthedocs.io/en/latest/extension/extension_dev.html).
- Once your extension is ready, test it in NaaVRE by installing it in a virtual lab environment using pip.

### Microservice architecture
Microservice architecture is a software design approach that structures an 
application as a collection of small, independent services that components that 
run each application process as a service. These services communicate via a 
well-defined interface using lightweight APIs.

Use this approach if your functionality requires complex interactions with existing 
NaaVRE components (e.g., APIs, databases, or backend services). 

In the context of NaaVRE examples of microservices could be a metadata 
annotation service, a dataset catalog service or a data visualization service.

Each of these services may need access to the NaaVRE cataloged service to query 
asset information or to the NaaVRE workflow service to trigger a workflow 
execution information.

In this case we recommend testing on a virtual machine.
Request a small virtual machine (VM) from LifeWatch ERIC VLIC. Contact them to 
explain your needs and request access.