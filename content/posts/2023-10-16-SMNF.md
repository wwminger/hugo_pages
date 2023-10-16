---
title:  "An ant colony optimization metaheuristic for single-path multicommodity network flow problems"
date:   2023-10-16 10:19:31 +0800
tags: 
    - math
---

# An ant colony optimization metaheuristic for single-path multicommodity network flow problems+
Footnote †: This work was supported by research grant from the Natural Science and Engineering Research Council of Canada.

X-Y Li

_Codete School of Business, University of Windsor, Windsor, Ontario, Canada_

 YP Aneja and F Baki

###### Abstract

This paper studies the single-path multicommodity network flow problem (SMNF), in which the flow of each commodity can only use one path linking its origin and destination in the network. We study two versions of this problem based on two different objectives. The first version is to minimize network congestion, an issue of concern in traffic grooming over wavelength division multiplexing (WDM), and in which there generally exists a commodity flow between every pair of nodes. The second problem is a constrained version of the general linear multicommodity flow problem, in which, for each commodity, a single path is allowed to send the required flow, and the objective is to determine a flow pattern that obeys the arc capacities and minimizes the total shipping cost. Based on the node-arc and the arc-chain representations, we first present two formulations. Owing to computational impracticality of exact algorithms for practical networks, we propose an ant colony optimization-(ACO) based metaheuristic to deal with SMNF. Considering different problem properties, we devise two versions of ACO metaheuristics to solve these two problems, respectively. The proposed algorithms' efficiencies are experimentally investigated on some generated instances of SMNF. The test results demonstrate that the proposed ACO metaheuristics are computationally efficient and robust approaches for solving SMNF.

2010 61, 1340\(-\)1355. doi:10.1057/jors.2009.86

**Keywords:** networks and graphs; multicommodity network flow; single path; routing strategy; ant colony optimization; heuristics

## 1 Introduction

In this paper, we consider a single-path multicommodity network flow problem (SMNF), which is a variant of the general linear multicommodity flow (MCF) problem (Ahuja _et al_, 1993). In contrast to the MCF problem, the main characteristic of SMNF lies in that only a single path is allowed for the flow of each commodity. We study two different versions of SMNF based on two different objectives: one is with the objective of minimizing the congestion (SMNF-1); another is the problem where the objective is to minimize the transportation cost (SMNF-2).

SMNF-1 is derived from the logical topology design problem over wavelength division multiplexing (WDM) in optical networks. An optical network connects computers (or any other device which can generate or store data in electronic form) using optical fibers. To facilitate data communication, an optical network also includes other optical devices to generate optical (electrical) signals from electrical (respectively optical) data, to restore optical signals after the signal propagates through fibers, and to route optical signals through the network. Optical networks, though very expensive, have found widespread use because the bandwidth of such networks using current technology is 50 tera-bits per second. The technology of using multiple optical signals on the same fiber is called WDM (Sung and Rim, 2004).

In an optical network, all possible sources or destinations of data (typically computers) are called end-nodes. Another important component of an optical network is the optical router. The optical router is to direct each incoming optical signal to an appropriate outgoing fiber, without converting an optical signal to an electronic signal and then back to an optical signal. The optical network can be formally represented as a directed graph where the nodes denote end-nodes combined with routers, and arcs (directed edges) represent unidirectional optical fibers. Such a diagram is called a physical topology, as it shows the major physical components of the network. A lightpath is an optical circuit-switched connection, from one end-node to another, used to carry data in the form of encoded optical signals. Such a lightpath always starts from an end-node, traverses a number of fibers and router nodes, and ends in another end-node. It is convenient to view the lightpaths as edges of a directed graph \(G_{L}\) where the nodes of \(G_{L}\) are the end-nodes of the physical topology. Such a graph is called the logical topology (some researchers use the term virtual topology) of an optical network and the edges of such a graph are called logical edges.

