# Architecture and Components

There are few principles that we had in mind whilde designing the architecture, namely:

- To support the hybrid model: classical batch-like and interactive/quasi interactive 
- To be compliant with the emerging analysis frameworks based on modern backends/columnar formulation ( coffea / RDF ) without being framework specific. 
- To foreseen a flexible resource integration in order to cope with a model based on a seed of resources for local development and possibility to offload everywhere
- To provide a optimized integration with data 
- To offer a democratized solution to anybody in the collaboration 
- To adopt open source industry standards, to lower the maitanance costs but also to lower the learning curve to any new adopter. 

The current design, as depicted in the schema, has a set of services that are manage centrally while the compute resources can be either locally close to the central services or distrbuted. In any case the user is proved with an environment where to perform interactive analysis or just submit jobs to a pool of resources in a cluster.

In detail, the users can access either through the [JupyterHub portal](https://cms-it-hub.cloud.cnaf.infn.it/) where can have a personal and customizable ( a user can provide his own runtime environment ) 
JupyterLab instance where they can work or via command line as any well know batch system. The latter is implemented with HTCondor which is also responsible to harvest the resources possibly geografically distributed. Moreover, in order to implement the interacitvity, HTcondor is the responsible to allow Dask being distributed where the resources are actually available. [Dask](https://dask.org/) has the role to parallelize the actual user analysis maximizing the usage of the hardware available in every node ( all the cores ). The software distribution ( ROOT and any other library ) is distributed via CVMFS. Finally the data access is mediated by the [XRootD](https://xrootd.slac.stanford.edu/) service. The caching layer is foreseen in roder to optimize the throughput, principally for all the distributed resources. 
At the moment there is no permanent storage available and the user is meant to use the grid storage for premanent/ long term data archiving.

For what concern the interaction with CMS Data Management system ... <span style="color:red;font-size: large;font-weight: bold;">HERE DIEGO MUST PUT all what we can get from ESCAPE.</span>

Finally a key building blocks required for a user in order to be granted access to the system is the authentication ( and authorization ) that is based on Json Web Tokens (JWT) issued by the [CMS instance of INDIGO-IAM at CERN](https://cms-auth.web.cern.ch/login). Additional details for accessing the system can be found [here](tutorials/dask_init.md)

All the central service, as well as the seed of local resources for quick development is manage with Kubernetes as detailed below. 

![Design schema](imgs/overview_schema_extended.png)
## Components

<span style="color:red;font-size: large;font-weight: bold;">dettagliare bene cosa fa ogni singolo pezzo qui sotto DIEGO MIRCO</span>

Summarizing all the resources and pieces needed to construct the described architecture,
we have the following components:

- A **[k8s](https://kubernetes.io/) cluster** where the JupyterHub and JupyterLabs are managed
- The **HTCondor central components**, namely Collector, Negotiator, CCB and Schedd, run on top of K8s and are responsible to manage any workernode and to dispatch Dask jobs ( master and workernode ). 
- A custom derived plugin for **Dask jobqueue**: [repository](https://github.com/comp-dev-cms-ita/dask-remote-jobqueue)
- A custom **Dask labextension** to support the end-users transparently: [repository](https://github.com/comp-dev-cms-ita/dask-labextension)
- A **forwarder service** for the cluster connections
- A **controller service** to interact with che Dask Scheduler inside the cluster
