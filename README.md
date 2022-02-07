# Superimposition Diffusion Model Package

This package is developed to provide users the ability to run diffusion(aka, social contagion, cascading behaviour, spreading phenomena ) simulations on graph structures.

Users can use normal graphs structures, higher-order structures (such as, groups, hypergraphs, simplicial complexes) or a combination of both.

The compartmental models supported by this software package are:

* SIS
* SIR
* SEIS
* SEIR
* Independent Cascade Model

***

## Process of running a single diffusion simulation

First import the compartmental model you want to use for your simulation. Then, import ModelConfig for setting the model parameters. 



## Model Configuration

In order to configure the model, you should import and create a ModelConfig object. ModelCofig object has a single function called "add_model_parameter" which takes two arguments. The first argument is the name of the configuration parameter and the second argument is the value. Here are a list of all the configuration parameters.

* beta1 : probability of desease propagation via edges( 0 to 1)
* initial_infected : probability of each node becoming infected at the start of simulation ( 0 to 1)
* recovery_prob : probability of recovery after getting infected (0 to 1)
* beta2 : control parameter for higher-order propagation where higher-order propagation is one of "triangle" , "Clique" , "threshold clique".
* alpha : probability of and exposed node to become infected in SEIS and SEIR model.

Other parameters regarding the higher-order structure of the network is as follows:

* number_of_groups : number of groups in the random intersection model
* intersection_probability : probability of a node to participate in a group in binomial intersection graph
* intersection_size : uniform size of each group in the uniform intersection graph
* HO_node_lists : node group list for manually setting up the node group structure (list of lists e.g. [ [1,2,3,4] , [1,2,6,7,8,9] ])
* HO_propagation_mechanims : higher- order propagation mechanism (one of "ThresholdClique" , "Clique" , "Triangle" , "Customized")
* Threshold : the threshold value for ThresholdClique propagation mechanism

If you choose customized as your propagation mechanism, you should specify these parameters:
* "HO_probability": a function for calculating the infection probability of participating in a group. this function should have two input parameters. first one represents the size of the group and the second one represents the number of infected nodes in the group 
* "HO_condition" : a function for specifying a condition when an infection takes place. this function should have two input parameters. first one represents the size of the group and the second one represents the number of infected nodes in the group 




```python
import networkx as nx
from SuperimpositionModel.Models import SIS
from SuperimpositionModel.Models import ModelConfig

base_graph = nx.barabasi_albert_graph(1000, 2)

beta2 = 0.3
threshold = 0.5

HO_prop_condition = lambda group_size , num_infected :num_infected / group_size >= threshold
HO_prop_probability = lambda group_size , num_infected : beta2

cfg = ModelConfig()
cfg.add_model_parameter("beta1" , 0.3)
cfg.add_model_parameter("initial_infected" , 0.1)
cfg.add_model_parameter("recovery_prob" , 0.9)
cfg.add_model_parameter("number_of_groups" , 100)
cfg.add_model_parameter("intersection_probability" , 0.2)
cfg.add_model_parameter("HO_prapagation_mechanism", "Customized")
cfg.add_model_parameter("HO_probability" , HO_prop_probability)
cfg.add_model_parameter("HO_condition" , HO_prop_condition)

model = SIS(base_graph)
model.set_initial_status(cfg)
result = model.run(100)
model.draw(result)
```