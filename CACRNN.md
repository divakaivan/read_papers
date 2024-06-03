
![[CACRNN.pdf]]
# Introduction

This paper proposes a context-aware attention-based convolutional recurrent neural network (CACRNN) for predicting fine-grained taxi demand in an urban area. 
Key features include urban area partitioning: using morphology-based map segmentation, the urban area is divided into fine-grained regions; and for  region, three taxi demand predictions are made, considering various spatio-temporal dependencies and external factor. The proposed model combines local convolutional layers and GRUs to capture spatial and temporal data characteristics. It also uses RNNs to identify short- and long-term periodic taxi demand patterns.
# Problem formulation
### Definition 1: road network
A road network of an urban area is composed of a set of road segments. Each road segment is associated with two terminal points (i.e. intersections of crossroads), and connects with other road segments by sharing the same terminals. All road segments compose the road network in the format of a graph.
Time is split into equal interval time slots, and the whole urban area is divided into disjoint regions based on its road network. Each region is an irregular polygon, encompassed by several road segments. 
### Definition 2: taxi demands
$X_{n, r}$ represents the number of passengers with the demand of taking a taxi in region $r_n \in R$ at time slot $t_r$. Then, the taxi demands at time slot $t_r$ are defined as $X_t = [X_{1,r}, X_{2,r},...,X_{N, r}]$ 
### Definition 3: taxi tripping records 
{tr} is denoted as a set of historical taxi trip records. Each record contains locations and timestamps of picking up and dropping of passengers. 

To predict taxi demands in future, first, historical demand is gathered. Given a dataset of taxi trip records in an urban area, historical taxi demands in region $r_n$ at a time slot $t_r$ can be approximated by the number of passengers that have taken a taxi

$X_{n_r} = |\{tr | tr.pl \in r_n \& tr.pt \in t_r\}|$, where $tr.pl \in r_n$ and $tr.pt \in t_r$ mean that the pick-up location of record $tr$ is in region $r_n$, and the pick-up timestamp is within the time slot $t_r$, respectively.$|.|$ refers to cardinality of a set. 
### Definition 4: Point of interest (POI)
A POI is a venue in an urban area like a shopping mall. Each POI $pi$ is associated with a location p$i.l$ and a category $pi.c \in C$, where C denotes the set of categories. 
### Definition 5: Taxi demand prediction problem
Consider an urban area divided into disjoint regions R by the road network. Given fine-grained taxi demands in the past time slots $\{X_t | t = 1,2,...,r\}$ extracted from historical taxi demands at the next time slot. The prediction is denoted as $\hat{X_{t+1}} = [\hat{X}_{1, r+1},...,\hat{X}_{N, r+1}]$ 
# Data

### Road network data
![[Pasted image 20240603142537.png]]
### Taxi trip data
![[Pasted image 20240603142841.png]]
![[Pasted image 20240603142957.png]]
![[Pasted image 20240603143008.png]]

### POI data
![[Pasted image 20240603143045.png]]
![[Pasted image 20240603143106.png]]
### Meteorological and holiday data
![[Pasted image 20240603143225.png]]
# Methodology

![[Pasted image 20240603143301.png]]
### Instal spatial-temporal module
This module is composed of a series of local convolutional (LC) layers and a GRU, which extract spatial and temporal dependencies of taxi demands in a close period. It takes the taxi demands in o successive time slots as its input, denoted by $Y_i = [X_{r+1-o}, X_{r+2-o},...,X_r] \in R^{oxN}$ and outputs a prediction of taxi demands in the next time slot $f^i \in R^{1xN}$.

Neighbouring and function-similar regions are considered as correlating regions.
![[Pasted image 20240603144958.png]]

##### Definition 6: Functional similarity
Two regions evaluated whether they are similar based on cosine-similarity of their embedding vector (containing significance of POIs in a specific category c, measured by TF-IDF).
### Short-Term periodic module
This module considers the existence of short-term (e.g. a few days) periodicity in taxi demands to perform the prediction. GRU is employed to learn the short-term periodicity, which takes a sequence of taxi demands in $p$ periodic time slots with interval $\Delta_s$ as the input, i.e. $Y^s = [X_{r+1-p\Delta_s}, X_{r+1-(p-1)\Delta_s},...,X_{r+1-\Delta_s}] \in R^{pxN}$. The influence of external factors on periodicity is also considered here. 
### Long-Term periodic module
This module draws the long-term (e.g. a few weeks) periodic pattern of taxi demands. 
### Context-Aware attention module 
The output of the above three modules is combined using attention in order to obtain the final taxi demand prediction $\hat{X}_{r+1}$. This module can be interpreted as assigning different weights to the predictions of each region, according to contextual information like weather condition, index of time slots, and region function. 

# Experiments

### Baseline models
HA, ARIMA, LSTM, DCRNN, [[STGCN]]

### Results
Also compared with different variations of the CACRNN
![[Pasted image 20240603151218.png]]