The logical topology design on a physical network consists of determining the lightpaths to be set up in terms of their source and destination and wavelength assignment (Dutta andRouskas, 2000). A general goal of logical topology design is to minimize the network congestion, the value of which is defined as the maximum traffic on network arcs. (The unit of a traffic is the capacity of a lightpath (2.5 GB/s or 10 GB/s currently, depending on the topology used.) To understand why the congestion is an important performance parameter, we quote from a standard reference (Ramaswami and Sivarajan, 2002):

Let us consider the case where the data packet arrivals follow a Poisson process and the packet transmission times are exponentially distributed with mean time given by \(1/\mu\) seconds. Making the standard assumption that the traffic offered to a lightpath in the network is independent of the traffic offered to other lightpaths, each lightpath can be modeled as an \(M/M/1\) queue. The average queuing delay on lightpath (\(i\), \(j\)) is then given by \(\dot{\psi}_{ij}=\frac{1}{\mu-\lambda}\) where \(\lambda\) is the total traffic routed over lightpath (\(i\), \(j\)). The throughput can be defined as the minimum value of the offered load for which the delay on any lightpath becomes infinite. This happens when \(\dot{\lambda}_{\text{max}}=\max_{(i,j)}\dot{x}_{ij}=\mu\). Thus it is important to minimize the congestion \(\dot{\lambda}_{\text{max}}\).

As pointed out in Dutta and Rouskas (2000), determining the logical topology, the routing and wavelength assignment for each lightpath, and a traffic strategy over the logical topology with the objective of minimizing the network congestion are not _independent_ problems. Using mixed integer linear programming (MILP), early researchers tried to optimize the above three problems together in one formulation. The disadvantage of these approaches is that such formulations are computationally intractable, even for moderate-sized networks, as the number of variables and constraints grow very quickly as the network size increases (Dutta and Rouskas, 2000; Aneja _et al_, 2006). This makes us switch to finding a good solution quickly by sub-optimal procedures. Generally, the problem is decomposed into three independent sub-problems, namely, (1) find a logical topology; (2) carry out the routing and wavelength assignment for each lightpath; (3) find an optimal traffic routing strategy (Dutta and Rouskas, 2000; Bandyopadhyay, 2007). In this paper, we consider SMNF-1 that corresponds to sub-problem (3) over the 'non-bifurcated' network model. This problem can be defined as follows: given a feasible logical topology, optimally route the traffic request between the source and destination nodes to minimize the network congestion with the constraint that each communication request can only be communicated on the logical topology by a single path between its source and destination nodes. The bifurcated routing strategy is also used in other papers where each traffic request can be split into multiple paths. (Rouskas and Dutta, 2000; Aneja _et al_, 2006). Although the requirement that the data stream corresponding to each request be communicated using a single logical path from the source of the data stream to its destination may reduce the utilization of network resources and/or increase the blocking probability, this efficient utilization may increase the complexity and the cost of traffic reassembly, and may also introduce delay jitter at the application layer. Many applications, especially real-time applications, require that their traffic be kept intact, that is, without demultiplexing at the source, independent switching at intermediate nodes, and multiplexing at the destination (Bandyopadhyay, 2007). Throughout this paper, unless otherwise specified, a traffic flow is equivalent to a commodity flow in the network.

SMNF-2 also arises in a wide variety of application contexts, for example, transportation and production planning. Barnhart _et al_ (2000) give some instances: (1) bandwidth packing problems and (2) package flow problems. In the case of video teleconferencing, each call should be routed on exactly one network path if call splitting is prohibited. In package delivery, all packages with common origin and destination can be viewed as a commodity and often must be assigned to a single network path (Barnhart _et al_, 2000). Therefore, these delivery problems can be modeled as a single-path multicommodity network flow problem. Given the capacity on each arc, the objective is to find the commodity paths with the minimum transportation cost.

### Related work

In the literature, the optimization techniques of integer linear programming (ILP) and MILP are used to deal with the traffic routing problem in WDM networks (Dutta and Rouskas, 2000). In Bandyopadhyay (2007), the non-bifurcated traffic grooming problem is studied, which has the objective of minimizing the cost of the network by minimizing the number of lightpaths and maximizing the throughput of the network. Hu and Leida (2004) study the non-bifurcated traffic grooming, routing, and wavelength assignment in optimal WDM mesh networks (GRWA) where the optimization objective is to minimize the number of transponders in the network. For a given logical topology of a WDM network, the MILP of the routing sub-problem can be derived based on the node-arc representation. Such a formulation can be solved directly by a mathematical programming tool, for example, LINGO, and CPLEX etc. But this formulation can only work well for relatively smaller networks. To handle the routing problem in large WDM networks, Aneja _et al_ (2006) use an arc-chain formulation to minimize the network congestion for bifurcated traffic grooming. In Hu and Leida (2004), the GRWA problem is formulated as an ILP problem and a decomposition method is considered to computationally solve GRWA problems on large networks. That is, the GRWA is divided into two smaller problems: the traffic grooming and routing problem and the wavelength assignment problem. To our knowledge, no study has looked at the non-bifurcated traffic routing with the objective of minimizing the network congestion (SMNF-1).

In the literature, the linear MCF problem has been studied extensively and many effective algorithms have been developed and tested. These approaches include price-directive decomposition, resource-directive decomposition, and partitioning methods. For the comprehensive survey of linear MCF problems and the solution approaches, the interested reader can refer to Ahuja _et al_ (1993). Contrary to the linear MCF problem, SMNF-2 has attracted less attention. Barnhart _et al_ (2000) present a column-generation model and branch-and-price-and-cut algorithm for SMNF-2. The branch-and-price-and-cut algorithm is a variant of the branch-and-bound algorithm, with bounds probed by solving the linear programs using column-and-cut generation at nodes of the branch-and-bound tree. Such exact algorithms are feasible only for smaller instances of SMNF-2.

### Our contributions

For SMNF-1 in WDM networks, there generally exists a communication request between each pair of end-nodes. For example, in a network with 40 nodes, a total number of 1560 communication requests might need to be handled. Thus the total number of binary variables will become very large. This is also verified by the computational results in (Hu and Leida, 2004). Although the decomposition method is used, it takes about 40 h to get a grooming and routing strategy for a network with 30 nodes, 38 fiber spans, 47 lightpaths, and 242 demands. Therefore, it is impractical to get the optimal routing strategy to minimize the network congestion for large WDM networks.

Similar conclusions also hold for SMNF-2. Although an exact algorithm is proposed in Barnhart _et al_ (2000), this method can only work well on smaller instances.

SMNF for larger networks is therefore computationally challenging. Thus, it is impractical to produce optimal solutions for large instances by exact algorithms. An alternative way is to quickly produce good, although not necessarily optimal, solutions by implementing heuristics or metaheuristics. With this objective in mind, we present an ant colony optimization-(ACO) based metaheuristic to solve SMNF, especially for large instances. Considering different properties of SMNF-1 and SMNF-2, we develop two different versions of ACO algorithms and investigate their performances on some generated instances. The computational results indicate that the proposed ACO algorithms are computationally effective approaches for SMNF, especially for larger instances.

### Outline

The remainder of this paper is organized as follows. In Section 2, we present two different formulations for SMNF-1 and SMNF-2, respectively. In Section 3, we devise two ACO-based algorithms to solve the above two problems. In Section 4, we provide a series of experiments to evaluate the performance and the efficiency of the two proposed ACO metaheuristics. Finally, we offer a summary in Section 5.

## 2 Models for SMNF

In this section, we describe two different formulations for SMNF. One is the node-arc formulation, and another is the arc-chain formulation. Accordingly, the formulations for SMNF-1 and SMNF-2 are presented, respectively.

### Notations

* \(N\) the set of nodes, \(N=\{1,\,2,\,3,\,\ldots,\,n\}\);
* \(A\) the set of arcs (\(i\), \(j\)) such that there is a lightpath from \(i\) to \(j\);
* \(K\) the set of commodities, \(K=\{1,\,2,\,3,\,\ldots,\,|K|\}\). Generally, the total number of commodities is \(n\times(n-1)\) in SMNF-1;
* \(q^{k}\) required quantity of commodity \(k\);
* \(s_{k}\) the source node of commodity \(k\);
* \(d_{k}\) the destination node of commodity \(k\);
* \(d_{ij}\) the capacity of arc (\(i\), \(j\)) \(\in A\);
* \(c^{i}_{ij}\) unit flow cost for commodity \(k\) on arc (\(i\), \(j\));
* \(x^{k}_{ij}\) a binary decision variable; it equals 1 if the entire quantity of commodity \(k\) uses arc (\(i\), \(j\)) for shipment, and equals 0 otherwise;
* \(\dot{\lambda}_{\text{max}}\) network congestion that equals the maximum traffic load on arcs in the network.
* \(c^{k}_{p}\) unit flow cost of commodity \(k\) on path \(p\);
* \(P(k)\) the set of origin-destination paths for commodity \(k\) in the network;
* \(\tilde{z}^{p}_{ij}\) a binary variable that is equal to 1 if arc (\(i\), \(j\)) is in path \(p\) of commodity \(k\), \(p\in P(k)\), and equals 0 otherwise;
* \(y^{k}_{p}\) a binary decision variable; If all the quantity of commodity \(k\) is assigned to path \(p\), \(y^{k}_{p}=1\), otherwise it equals 0.

### Formulations of SMNF-1

(1) _The node-arc formulation of SMNF-1_

\[\min \dot{\lambda}_{\text{max}}\] (1) s.t. \[\sum_{k\in K}q^{k}x^{k}_{ij}\leqslant\dot{\lambda}_{\text{max}} \qquad\forall(i,\,j)\in A \tag{2}\] \[\sum_{j:(i,j)\in A}x^{k}_{ij}-\sum_{j:(i,j)\in A}x^{k}_{ji}=\begin{cases} 1&\text{if }i=s_{k}\\ -1&\text{if }i=d_{k}\\ 0&\text{otherwise}\end{cases}\] \[x^{k}_{ij}\in\{0,\,1\}\qquad\forall(i,\,j)\in A\qquad\forall k\in K \tag{3}\]

where constraint (2) defines the congestion value in WDM networks. The objective of SMNF-1 is to minimize \(\dot{\lambda}_{\text{max}}\). Constraints (3) and (4) ensure that each commodity is sent along a single path from its source to its destination.

(2) _The arc-chain formulation of SMNF-1_

\[\min \lambda_{\max}\] s.t. \[\sum_{k\in K}\sum_{p\in P(k)}q^{k}y_{p}^{k}\bar{\varsigma}_{ij}^{p} \leqslant\lambda_{\max}\qquad\forall(i,\,j)\in A \tag{6}\] \[\sum_{p\in P(k)}y_{p}^{k}=1\qquad\forall k\in K\] (7) \[y_{p}^{k}\in\{0,1\}\qquad\forall k\in K\qquad\forall p\in P(k) \tag{8}\]

where constraints (7) and (8) ensure that each commodity should be assigned exactly to one path linking its origin and destination. Generally, the linear programming (LP) relaxation of the arc-chain formulation can be solved by the column-generation approach.

(3) _Comparison of two formulations for SMNF-1_

Let \(m\) and \(P_{k}\) be the number of arcs in the network, and the number of paths for commodity \(k\), respectively. Table 1 summarizes the number of binary variables and constraints for the two formulations. The arc-chain formulation contains \(|K|+m\) constraints. In contrast, the node-arc formulation includes \(m+n|K|\), as it has one mass balance constraint for every node and commodity combination. For example, a WDM network with \(n\)=100 nodes, and \(m\)=5000 arcs and with a commodity between each pair of nodes has approximately \(|K|\approx n^{2}=10\,000\) commodities. The arc-chain formulation has about 15 000 constraints. In contrast, the node-arc formulation contains about 1 005 000 constraints. On the other hand, the arc-chain formulation contains a large number of binary variables because there may be a large number of paths for each commodity in the network. The saving in the number of constraints does come at a computational cost. The number of variables will typically be enormous, growing exponentially in the size of the WDM network. The number of binary variables is the crucial factor determining the time required to solve these two formulations. The computational time will be high, even for some smaller networks.

### Formulations of SMNF-2

Although SMNF-2 is very similar in structure to SMNF-1, it is a capacitated integer multicommodity network flow problem where the individual commodities share common arc capacities. SMNF-2 can also be formulated by node-arc and arc-chain models. Similar formulations are also given in Barnhart _et al_(2000).

(1) _The node-arc formulation of SMNF-2_

\[\min Z=\sum_{k\in K}\sum_{(i,j)\in A}c_{ij}^{k}q^{k}x_{ij}^{k}\] (9) s.t. \[\sum_{k\in K}q^{k}x_{ij}^{k}\leqslant d_{ij}\qquad\forall(i,\,j)\in A\] (10) (3)-(4)

where constraint (10) indicates that the total flow of all commodities on each arc \((i,\,j)\) cannot exceed the capacity limit of \(d_{ij}\).

(2) _The arc-chain formulation for SMNF-2_

\[\min Z=\sum_{k\in K}\sum_{p\in P(k)}c_{p}^{k}q^{k}y_{p}^{k}\] (12) s.t. \[\sum_{k\in K}\sum_{p\in P(k)}q^{k}y_{p}^{k}\bar{\varsigma}_{ij}^ {p}\leqslant d_{ij}\qquad\forall(i,\,j)\in A\] (13) (7)-(8)

where constraint (13) ensures that the capacity constraint on each arc cannot be violated.

(3) _Lower bounds of SMNF-2_

As most practical networks result in a large number of variables and constraints in SMNF-2, exact algorithms can only solve smaller instances in moderate CPU time. We, therefore, consider a heuristic to solve large instances of SMNF-2. To evaluate the performance of heuristics, two equivalent lower bounds are obtained as follows:

The first choice is the lower bound \(Z_{LP}\) obtained by relaxing integrality constraints on variable \(x_{ij}^{k}\) in model (9)-(11), and solving the resulting LP problem. In our experiments, we use CPLEX 11.0 to solve LP relaxation problem to get \(Z_{LP}\).

To apply Lagrangian relaxation to SMNF-2, we can obtain the Lagrangian relaxation based lower bound, \(Z_{LR}\). We associate non-negative Lagrangian multipliers \(u_{ij}\) with constraint (10) and add them into the objective function in (9). The resulting Lagrangian relaxation problem is as follows:

\[Z_{LR}(u)=\min \sum_{k\in K}\sum_{(i,j)\in A}(c_{ij}^{k}+u_{ij})q^{k}x_{ij}^{k}- \sum_{(i,j)\in A}u_{ij}d_{ij}\] (15) s.t. (3)-(4)

For any given Lagrangian multipliers, the term \(-\sum_{(i,j)\in A}u_{ij}d_{ij}\) is a constant and therefore we can ignore it. We can see that none of the constraints in the reduced Lagrangian relaxation problem contains the flow variables for more than one commodity. The problem is decomposed into solving a shortest path problem for each commodity. To find the shortest path for commodity \(k\), we use \(c_{ij}^{k}+u_{ij}\) as the distance of arc \((i,\,j)\). To find \(Z_{LR}\) where \(Z_{LR}=\max_{u\geqslant 0}Z_{LR}(u)\), we can use the subgradient optimization algorithm (Fisher, 1985).

As pointed out in Fisher (1981, 1985), \(Z_{LR}\geqslant Z_{LP}\), and \(Z_{LR}=Z_{LP}\) only if the Lagrangian problem is unaffected by removing the integrality requirement on \(x_{ij}^{k}\). For our

\begin{table}
\begin{tabular}{l l l} \hline \hline _Item_ & \begin{tabular}{l} _Node-arc-based_ \\ _formulation_ \\ \end{tabular} & \begin{tabular}{l} _Arc-chain-based_ \\ _formulation_ \\ \end{tabular} \\ \hline \begin{tabular}{l} Number of binary variables \\ Number of constraints \\ \end{tabular} & \begin{tabular}{l} \(m|K|\) \\ \(m+n|K|\) \\ \end{tabular} & 
\begin{tabular}{l} \(\sum_{k\in K}P_{k}\) \\ \(|K|+m\) \\ \end{tabular} \\ \hline \hline \end{tabular}
\end{table}
Table 1: Number of binary variables and constraints for two formulationsproblem, therefore, \(Z_{LR}=Z_{LP}\). Although both LP and Lagrangian relaxations will give the same lower bound, it is computationally more efficient to find the lower bound \(Z_{LR}\) than \(Z_{LP}\). Using exact algorithms, the LP relaxation problem still contains a large number of constraints and variables for large-scale SMNF-2. Without special techniques, it will take excessive CPU time to solve the LP relaxation problem. To find \(Z_{LR}\) for any given Lagrangian multipliers, using the subgradient optimization algorithm, only one shortest path problem needs to be solved for each commodity. The shortest path problem can be solved efficiently by many well-known algorithms (Ahuja _et al_, 1993).

## 3 ACO metaheuristic for single-path multicommodity network flow problems

We consider ACO to deal with SMNF problems, especially for large-scale instances. ACO is a nature-inspired metaheuristic for tackling hard combinatorial optimization problems that was first proposed in the early 1990s (Dorigo and Stutzle, 2004). Its inspiring source is the foraging behavior of real ant colonies. The central mechanism of ACO algorithms is to probabilistically construct solutions using a parameterized probability model, which is indicated by the pheromone trails. In the context of ACO, the solution component is usually associated with the pheromone trail. Using the pheromone model, artificial ants probabilistically add the solution component to the partial solution until they generate a completely feasible solution. During these iterations, the pheromone values are dynamically updated (including the evaporation and intensification procedures) based on the information derived from some high-quality solutions. The aim of pheromone update is to force the search to concentrate on regions containing high-quality solutions in the solution space. Sometimes additional intensification or diversification strategies such as a local search, are also considered in ACO. For detailed descriptions of ACO and its variants, one may refer to Dorigo and Stutzle (2004).

As SMNF-1 and SMNF-2 have different constraints, objectives, and network properties, we therefore, develop two different ACO metaheuristics to solve them respectively. The main framework of both proposed ACO algorithms is a \(\mathcal{MLX}\)-\(\mathcal{MLN}\) ant system. It forces the pheromone values in the interval \([\tau_{\min},\tau_{\max}]\), which increases the diversification and prevents the algorithm from converging to a local solution. To automatically scale the objective functions, we implement the proposed algorithms in the hyper-cube framework (Blum and Dorigo, 2004) where pheromone values are restricted in the interval [0,1].

### An ACO metaheuristic for SMNF-1

```
Set algorithmic parameters while(termination conditions not met)do \(P\leftarrow\emptyset\)  InitialNetworkTraffic(\(D\), \(A\)) for\(k=1\) to \(|K|\)do  ComputeHeuristic(\(D\), \(A\)) \(p^{k}\leftarrow\texttt{PathConstruction}(\mathcal{F}_{k},\eta,k)\) {See Algorithm 2} \(P\gets P\bigcup p^{k}\)  UpdateTraffic(\(D\), \(p^{k}\)) endfor  LocalSearch(\(P\), \(\lambda_{\max}\)) \(\lambda_{\max}=\texttt{EvaluateSolution}(P)\) if\(P^{*}=\emptyset\) or \(\lambda_{\max}<\lambda^{*}\)then \(i_{0}=0\), \(P^{*}\gets P\) and \(\lambda^{*}=\lambda_{\max}\) else \(i_{0}\gets i_{0}+1\) endif if\(i_{0}\geqslant i_{\text{no}}\)then  ResetPheromoneTrails(\(\mathcal{F}\), \(A\)) else  PheromoneTrailsUpdate(\(\mathcal{F}\), \(P^{*}\), \(\rho\)) endif endwhile  output \(P^{*}\) and \(\lambda^{*}\)
```

Different from general \(\mathcal{NP}\)-hard CO problems, to which ACO algorithms are applied, SMNF-1 has a special property where there exists a traffic flow between each pair of nodes. We therefore associate each commodity \(k\) with one artificial ant, which independently searches for a path linking the source and destination of commodity \(k\). At each iteration, each artificial ant starts from its origin and moves toward its destination where a greedy stochastic policy is used to choose the next node to move to. This transition policy is indicated by the pheromone trails and local heuristic information. Artificial ants act independently and communicate in an indirect way through pheromone trails and net work's current traffic status. When the path of one commodity is constructed, the current traffic state of the network is updated and the traffic information is then used for other commodities' path constructions. We refer to the proposed ACO algorithm for SMNF-1 as ACO-MC. The general framework of ACO-MC is shown in Algorithm 1. Before discussing the components of ACO-MC in detail, we first define some symbols used. These notations include those given in formulation definition and those given below.

* \(p^{k}\)= the constructed path of commodity \(k\) linking its origin and destination nodes;
* \(P\)= the set of paths already constructed at each iteration, \(P=\{p^{k}|k\in K\}\);
* \(\lambda^{*}\)= the minimum network congestion since the start of ACO-MC;
* \(P^{*}\)= the set of paths when the network has minimum congestion;* \(D\)= matrix of current traffic status of the network, \(D\) = \(\{D_{ij}|(i,j)\in A\}\). Based on paths already constructed in \(P\), \(D_{ij}\) is defined as \(\sum_{(i,j)\in A}\sum_{p^{i}\in P}x_{ij}^{k}q^{k}\).
* \(i_{0}\)= the number of iterations without solution improvement in ACO-MC;
* \(i_{\infty}\)= maximum allowed iterations without solution improvement found in ACO-MC.

_Definition of pheromone trails and heuristic information_: In SMNF-1, we need to find a path for each commodity linking its source and destination. Accordingly, we maintain an independent pheromone matrix \(\mathcal{F}_{k}\) for each commodity \(k\), defined as \(\mathcal{F}_{k}\) = \(\{\tau_{ijk}|(i,j)\in A\}\). The entry \(\tau_{ijk}\) indicates the learned desirability for an ant to move to node \(j\) immediately after node \(i\) in the constructed path of commodity \(k\). At the start of ACO-MC, pheromone values are initialized to 0.5.

The heuristic information \(\eta_{ij}\) is associated with each arc \((i,j)\). It is problem-specific information that denotes the attracability of visiting node \(j\) as the next node in partial path \(p^{k}\) when the ant is located at \(i\). Node \(j\) is the neighboring node, that is, \(j\in\mathcal{N}(p^{k})\). To discuss the calculation of \(\eta_{ij}\), we first describe the definition of \(\mathcal{N}(p^{k})\). It is also used in the path construction procedure (see section PathConstruction(\(\mathcal{F}_{k},\eta,k\))). Let \(i\) be the end-node of partial path \(p^{k}\) of commodity \(k\). Define \(\mathcal{N}_{1}\) = \(\{j|(i,j)\in A,\,j\notin p^{k}\}\) and \(\mathcal{N}_{2}\) = \(\{j|(i,j)\in A\}\). \(\mathcal{N}(p^{k})\) defines the set of all neighboring nodes, which can be included in partial path \(p^{k}\) of commodity \(k\). It can be defined as

\[\mathcal{N}(p^{k})=\begin{cases}\mathcal{N}_{1}&\text{if }\mathcal{N}_{1}\neq \emptyset\\ \mathcal{N}_{2}&\text{otherwise}\end{cases} \tag{17}\]

Namely the ant first chooses the next node to visit among all unvisited nodes. If the ant arrives at a dead node, then we allow the ant to go back to one already visited node.

As in WDM networks, there exists a traffic flow between each pair of nodes, we consider two different kinds of information to define the heuristic information. On the one hand, heuristic information is used to reflect the current network traffic state. That is the ants will prefer to arcs with less traffic load. As there is a communication request between each pair of nodes, the total number of commodities becomes very large. To get a routing strategy with minimum congestion, we should reduce the influence of one path on the possible congestion status. With this thought in mind, we consider a natural approach, which is to make the ants prefer shortest paths. Here, the shortest path is the path containing the minimum number of arcs, which connects the next node \(j\) and the destination node of current commodity. Different from pheromone trails, all ants share the same heuristic information \(\eta_{ij}\).

\[\eta_{ij} =(\eta_{ij}^{1})^{\beta}(\eta_{ij}^{2})^{\gamma}\] \[=\left(1-\frac{D_{ij}}{\sum_{j^{\prime}\in\mathcal{N}(p^{k})}D_ {ij^{\prime}}}\right)^{\beta}\left(\frac{1}{W_{j_{d_{k}}}}\right)^{\gamma} \tag{18}\]

where \(\eta_{ij}\) is the function of \(\eta_{ij}^{1}\) and \(\eta_{ij}^{2}\). The first component \(\eta_{ij}^{1}\) reflects the current traffic state of the network. It is a normalized value in [0,1]. \(\eta_{ij}^{1}\) contains two special cases. The first is the situation where all neighboring arcs except one arc (\(i\), \(j_{0}\)) have 0 traffic. With the above equation, \(\eta_{ij_{0}}^{1}\) =0. In order to choose this arc with a positive probability, we can set \(\eta_{ij_{0}}^{1}\) to a very small value, for example, \(\eta_{ij_{0}}^{1}\) = 0.001. Additionally, at the start of each iteration, we set all \(\eta_{ij}^{1}\) equal to 1. The second component \(\eta_{ij}^{2}\) is inversely proportional to the shortest distance \(W_{j_{d_{k}}}\) between \(j\) and the destination node \(d_{k}\) of current commodity \(k\). When the next node \(j\) is the destination node, the value of \(W_{j_{d_{k}}}\) is set to 1. \(\beta\) and \(\gamma\) are two coefficients, which weigh the relative importance of two components.

Algorithm 2. Path construction in ACO-MC-- PathConstruction(\(\mathcal{F}_{k},\eta,k\)) \(p^{k}\) = \(\{s_{k}\}\), _current_\(\gets s_{k}\)

**while** (_current_ \(\neq d_{k}\)) **do**

 Choose next node \(v\in\mathcal{N}(p^{k})\) according to the

 policy in Equations (19)-(20)

\(p^{k}\gets p^{k}\bigcup v\);

**end while**

 PathCycleDelete(\(p^{k}\))

 output (\(p^{k}\))

_ComputeHeuristic(\(D,\,A\))_: The heuristic information is dynamically updated according to the current traffic state of the networks. After the path is constructed for one commodity, its quantity is placed on the path in the network and current traffic status \(D\) will accordingly be updated. Such a traffic status will be used by other ants in the path construction, which is indicated in the new heuristic information. Before the path construction, the heuristic information is first computed with a new network traffic state.

_InitialNetworkTraffic_(\(D\), \(A\)): At the start of each iteration, the network is initialized as a null network, that is, \(D\) = \(\{D_{ij}\) = \(0|(i,j)\in A\}\).

_PathConstruction_(\(\mathcal{F}_{k}\), \(\eta,k\)): The most important component of ACO-MC is the greedy stochastic policy, which is applied to construct the network path for each commodity. Such selection policy is a probability model indicated by pheromone values and heuristic information. At the path construction stage, an ant independently searches a path for commodity \(k\) and incrementally constructs a path by sequentially adding a node to partial path \(p^{k}\) until it reaches its destination \(d_{k}\). The ant starts from the origin and moves step by step toward its destination. The procedure of the path solution is given in Algorithm 2. We take commodity \(k\) as an example to explain how its path is built.

Located at current node \(i\), the ant selects the next node \(j\) to move to among \(\mathcal{N}(p^{k})\) by a pseudorandom proportional rule. The transition rule is defined as

\[j=\left\{\begin{array}{ll}\text{arg max}_{\tau\in\mathcal{N}(p^{k})}\{\tau_{ ijk}\cdot\eta_{iv}\}&\text{if }r\leqslant p_{0}\\ H&\text{otherwise}\end{array}\right. \tag{19}\]where \(r\) is a random number in [0,1]. \(p_{0}\) is a parameter, which controls the balance between the diversification and the intensification. When \(r>p_{0}\), the next node \(H\) is determined using the roulette wheel selection procedure of evolutionary computation. The probability distribution is defined as follows:

\[p_{ij}^{k}=\left\{\begin{array}{ll}\frac{\tau_{ijk}\cdot\eta_{ij}}{\sum_{j \in\mathcal{N}^{\prime}(p^{k})}\tau_{ij^{\prime}k}\cdot\eta_{ij^{\prime}}}& \text{if }j\in\mathcal{N}^{\prime}(p^{k})\\ 0&\text{otherwise}\end{array}\right. \tag{20}\]

where \(p_{ij}^{k}\) denotes the probability of node \(j\) being chosen as the visiting target in the partial path of commodity \(k\) by the ant.

As an ant is allowed to return to the already visited nodes, a cycle may occur. This further results in the situation where a path covers many nodes. We consider a parameter \(\max_{l}\) to control the maximum lifetime of each path. Although the path of one commodity contains more than \(\max_{l}\) nodes, the ant deletes this path and the memory about it, and reconstructs a new path. In our experiment, \(\max_{l}\) is set to 25. PathCycleDelete(\(p^{k}\)) in Algorithm 2 is used to delete the cycle in each path.

After the path is constructed for one commodity, the corresponding quantity will be placed on the network. And the local traffic status of the network is thus updated, which is achieved by the procedure UpdateTraffic(\(D\), \(p^{k}\)).

_PheromoneTrailsUpdate_(\(\mathcal{F},\rho\)): The aim of pheromone update is that the information contained in some good solutions should be indicated by pheromone trails and the nodes included in these solutions will be biased by other ants in constructing the following solutions. The pheromone update contains two subprocedures, namely, evaporation, and reinforcement. First, pheromone trails on all arcs are reduced by an evaporation, ratio. Second, some additional pheromone trails will be deposited on the arcs associated with some good solutions. That is a reinforcement procedure of pheromone levels.

As we define an independent pheromone matrix for each commodity, we implement an independent update procedure with each commodity, which is very different from the procedures used in general implementation of ACO algorithms. In SMNF-1, we do not have an objective function to evaluate the path of each commodity. All artificial ants cooperate with each other to find a network traffic mode with minimum congestion. As a result, the ants should share the collective search experience. As a natural idea, more additional reinforcement should be given to the paths when all ants collaborate with each other in finding a better traffic mode (less congestion). Therefore, we define a best path \(p_{best}^{k}\) for each commodity \(k\). It is the path of commodity \(k\) when the network has a best traffic mode. As ACO-MC is an ACO algorithm implemented in the hyper-cube framework, the following update rule is considered:

\[\tau_{ijk}\leftarrow\tau_{ijk}+\rho(\chi(i,\,j,\,\rho_{best}^{k})-\tau_{ijk}) \qquad\forall\tau_{ijk}\in\mathcal{F}_{k} \tag{21}\]

The function \(\chi\) is given as

\[\chi(i,\,j,\,p_{best}^{k})\!\!=\!\left\{\begin{array}{ll}1&\text{if arc ($i$, $j$) is included in path $p_{best}^{k}$}\\ &\text{of commodity $k$}\\ 0&\text{otherwise}\end{array}\right. \tag{22}\]

where \(\rho\) is the reinforcement that is automatically tuned in the experiment. It is important to remark that using the best path to update pheromone trails is an exploitation procedure. By such an approach, the search memory and experience can be used and reinforced, which, in fact, is an intensification strategy. In ACO-MC, an upper bound \(\tau_{\text{max}}\) and a lower bound \(\tau_{\text{min}}\) are added to pheromone values. In our implementation, we have set the lower bound to 0.001 and the upper bound to 0.999. After finishing the above pheromone update, we use the following rule to set pheromone values in the interval [\(\tau_{\text{min}}\), \(\tau_{\text{max}}\)]:

\[\tau_{ijk}\leftarrow\min\{\tau_{\text{max}},\max\{\tau_{\text{min}},\,\tau_{ijk}\}\} \tag{23}\]

_Diversification and intensification strategies_: The vast literature on metaheuristics tells us that a promising approach to obtain high-quality solutions is to couple a local search algorithm with a metaheuristic. This also holds for ACO algorithms (Dorigo and Stutzle, 2004). Let \(\lambda^{*}\) be the congestion value of the feasible solution, to which the local search is applied. We use the following simple local search algorithm.

* Find arc (\(i^{*}\), \(j^{*}\)) with the traffic flow \(\lambda^{*}\) (it represents the congestion value corresponding to each solution).
* Find set \(K^{*}\) of commodities, which use arc (\(i^{*}\), \(j^{*}\)) in their paths.
* For each commodity \(k^{\prime}\) in \(K^{*}\), we compute the maximum reduction in the congestion value \(\Delta_{k^{\prime}}\) by three possible operations: (a) break arc (\(i^{*}\), \(j^{*}\)), and insert an unvisited node between \(i^{*}\) and \(j^{*}\) with maximum congestion saving; (b) break the path and reconnect the predecessor of node \(i^{*}\) to node \(j^{*}\); (c) break the path and reconnect node \(i^{*}\) to the successor of node \(i^{*}\).
* If the maximum value of \(\Delta_{k^{\prime}}(k^{\prime}\in K^{*})\) is greater than 0, we perform the operations and update the path of the corresponding commodity.
* Repeat Steps 1-3 until no improvement is found within the successive three trials.

As an intensification strategy, the best path of each commodity is considered to update the pheromone trails. This may trap ACO-MC in a local optimum. To help the algorithm escape from local optimum and enhance diversification, we consider a reset-based strategy. When the number of the iterations without improvement found reaches the value of \(i_{\text{no}}\), all pheromone values are set back to 0.5 by ResetPheromoneTrails(\(\mathcal{F},A\)).

### An ACO metaheuristic for SMNF-2


Algorithm 3. ACO for SMNF-2 (ACO-MTC)  Input the data of the problem instance  Initialize pheromone trails \(\mathcal{T}=\{\mathcal{F}_{k}|k\in K\}\) and  statistical data  Set algorithmic parameters while (termination conditions not met) do \(P\leftarrow\emptyset\) \(Z^{ib}=0\)  InitialNetworkTraffic(\(A\)) for\(k=1\) to \(|K|\)do \(p_{ib}^{k}\leftarrow\)PathConstruction(\(\mathcal{F}_{k}\), \(k\), \(n_{a}\))  {See Algorithm 4} \(P\gets P\bigcup p_{ib}^{k}\) \(Z^{ib}\gets Z^{ib}+f(p_{ib}^{k})\)  UpdateTraffic(\(D\), \(p_{ib}^{k}\)) endfor \(q\leftarrow\)PenaltyCoefficientUpdate(\(P\)) if\(p^{bs}=\emptyset\) or \(Z^{ib}<Z^{*}\)then \(i_{0}=0\), \(p_{bs}^{k}\gets p_{ib}^{k}\) and \(Z^{*}=Z^{ib}\) else \(i_{0}\to i_{0}+1\) endif if\(i_{0}\!\geqslant\!i_{\text{no}}\)then  ResetPheromoneTrails(\(\mathcal{T}\), \(A\)) else  PheromoneTrailsUpdate(\(\mathcal{T}\), \(P\), \(P^{bs}\), \(\kappa\)) endif endwhile  output \(P^{bs}\) and \(Z^{*}\)


Without capacity constraint on each arc, a feasible solution can always be established in ACO-MC. However, this is not the case in SMNF-2. Therefore, we need to take into account capacity constraints and establish a new ACO metaheuristic for SMNF-2. Henceforth, this heuristic is denoted as ACO-MTC. ACO-MTC is a multiple ACO metaheuristic where several ants act concurrently and independently to construct several paths for one commodity, through pheromone trails associated with the corresponding commodity. Artificial ants start from the commodity's origin and are launched toward the destination where a greedy stochastic policy is used to choose the next node to visit. Differently, the transition policy is indicated only by pheromone trails. Additionally, considering the capacity limit on each arc, ACO-MTC allows the ants to construct infeasible solutions. However, an alternative penalized function is used to evaluate the solutions (see in the following section). When several paths for one commodity are built, the quantity is placed on the arcs in the best path and the network traffic thus updated. Algorithm 3 describes the general framework of ACO-MTC. We first define some new notations.

* \(n_{a}\)= the number of ants that construct the paths for each commodity;
* \(p_{i}^{k}\)= the path of commodity \(k\) constructed by ant \(i\);
* \(P(k)\)= the constructed paths of commodity \(k\), \(P(k)\) = \(\{p_{1}^{k},\,p_{1}^{k},\,\ldots,\,p_{n_{a}}^{k}\}\);
* \(p_{ib}^{k}\)= the iteration-best path of commodity \(k\), \(p_{ib}^{k}\in P(k)\)
* \(P^{=}\) the set of best paths at current iteration, \(P=\{p_{ib}^{k}|k\in K\}\);
* \(P^{bs}\)= the set of paths of all commodities with minimum transportation cost since the start of ACO-MTC, \(P^{bs}=\{p_{bs}^{1},\,p_{bs}^{2},\,\ldots,\,p_{n}^{|K|}\}\) where \(p_{bs}^{k}\) is the path of commodity \(k\);
* \(D\)= the matrix of current traffic status of the network, \(D=\{D_{ij}|(i,\,j)\in A\}\). Based on paths already constructed in \(P\), \(D_{ij}\) is defined as \(\sum_{(i,j)\in A}\sum_{p^{i}\in P}x_{ij}^{k}q^{k}\); if arc \((i,\,j)\) is used in path \(p^{k}\) of commodity \(k\), \(x_{ij}^{k}=1\); otherwise \(x_{ij}^{k}\) equals 0;
* \(i_{0}\)= the number of iterations without solution improvement found;
* \(i_{\text{no}}\)= maximum allowed iterations without solution improvement found;
* \(f\)= evaluation function of each commodity;
* \(Z^{ib}\)= the objective value of the iteration best solution;
* \(Z^{*}\)= the objective value of the best-so-far solution;
* \(q\)= penalty coefficient in the objective function.

_Solution evaluation_: As mentioned previously, the ants are allowed to construct infea-sible paths, which violate the capacity constraint on some arcs. We consider an alternative penalized objective function to evaluate the path of each commodity as follows:

\[f(p_{g}^{k})=\sum_{(i,j)\in A}[c_{ij}^{k}q^{k}+q\cdot\max((L_{ij}-d_{ij}),\,0 )]x_{ij}^{k} \tag{24}\]

where \(f(p_{g}^{k})\) is the transportation cost of the \(g\)-th path of commodity \(k\). If arc \((i,\,j)\) is used in path \(p_{g}^{k}\) of commodity \(k\), \(x_{ij}^{k}=1\); otherwise \(x_{ij}^{k}\) equals 0. \(L_{ij}\) is the new traffic flow on arc \((i,\,j)\) if the quantity of the path \(p_{g}^{k}\) is put on the network. \(q\) is the penalty coefficient, which is dynamically updated according to whether previous solutions were infeasible or feasible. More precisely, they are adjusted as follows. \(q\) is initialized to 100. Then every 15 iterations \(q\) is multiplied by \(2^{(1-2/15)}\) where \(t\) is the number of SMNF-2 solutions satisfying the capacity constraint in the past 15 iterations. That is to say that \(q\) will be divided by 2 if the last 15 solutions are feasible for the capacity constraint. On the contrary, \(q\) will be multiplied by 2 if the last 15 solutions all violate the corresponding constraint. Other cases will produce an intermediate change in the value of \(q\). The value of \(q\) is updated by PenaltyCoefficientUpdate(\(P\)).

_InitialNetworkTraffic(\(A\))_: At the start of each iteration, the network is initialized as a null network where \(D_{ij}=0\) for all arcs \((i,\,j)\in A\).

_Definition of pheromone trails_: We associate an independent pheromone matrix \(\mathcal{F}_{k}\) with each commodity \(k\). It is defined as \(\mathcal{F}_{k}=\{\tau_{ijk}|(i,\,j)\in A\}\) where \(\tau_{ijk}\) also indicates the learned desirability for an ant to move to node \(j\) immediately afternode \(i\) in the path of commodity \(k\). At the start of ACO-MTC, all pheromone values are initialized to 0.5.

\(\mathit{PathConstruction}(\mathcal{F}_{k},k,n_{a})\): This stage is to probabilistically construct the path of each commodity. ACO-MTC also maintains a probability model, which is indicated by the pheromone trails. As we prefer finding the path for each commodity with less transportation cost, we use \(n_{a}\) ants to cooperate with each other in finding a better path for commodity \(k\) in ACO-MTC. At the stage of the path construction, these \(n_{a}\) ants start from the origin of commodity \(k\), and incrementally construct the paths by sequentially adding a node to the partial paths until they reach the destination \(d_{k}\). Algorithm 4 describes the procedure of \(\mathtt{PathConstruction}(\mathcal{F}_{k},k,n_{a})\). We take commodity \(k\) as an example to explain how its paths are built probabilistically. When being located at current node \(i\), ant \(l\) selects the next node \(j\) to move to by a pseudorandom proportional rule, choosing among the neighbors \(\mathcal{N}(p_{l}^{k})\). The transition rule is defined as follows:

 \(\mathtt{PathConstruction}(\mathcal{F}_{k},k,n_{a})\) \(P(k)=\emptyset\) for\(l=1\) to \(n_{a}\)do \(p_{l}^{k}=\{s_{k}\}\), current \(\gets s_{k}\) while (current \(\neq d_{k}\)) do  Choose next node \(v\in\mathcal{N}(p_{l}^{k})\) according to the policy in Equations (25)-(26) \(p_{l}^{k}\gets p_{l}^{k}\bigcup v\); endwhile  PathCycleDelete(\(p_{l}^{k}\)) \(P(k)\gets P(k)\bigcup p_{l}^{k}\) endfor \(p_{lb}^{k}=\argmin\{f(s)|s\in P(k)\}\)  Output \(p_{lb}^{k}\) \[j=\left\{\begin{array}{ll}\arg\max_{v\in\mathcal{N}(p_{l}^{k})}\tau_{ivk}& \text{if }\phi\leqslant p_{0}\\ J&\text{otherwise}\end{array}\right.\] (25)

where \(\mathcal{N}(p_{l}^{k})\) defines neighboring nodes, which consist of all unvisited nodes or all nodes. In other words, ant \(l\) first chooses the next node among all unvisited nodes. If it reaches a dead end-node, the ant is allowed to choose any node already visited. \(\phi\) is a number randomly generated in [0,1]. Similarly, \(p_{0}\) is a parameter controlling the balance between diversification and intensification search. If \(\phi\leqslant p_{0}\), the next node with the maximizing pheromone value is chosen, which focuses on intensification, and pays more attention to the search experience. However, if \(\phi>p_{0}\), the ant selects node \(J\) probabilistically determined by the roulette wheel selection procedure. The probability distribution is defined as follows:

\[p_{ij}^{kl}=\left\{\begin{array}{ll}\frac{\tau_{ijk}}{\sum_{j\in\mathcal{N} (p_{l}^{k})}\tau_{ij}k}&\text{if }j\in\mathcal{N}(p_{l}^{k})\\ 0&\text{otherwise}\end{array}\right. \tag{26}\]

where \(p_{ij}^{kl}\) denotes the probability for ant \(l\) to choose node \(j\) as the next node to move to in partial path of commodity \(k\).

As mentioned previously, ACO-MTC allows the ants to return to already visited nodes, which may lead to the occurrence of a cycle where a path covers many nodes. Similar to the implementation in ACO-MC, the parameter \(\max_{l}\) is also used to control the maximum lifetime of each path. In our implementation, we set \(\max_{l}\) to 20 in ACO-MTC.

After \(n_{a}\) ants finish the path construction for commodity \(k\), all these paths are evaluated based on equation (24). The one with the minimum transportation cost, \(p_{ib}^{k}\), is added into set \(P\) of iteration-best paths. The quantity of commodity \(k\) is put on the corresponding arcs contained in \(p_{ib}^{k}\) and current traffic status of the network is, therefore, updated.

_PheromoneTrailsUpdate_(\(\mathcal{F}\), \(P\), \(P^{\text{hs}}\), \(\kappa\)): The update of pheromone values is performed for each commodity, respectively. As SMNF-2 has an evaluation function with the paths for each commodity, a different update rule of pheromone values is used in ACO-MTC. For updating pheromone trails at each iteration, we either use the iteration-best path \(p_{ib}^{k}\) or the best-so-far path \(p_{hs}^{k}\). For commodity \(k\), the pheromone update rule is as follows:

\[\tau_{ijk}\leftarrow\tau_{ijk}+\kappa[\gamma(i,j,p^{k})-\tau_{ijk}]\quad\forall \tau_{ijk}\in\mathcal{F}_{k} \tag{27}\]

where \(p^{k}\) is the path considered for updating pheromone values for commodity \(k\). To facilitate the trade-off between the diversification and the intensification, the iteration best path \(p_{ib}^{k}\) is used at the early stage. This is focusing on the exploration search. At the late stage, the exploitation search should be reinforced, namely, the best-so-far path \(p_{bs}^{k}\) is used. In our experiments, \(p_{ib}^{k}\) is used for updating pheromone values in the first 300 iterations. Then, the best-so-far path \(p_{bs}^{k}\) is adopted in the subsequent iterations. \(\kappa\) is the evaporation rate. The function \(\gamma\) is given as

\[\chi(i,j,p^{k})\!=\!\begin{cases}1&\text{if }\text{arc }(i,j)\text{ is contained in the path }p^{k}\\ &\text{of commodity }k\\ 0&\text{otherwise}\end{cases} \tag{28}\]

In the experiment of ACO-MTC, we set \(\tau_{\text{min}}\) to 0.001 and \(\tau_{\text{max}}\) to 0.999. The same operation as in Equation (23) is performed to force pheromone values in interval \([\tau_{\text{min}},\tau_{\text{max}}]\).

_Diversification strategy_: We choose a reset-based diversification strategy. When it appears to be trapped in local optimum, ACO-MTC will be restarted. More precisely, the function \(\mathtt{ResetPheromoneTrails}(\mathcal{F},A)\) sets all pheromone values back to 0.5 when the value of \(i_{0}\) reaches the limit \(i_{\text{no}}\) in Algorithm 3.

## 4 Experiment and computational results

A series of experiments was carried out to check the efficiency of the two proposed ACO algorithms. The experiments are composed of two parts: (1) the performance of ACO-MC is first investigated on solving SMNF-1; (2) ACO-MTC's efficiency is then checked in the second experiment. In each of these two experiments, we first discuss how to generate the benchmark problems. We then conduct an automatic procedure to tune algorithmic parameters and get the best configuration. Finally, with the obtained configuration, we test the algorithms on the generated instances and report the results.

ACO-MC, ACO-MTC, and the subgradient method are all coded in C language and compiled on VC++6.0. CPLEX 11.0 is used to solve the LP relaxation and integer programming problems. All experiments are implemented on a PC with Intel CPU 2.2G under Windows XP. In all the experiments, the computation time is reported in seconds.

### Experiment 1 on SMNF-1

(1) _Benchmark problems_: To check ACO-MC's performance, we require the logical topology and the associated traffic matrix of WDM networks. Two sets of SMNF-1 instances are generated as follows:

The instances in set 1 are generated based on two real-world networks: the US National Science Foundation (NSF) network, and the British Synchronous Digital Hierarchy (SDH) network (Dorigo and Stutzle, 2004). The NSF network is the old US T1 backbone, which is composed of 14 nodes, 21 bidirectional links (42 directed arcs). The SDH network contains 30 nodes, 55 bidirectional links (110 directed arcs). We generate 20 instances with the NSF network and 10 instances with the SDH network respectively. As there exists a communication request between each pair of nodes, the total number of commodities are 182 and 870 in the SDH and NSF networks, respectively. The quantity of each commodity is an integer uniformly distributed in [5, 50].

The advantage of ACO-MC consists in finding good solutions in larger instances. To verify this advantage, we generate some new instances set 2. The instances in set 2 have larger networks with more nodes and arcs. The instance features are summarized in Table 2. We can see that total number of commodities increases significantly with network size. The largest instance generated has 60 nodes, 3540 commodities, and 396 arcs.

(2) _Parameter tuning_: ACO-MC has some parameters that influence the algorithmic performance. Here, an automatic procedure, _F-Race_ is considered to determine the best configurations of some main parameters. The _F-Race_ approach is proposed by Birattari (2005) to automatically configure the metaheuristic. Birattari (2005) gave the formal definition of parameter tuning and proposed a _F-Race_ approach to solve the tuning problem. The _F-Race_ approach is developed based on Friedman two-way analysis of variance by ranks, a statistical method for hypothesis testing. It can return the best configuration of a metaheuristic that performs statistically best with respect to some evaluation metric on a number of instances. For the detailed discussion of the _F-Race_ approach, one may refer to Birattari (2005).

The set of candidate configurations is given by all possible combinations of different values of the parameters of ACO-MC. Table 3 gives the levels of the related parameters to be tuned. Therefore, the total number of the configurations in this preliminary experiment is \(5\times 5\times 5\times 5\times 4\!=\!2500\). A total number of 100 instances are randomly generated for parameter tuning, which have similar properties to those generated in subsection _Benchmark problems_. The instances used for parameter tuning are different from those in the benchmark set. With each configuration, ACO-MC is executed once on each of the 100 instances for 8 s. The objective values of the best solutions found in each of the experiments are stored in a two-dimensional \(100\times 2500\) array. After getting the results under different configurations, we switch to the automatic tuning phase with the _F-Race_ approach. The results in the \(100\times 2500\) array become the input of the _F-Race_ and the final configuration returned is as follows: \(\beta=2\), \(\gamma=3\), \(\rho=0.1\), \(p_{0}=0.4\), and \(i_{\text{no}}=50\).

(3) _Computational results_: The first experiment is carried out to check how well ACO-MC works on the instances with the NSF and SDH networks in set 1. In addition to the configuration determined above, we choose maximum number of iterations as the termination criteria of ACO-MC, that is, 10 000 iterations. Two versions of ACO-MC are implemented, that is, ACO-MC with local search (ACO-MC) and ACO-MC without local search (ACO-MC\({}_{w}\)). ACO-MC is implemented to solve each instance with 20 independent runs. Table 4 reports the

\begin{table}
\begin{tabular}{l c c c} \hline _Problem_ & _Nodes_ & _Commodities_ & _Arcs_ \\ \hline
1 & 40 & 1560 & 209 \\
2 & 40 & 1560 & 211 \\
3 & 40 & 1560 & 214 \\
4 & 50 & 2450 & 321 \\
5 & 50 & 2450 & 322 \\
6 & 50 & 2450 & 324 \\
7 & 50 & 2450 & 322 \\
8 & 55 & 2970 & 345 \\
9 & 55 & 2970 & 360 \\
10 & 55 & 2970 & 350 \\
11 & 55 & 2970 & 357 \\
12 & 60 & 3540 & 394 \\
13 & 60 & 3540 & 396 \\
14 & 60 & 3540 & 391 \\
15 & 60 & 3540 & 386 \\ \hline \end{tabular}
\end{table}
Table 2: Instance characteristics of SMNF-1 with larger WDM networks

\begin{table}
\begin{tabular}{l c c c c} \hline \(\beta\) & \(\gamma\) & \(\rho\) & \(p_{0}\) & \(i_{\text{no}}\) \\ \hline
0.75 & 1 & 0.1 & 0 & 50 \\
1 & 2 & 0.15 & 0.2 & 100 \\
2 & 3 & 0.20 & 0.4 & 150 \\
3 & 4 & 0.35 & 0.6 & 200 \\
5 & 5 & 0.40 & 0.7 & \\ \hline \end{tabular}
\end{table}
Table 3: The set of candidate configurations of ACO-MC

[MISSING_PAGE_FAIL:11]

found by CPLEX 11.0 and the CPU time, for the purpose of indicative comparison. Most of these larger instances cannot be solved optimally within acceptable time; and the running of CPLEX 11.0 always results in an out-of-error memory. So in Table 6, the 'Best' column as well as the next column only reports the corresponding integer solution and CPU time at the termination. Concerning ACO-MC, we report the gap between the lower bound and the best solution found by ACO-MC, as well as the measures used in previous experiments.

From the results in Table 6, we can draw the following conclusions:

1. The computational difficulty increases significantly with network size. In most of these instances, CPLEX 11.0 needs more than 1000 s to solve the LP relaxation problems. For instance, it even takes more than 6 h to get a lower bound of instance 12; and for instance 14 no optimal LP solution is returned within 13 h.
2. We also can observe that it gradually becomes impractical to solve these larger instances by exact algorithms. While trying to approach the optimal solutions, CPLEX 11.0 will without a doubt result in an out-of-memory error where the enumeration tree becomes so large that insufficient memory remains to solve a continuous LP sub-problem.
3. CPLEX 11.0 even requires a lot of computation time to get the first feasible integer solutions in most of these larger instances. For example, in instances 5 and 10, no

\begin{table}
\begin{tabular}{l c c c c c c c c c c c} \hline \hline _Problem_ & \multicolumn{6}{c}{_CPLEX_} & \multicolumn{6}{c}{_ACO-MC_} \\ \cline{2-13}  & _LB_ & _CPUs_ & _CPLEX-first_ & _CPUs_ & _Best_ & _CPUs_ & _Best_ & _Worst_ & _Avg._ & _Std._ & _CPUs_ & _GAP_ \\ \hline
1 & 3582 & 97.24 & 3693 & 960.33 & **3582a** & 1653.795  & 3582 & 3582 & 3582 & 0 & 37.23 & 0 \\
2 & 3527 & 107.05 & 3636 & 601.38 & **3527** & 1635.34 & **3527** & 3527 & 3527 & 0 & 11.77 & 0 \\
3 & 3225.99 & 122.56 & 3369 & 793.27 & **3226** & 2273.69 & **3226** & 3226 & 3226 & 0 & 14.83 & 0 \\
4 & 1928 & 6264.78 & \(-\)b  & \(-\) & \(-\) & 88 564.38 & 2315 & 2437 & 2392.6 & **48.94** & 969.72 & 16.72 \\
5 & 2239.50 & 2930.02 & \(-\) & \(-\) & \(-\) & 81 588.12 & 2404 & 2580 & 2479.45 & 52.82 & 890.63 & 6.84 \\
6 & **4144** & 278.04 & 4258 & 637.26 & **4144** & 1871.01 & **4144** & **4144** & **4144** & 0 & 17.87 & 0 \\
7 & 1643.01 & 5030.68 & 1868 & 5042.92 & 1868 & 72 010.45 & 1988 & 2069 & 2040.86 & 31.24 & 946.39 & 17.35 \\
8 & 4051 & 1271.87 & 4167 & 1281.91 & **4051** & 11 260.06 & **4051** & 4051 & 4051 & 0 & 259.83 & 0 \\
9 & 1957.88 & 7057.84 & 2137 & 9148.32 & 2092 & 49 363.5 & 2413 & 2531 & 2489.2 & 46.07 & 665.39 & 18.86 \\
10 & 2343.50 & 11877.84 & \(-\) & \(-\) & \(-\) & 11 907.81 & 2970 & 3135 & 3065.88 & 69.15 & 937.56 & 21.09 \\
11 & 2389.50 & 3421.82 & 2573 & 5549.65 & 2550 & 34 201.43 & 2641 & 2676 & 2655.2 & 38.25 & 547.05 & 9.52 \\
12 & 2176.12 & 22 502.41 & \(-\) & \(-\) & \(-\) & \(-\) & 22 537.72 & 2986 & 3203 & 3094.8 & 69.47 & 880.16 & 27.12 \\
13 & 5272 & 2835.63 & 5435 & 4839.38 & 5421 & 13 034.44 & 5272 & 5272 & 5272 & 0 & 585.44 & 0 \\
14 & \(+\)d  & \(+\)d \hline \end{tabular}
\end{table}

[FOOTNOTE:d]Footnote d: No optimal LP solution is returned when CPLEX 11.

[MISSING_PAGE_FAIL:13]

(2) _Parameter tuning_

In contrast to ACO-MC, ACO-MTC has fewer parameters. The previous _F-Race_ approach is also chosen to tune main algorithmic parameters. Table 8 gives the levels of related parameters to be tuned. The total number of the configurations considered in the preliminary experiment, therefore, is \(6\times 5\times 4=120\). Additionally, we set the value of \(n_{a}\) to 30 on small instances (Problems 1-10); and its value is set to 20 on large instances in order to reduce the memory required. We use the procedure in the previous section to generate 120 instances for parameter tuning. Each configuration is executed once on each of the 120 instances for 15 s. The objective values of the best solutions found in each of the experiments are stored in a two-dimensional \(120\times 120\) array.

After getting the results under different configurations, we implement the _F-Race_ approach to systematically tune ACO-MTC. The best configuration is output as follows: \(\kappa=0.10,\ p_{0}=0.15\), and \(i_{\text{no}}=200\).

(3) _Computational results_

ACO-MTC's performance is investigated on the generated instances in the following section. As the subgradient method for finding \(Z_{LR}\) has no convenient stopping criteria, we terminate it after it has performed 1000 iterations. Additionally, we just choose maximum number of allowed iterations as the termination criteria, and set its value equal to 10 000.

As the optimal solutions can be quickly found on small instances by exact algorithms, ACO-MTC's performance is first checked on small instances (Problems 1-10). CPLEX 11.0 is chosen to optimally solve small instances. With the best configuration, ACO-MTC is implemented with 20 independent runs on small instances. The test results are shown in Table 9. In the second and third columns, we give the optimal value (OPT) and corresponding CPU time (CPUs), which are required for CPLEX 11.0 to get the optimal solutions. In the following two columns, we report the lower bounds of Lagrangian relaxation (LB) and the relative gap based on the optimal solutions (GAP). The following five columns report the results of ACO-MTC. The measures considered are: the value of best-found solution (Best), average value of best solutions (Avg.), the standard deviation (Std.) of values of best-found solutions over 20 runs, and the CPU time, which is required for finding the best solution (CPUs). Finally, for each best solution, the relative gap is reported based on the optimal solutions given in the second column (GAP). The value of GAP is computed as 100 (Best-OPT)/Best.

Concerning the comparison of ACO-MTC to the optimal solutions and lower bounds, we can observe that ACO-MTC solves these small instances very efficiently; it has found all the optimal solutions. In comparison with exact algorithms, our proposed algorithm can obtain the optimal solutions more quickly and has solved most of these instances within 1 min. For example, on Problem 9, it takes 593 s for CPLEX 11.0 to find the optimal solution; however ACO-MTC has got the same solution within less than 10 s. The standard deviation column indicates that ACO-MTC is stable on these instances. Additionally, these results also show that the difference between the lower bound and the value of the optimal solution is slight; the average gap is 0.57%.

Owing to the existence of a large number of constraints and integer variables, it is very difficult to use exact algorithms to optimally solve large SMNF-2 instances within acceptable time. Therefore, we cannot use optimal solutions to evaluate ACO-MTC's performance on large instances. As an alternative choice, the efficiency of ACO-MTC is checked in comparison with the lower bound obtained by Lagrangian relaxation. We implement ACO-MTC with 20 trials with the best configuration on Problems 11, 12,..., and 25. Table 10 summarizes the results. On each instance, we report the value of best-found solutions (Best), the average value of the best solutions (Avg.), and the standard deviation (Std.) of the values of the best-found solutions over 20 runs. The CPU time required for ACO-MTC to find the best solutions, is also reported (CPUs). Furthermore, the subgradient procedure is implemented to get the LB. In the last column we provide the relative gap between the lower bound and the value of best solution obtained by ACO-MTC. The computational results in Table 10 allow us to draw the following conclusions:

1. The results show a clear advantage of ACO-MTC. It works well on all these large instances of SMNF-2 that can be indicated by the GAP column.
2. ACO-MTC is a computationally efficient algorithm for large instances, which can get better solutions very quickly within acceptable CPU time; for example, on Problem 12, it take less than 300 s for ACO-MTC to find the best solution with the value 10 828. In fact, this value is also the one of the optimal solution.
3. The best solution found by ACO-MTC gives an upper bound for SMNF-2. As the gaps between the lower bounds and the upper bounds are very small, it proves that ACO-MTC has found high-quality solutions on these large instances, which should be very close to the optimal solutions. At the same, it shows that \(Z_{LR}\) (=\(Z_{LP}\)) is fairly close to the optimal solution value.
4. The standard deviation column indicates that ACO-MTC is a robust method.

The computational results in both Tables 9 and 10 have shown that ACO-MTC is computationally a very efficient approach for solving SMNF-2, especially for large instances.

\begin{table}
\begin{tabular}{l c c} \(\kappa\) & \(p_{0}\) & \(i_{no}\) \\ \hline
0.1 & 0.10 & 50 \\
0.15 & 0.15 & 100 \\
0.20 & 0.30 & 150 \\
0.35 & 0.40 & 200 \\
0.40 & 0.50 & — \\
0.50 & — & — \\ \hline \end{tabular}
\end{table}
Table 8: The set of candidate configurations of ACO-MTC

[MISSING_PAGE_FAIL:15]

* Bandyopadhyay (2007) Bandyopadhyay S (2007). _Dissemination of Information in Optical Networks: From Technology to Algorithms_. Springer: Berlin.
* Barnhart et al. (2000) Barnhart C, Hane CA and Vance PH (2000). Using branch-and-price-and-cut to solve origin-destination integer multicommodity flow problems. _Opns Res_**48**: 318-326.
* Birattari (2005) Birattari M (2005). _The problem of tuning metaheuristics as seen from a machine learning perspective_. PhD thesis, Universite Libre De Bruxelles.
* Blum and Dorigo (2004) Blum C and Dorigo M (2004). The hyper-cube framework for ant colony optimization. _IEEE Trans Syst Man Cybern B_**34**: 1161-1172.
* Dorigo and Stutzle (2004) Dorigo M and Stutzle T (2004). _Ant Colony Optimization_. The MIT Press: Massachusetts.
* Dutta and Rouskas (2000) Dutta R and Rouskas G (2000). A survey of virtual topology design algorithms for wavelength routed optical networks. _Opt Network Mag_**1**: 73-89.
* Fisher (1981) Fisher M (1981). The Lagrangian relaxation method for solving integer programming problems. _Mngt Sci_**27**: 1-18.
* Fisher (1985) Fisher M (1985). An applications oriented guide to Lagrangian relaxation. _Interfaces_**15**(2): 10-21.
* Hu and Leida (2004) Hu JQ and Leida B (2004). Traffic grooming, routing, and wavelength assignment in optical wdm mesh networks. In: _Proceedings of 2004 IEEE INFOCOM_. IEEE Press: Piscataway: NJ, pp 495-501.
* Klingman and Stutz (1974) Klingman D, Napier A and Stutz J (1974). NETGEN: A program for generating large scale capacitated assignment transportation and minimum cost flow network problems. _Mngt Sci_**20**: 814-821.
* Ramaswami and Sivarajan (2002) Ramaswami R and Sivarajan KN (2002). _Optimal Networks: A Practical Perspective_. Morgan Kaufmann Publishers: San Francisco, CA.
* Rouskas and Dutta (2000) Rouskas G and Dutta R (2000). Design of logical topologies for wavelength routed networks. In: Sivalingam K and Subramanian S (eds). _Optical WDM Networks: Principles and Practice_. Kluwer Academic Publishers: Norwell: MA, pp 79-102.
* Sung and Rim (2004) Sung CS and Rim HC (2004). Receiver set partitioning and sequencing for multicasting traffic in a WDM lightwave single-hop network. _J Opl Res Soc_**6**: 630-639.