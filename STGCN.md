Spatio-Temporal Graph Convolutional Networks
![[STGCN.pdf]]
# Introduction

The paper presents methods to effectively capture the temporal and spatial patterns in traffic flow. Instead of viewing the traffic network as separate grids or segments, it represents it as a general graph to better leverage spatial data. To address the shortcomings of recurrent networks, a fully convolutional structure along the time axis is used. The key contribution is the development of a novel deep learning model, called spatio-temporal graph convolutional networks, specifically designed for traffic forecasting. This model consists of spatio-temporal convolutional blocks that integrate graph convolutional layers with convolutional sequence learning layers, enabling the simultaneous modelling of spatial and temporal dependencies. This approach is, to our knowledge, the first to apply purely convolutional structures for extracting spatio-temporal features from graph-based time series in traffic analysis.

# Preliminaries

### Traffic prediction on road graphs
Traffic forecast, as a time-series prediction problem, can be expressed as the traffic flow in the next $H$ time steps given previous $M$ traffic observations:
$\tilde{v}_{t+1},...,\tilde{v}_{t+H} = argmax_{v_{t+1},...,v_{t+H}} log P(v_{t+1},...,v_{t+H} | v_{t-M+1},...,v_{t})$, where $v_t \in R^n$ is an observation vector of n road segments at time step $t$, and each element holds historical observation for a single road segment.
Further, at time step $t$, a graph $G_t = (V_t, E, W)$, where $V_t$ is a finite set of vertices, corresponding to the observations from $n$ monitor stations in a traffic network; $E$ is a set of edge, indicating a connection between stations, and $W \in R^{nxn}$ is the weighted adjacency matrix.
### Convolutions on Graphs
The notion of graph convolution operator $*_G$ is introduced based on the conception of spectral graph convolution, as the multiplication of a signal $x \in R^n$ with a kernel $\Theta$ 
![[Pasted image 20240603103907.png]]
# Proposed model

### Network architecture
![[Pasted image 20240603110532.png]]
### Graph CNNs for extracting spatial features
The Theta formula from the previous section, can be expensive as O(n^2), but two approximation strategies are applied to overcome this issue.
##### Chebyshev polynomials approximation
From O(n^2) we can go to O(K|E|). 
##### 1st-order approximation
They are a simplified way to perform graph convolutions, making them efficient for large-scale graphs. They simplify the convolution operation by merging two parameters into one and normalizing the graph's adjacency matrix. The result is a deep architecture can be created to capture spatial information efficiently, with each layer using information from neighbouring nodes up to a certain depth (defined by the number of layers, K).
##### Generalisation of graph convolutions
![[Pasted image 20240603111744.png]]
Graph convolutions can handle multi-channel and multi-frame data, making them suitable for complex inputs like traffic prediction data.
### Gated CNNs for extracting temporal features
The popular for time-series analysis, suffer when it comes to traffic predictions due to their time-consuming nature and inability to respond to dynamic changes. CNNs, on the other hand, have fast training, simple structure, and no dependency constraints to previous steps. 
### Spatio-temporal convolutional block
This block fuses both spatial and temporal features. The sandwich structure (middle picture from the network architecture) helps the network sufficiently apply bottleneck strategy to achieve scale compression and feature squeezing by downscaling and upscaling of channels C through the graph convolutional layer. 

### Model summary
- STGCN is a universal network that can handle any type of spatio-temporal sequence learning task
- The spatio-temporal block combines graph convolutions and gated temporal convolutions, which extract spatial and temporal features, respectively
- The model is entirely composed of convolutional structures and therefore achieves parallelisation over input with fewer parameters and faster training speed. Also, due to the approximations included, large-scale networks can be handled as well.
# Experiment

### Dataset
Using two real-world traffic datasets - BJER4 (Beijing) and PeMSD7 (California). 
![[Pasted image 20240603113453.png]]
### Settings
Only workday traffic data is used to eliminate atypical traffic (Li et al., 2015). Grid search is used to locate the best parameters on validations. All tests use 60 minutes as a historical time window (i.e. 12 observed data points; M=12 are used to forecast traffic conditions in the next 15, 30, and 45 minutes)
### Evaluation metrics
MAE, MAPE, RMSE
### Baseline models
HA, Linear SVR, ARIMA, FNN, FC-LSTM, GCGRU
### Results
![[Pasted image 20240603114016.png]]
![[Pasted image 20240603114026.png]]
![[Pasted image 20240603114121.png]]

Paper mentions: [[GCRN]]