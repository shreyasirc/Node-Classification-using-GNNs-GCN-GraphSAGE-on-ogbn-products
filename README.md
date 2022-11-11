# Node Classification on OGBN Products Dataset
## Problem Statement 
In this project our aim is to perform node-level prediction for supervised multi-class classification of Amazon products into their categories. 

We are making use of a subset of the ogbn-products dataset and used GCN and GraphSAGE algorithms to perform node classification.

## Dataset Description ([dataset](https://ogb.stanford.edu/docs/nodeprop/))
- The ogbn-products dataset is an undirected and unweighted graph.
- It represents an Amazon product co-purchasing network.
- The nodes in the graph represent the products sold in Amazon.
- The edges between two nodes indicate that the products were purchased together.
- The node features provided in the dataset have been generated by extracting bag-of-words  features from the product descriptions followed by a Principal Component Analysis to reduce the dimension to 100.
- In the provided dataset, the 47 top-level categories are used for target labels.

### Picking a subset of dataset

#nodes : 2,449,029<br>
#edges : 61,859,140

Since the dataset is really huge, we have reduced the number of classes from 47 to 6.

We have considered the following classes:
- 0: Home & Kitchen
- 5: Patio, Lawn & Garden
- 21: Tools & Home Improvement
- 25: Appliances
- 26: Kitchen & Dining
- 37: Home Improvement
<br>
#nodes in the subset : 173157<br>
#edges in the subset : 4913889

### Dataset splitting
We have used StratifiedShuffleSplit for splitting our dataset.

Stratified sampling aims at splitting a data set by ensuring  that relative class frequencies is approximately preserved in the train and validation set.


 The node labels have been renamed as shown:
{0: 0, 5: 1, 21: 2, 25: 3, 26: 4, 37: 5}


- Train set - {0: 60531,   2: 34518,  1: 24330, 3: 1404,    4: 256,   5: 170}
- Test set - {0: 25943,   2: 14794,   1: 10427,  3: 601,     4: 110,   5: 73}

## GCN (Graph Convolution Network) ([Paper](https://arxiv.org/abs/1609.02907v4))
GCN is an algorithm that can extract node representations or dense vector embeddings by using both node attributes and graph topological information (i.e., a node's neighbourhood).

![](https://i.imgur.com/AvDdVuv.png)


### Challenges with GCN
- Computationally Expensive
- Curse of hub / celebrity nodes
- Node embedding for single fixed graph
- Application on real world graphs

Therefore, we need an inductive approach rather than a transductive one

## GraphSAGE (SAmple and aggreGatE) ([Paper](https://arxiv.org/abs/1706.02216))
GraphSage is an inductive variant of GCNs, which means that it does not need to learn the entire graph structure and can generalise well to unseen nodes. By selecting and combining neighbours from various search depths or hops, this subset of graph neural networks learns node representations.

![](https://i.imgur.com/GmDwa0g.png)

## Model Architecture

Following model architecture was used in our implementation:

### GCN
Total number of parameters : 4486

GCN Layers are as follows:
- **gcn1**: GCNConv(in_features=100, out_features=32)
- **gcn2**: GCNConv(in_features=32, out_features=32)
- **linear**: Linear(in_features=32, out_features=6, bias=True)

### GraphSAGE
Total number of parameters : 54534

GraphSAGE layers are as follows:
- **SAGEConv**(in_features=100, out_features=256, aggr=mean)
- **SAGEConv**(in_features=256, out_features=6, aggr=mean)
- For neighbourhood sampling:
    - 15 nodes were picked for layer 1
    - 10 nodes were picked for layer 2
  
## Results

### GCN
![](https://i.imgur.com/GMeKSLN.jpg)


- The model almost converged at around 1000 epochs.
- An accuracy of 91.27% was achieved on train dataset.
- An accuracy of 91.15% was achieved on test dataset.

### GraphSAGE
![](https://i.imgur.com/MjCRDLk.jpg)


- The model almost converged at around 17 epochs.
- An accuracy of 94.88% was achieved on train dataset.
- An accuracy of 94.72% was achieved on test dataset.


### GCN vs GraphSAGE


| GCN                                                    | GraphSAGE                                                                         |
| ------------------------------------------------------ | --------------------------------------------------------------------------------- |
| No. of parameters = 4486                               | No. of parameters = 54534                                                         |
| No. epochs taken to converge ~ 1000                    | No. epochs taken to converge ~ 15 |
| Training accuracy = 91.3% | Training accuracy = 94.9% |
| Test accuracy = 91.1% | Test accuracy = 94.7% |
| Learns representations for only the dominant 3 classes | Learns representations for the dominant 4 classes, hence more expressive than GCN |


![](https://i.imgur.com/qKMrQ25.png)


