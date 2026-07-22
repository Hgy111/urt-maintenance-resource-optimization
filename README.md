# Replication Data and Computational Results

This repository provides the input data and individual computational results associated with the numerical experiments in the paper:

**A Two-Stage Optimization Framework for Maintenance Depot Location and Technician Allocation in Urban Rail Transit Systems**

The package is intended to support transparency, replicability, and verification of the reported computational experiments. It contains the Shanghai-Metro-derived test networks, aggregate station-level maintenance-demand probabilities, individual optimization results for each tested instance, and machine-readable representative resource-configuration solutions.

## Repository Structure

```text
network_data/
  small_network_edges.csv
  small_station_demand_probabilities.csv
  large_network_edges.csv
  large_station_demand_probabilities.csv

raw/
  optimization_results.csv
  exact_optimization_results.csv

processed/
  small_network_stage1_location_results.csv
  small_network_stage2_allocation_results.csv
  small_network_total_runtime_results.csv
  large_network_stage1_location_results.csv
  large_network_stage2_allocation_results.csv
  large_network_total_runtime_results.csv

representative_solutions/
  representative_solution_small_M5_T30.csv
  representative_solution_large_M10_T70.csv

README.md
data_dictionary.md
```

## Data Scope

The experiments use two urban rail transit networks derived from the Shanghai Metro setting:

* **Small-scale network:** 3 lines and 80 stations.

* **Large-scale network:** 9 lines and 245 stations.

Each network is represented as an undirected graph. Stations are anonymized as numeric station IDs. Each edge represents a direct inter-station connection, and each edge is assigned unit length in the computational experiments. Therefore, network distance is measured as the shortest-path number of inter-station segments.

The maintenance-demand inputs are aggregate station-level demand probabilities. These probabilities are calibrated using passenger-flow patterns and engineering judgment from maintenance practice. The available data do not contain task-type maintenance records or technician skill-roster information. Accordingly, the empirical implementation corresponds to the aggregate-capacity technician allocation model (P2-A), which is the aggregate-data special case of the generalized skill-compatible formulation (P2-B).

## Experiment Design

The numerical experiments evaluate a two-stage strategic resource-configuration framework:

1. **Stage 1: Depot location.** The model minimizes the Weighted Maintenance Response Distance (WMRD).

2. **Stage 2: Technician allocation.** The aggregate-capacity allocation model maximizes system reliability under a total workforce budget.

The tested parameter settings are:

* Small-scale network: `M = 1,...,10` depots and `T = 10,20,30,40` technicians.

* Large-scale network: `M = 1,...,10` depots and `T = 10,20,30,40,50,60,70` technicians.

The following computational methods are reported:

* **EA/RA:** complete enumeration when feasible, and random search when complete enumeration is computationally infeasible.

* **Heuristic:** the proposed two-stage heuristic procedure, consisting of the local search-based heuristic (LSBH) for Stage 1 and the marginal benefit-based greedy algorithm (MBBGA) for Stage 2.

* **Exact:** exact optimization using Gurobi for the Stage 1 ILP and the exact linear reformulation of the aggregate Stage 2 allocation model.

## File Descriptions

### network_data/

This folder contains graph and demand inputs:

* `small_network_edges.csv`, `large_network_edges.csv`: undirected network edges.

* `small_station_demand_probabilities.csv`, `large_station_demand_probabilities.csv`: aggregate station-level maintenance-demand probabilities.

### raw/

This folder contains individual instance-level computational results:

* `optimization_results.csv`: raw results for EA/RA and heuristic methods.

* `exact_optimization_results.csv`: exact benchmark results.

Each row corresponds to one tested instance defined by a network, a method, the number of depots `M`, and the number of technicians `T`.

### processed/

This folder contains cleaned and organized results used to produce the tables and comparisons in the manuscript:

* `*_stage1_location_results.csv`: depot-location outputs and Stage 1 runtimes.

* `*_stage2_allocation_results.csv`: technician-allocation outputs and Stage 2 runtimes.

* `*_total_runtime_results.csv`: total computational times for the complete two-stage procedure.

### representative_solutions/

This folder contains machine-readable versions of the representative resource-configuration schemes discussed in the manuscript:

* `representative_solution_small_M5_T30.csv`: representative solution for the small-scale network with 5 depots and 30 technicians.

* `representative_solution_large_M10_T70.csv`: representative solution for the large-scale network with 10 depots and 70 technicians.

Each row represents one selected depot, its covered stations, the number of allocated technicians, and the total aggregate demand in its service area.

## Computational Environment

The algorithms were implemented in Python. Exact benchmarks were obtained using Gurobi Optimizer v10.0.1. The experiments were executed on a server equipped with an Intel(R) Xeon(R) Gold 5218R CPU @ 2.10GHz.

All runtimes are reported in seconds. Small runtime differences may occur across computing environments.

## Notes on Interpretation

* `WMRD` denotes the Weighted Maintenance Response Distance. Smaller values indicate better response efficiency.

* `system_reliability` denotes the aggregate-capacity system reliability value. Larger values indicate better service-capacity adequacy.

* `stage1_selected_depots` is a list of selected depot station IDs in the Stage 1.

* `stage2_fixed_depots` is an ordered list of depots fixed for the Stage 2 technician-allocation problem based on the optimal Stage 1 depot-location solution.

* `technician_allocation` is ordered consistently with `stage2_fixed_depots`.

* `covered_stations` is a list of demand stations assigned to the corresponding depot.

* The reported empirical results are based on the aggregate-capacity formulation (P2-A), because the case-study data do not contain task-specific maintenance records or technician skill profiles.

For detailed definitions of all columns, see `data_dictionary.md`.
