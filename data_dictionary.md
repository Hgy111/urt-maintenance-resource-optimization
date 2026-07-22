# Data Dictionary

This file defines the variables and columns used in the replication data and computational-result files.

## Common Identifiers

| Column              | Description                                                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `network`           | Network identifier. `3_lines_80_stations` denotes the small-scale network; `9_lines_245_stations` denotes the large-scale network. |
| `network_size`      | Number of stations in the network.                                                                                                 |
| `method`            | Computational method. See "Method Labels" below.                                                                                   |
| `algorithm_variant` | More detailed method label used in processed files.                                                                                |
| `M`                 | Number of maintenance depots to be located.                                                                                        |
| `T`                 | Total number of technicians available for allocation.                                                                              |

## Method Labels

| Label                | Description                                                                                                                                                                               |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enumeration`        | Complete enumeration benchmark used when the feasible search space is computationally tractable.                                                                                          |
| `random`             | Random search benchmark used when complete enumeration is computationally infeasible.                                                                                                     |
| `random/enumeration` | Raw-record label for a combined EA/RA benchmark: random search is used for the Stage 1 depot-location problem, whereas enumeration is used for the Stage 2 technician-allocation problem. |
| `ea_ra`              | Processed label for the enumeration/random-search benchmark.                                                                                                                              |
| `heuristic`          | Proposed heuristic method. It uses LSBH for depot location and MBBGA for aggregate technician allocation.                                                                                 |
| `exact`              | Exact benchmark solved by Gurobi. Stage 1 is solved as an ILP; Stage 2 uses the exact linear reformulation of the aggregate allocation model.                                             |

## network_data/*_network_edges.csv

| Column      | Description                                       |
| ----------- | ------------------------------------------------- |
| `network`   | Network identifier.                               |
| `edge_id`   | Sequential edge identifier.                       |
| `station_u` | First endpoint station ID of an undirected edge.  |
| `station_v` | Second endpoint station ID of an undirected edge. |

Each edge represents a direct inter-station connection. In the experiments, each edge has unit length, so shortest-path distance equals the number of inter-station segments.

## network_data/*_station_demand_probabilities.csv

| Column                     | Description                                                                                                                 |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| `network`                  | Network identifier.                                                                                                         |
| `station_id`               | Station ID.                                                                                                                 |
| `demand_probability`&#x20; | Aggregate probability that station `station_id` generates a maintenance demand during the representative planning interval. |

The demand probabilities are aggregate station-level inputs. They are not task-type-specific probabilities.

## raw/optimization_results.csv

| Column                        | Description                                                                                                                                                                                           |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `network`                     | Network identifier.                                                                                                                                                                                   |
| `method`                      | Computational method, including enumeration, random search, mixed EA/RA, or heuristic.                                                                                                                |
| `M`                           | Number of depots.                                                                                                                                                                                     |
| `T`                           | Number of technicians.                                                                                                                                                                                |
| `stage1_runtime_seconds`      | Runtime of the Stage 1 depot-location procedure, in seconds.                                                                                                                                          |
| `stage1_selected_depots`      | List of selected depot station IDs in the Stage 1.                                                                                                                                                    |
| `WMRD`                        | Weighted Maintenance Response Distance obtained in Stage 1.                                                                                                                                           |
| `stage2_fixed_depots`         | Ordered list of depots fixed for the Stage 2 technician-allocation problem based on the optimal Stage 1 depot-location solution. Its order corresponds element by element to `technician_allocation`. |
| `stage2_runtime_seconds`      | Runtime of the Stage 2 technician-allocation procedure, in seconds.                                                                                                                                   |
| `system_reliability`          | System reliability obtained in Stage 2 under the aggregate-capacity formulation.                                                                                                                      |
| `technician_allocation`&#x20; | List of technician allocations ordered consistently with `stage2_fixed_depots`.                                                                                                                       |
| `total_runtime_seconds`       | Sum of Stage 1 and Stage 2 runtimes, in seconds.                                                                                                                                                      |

## raw/exact_optimization_results.csv

This file uses the same column definitions as `raw/optimization_results.csv`, but contains only exact benchmark results.

## processed/*_stage1_location_results.csv

| Column                        | Description                                                                                   |
| ----------------------------- | --------------------------------------------------------------------------------------------- |
| `network`                     | Network identifier.                                                                           |
| `method`                      | Processed method label: `ea_ra`, `heuristic`, or `exact`.                                     |
| `algorithm_variant`           | Detailed method label, such as `enumeration`, `random`, `heuristic`, or `exact`.              |
| `M`                           | Number of depots.                                                                             |
| `selected_depots`             | List of selected depot station IDs.                                                           |
| `WMRD`                        | Weighted Maintenance Response Distance.                                                       |
| `stage1_runtime_seconds_mean` | Mean Stage 1 runtime over the repeated `T` settings associated with the same `M`, in seconds. |
| `stage1_runtime_seconds_min`  | Minimum observed Stage 1 runtime for the same `M`, in seconds.                                |
| `stage1_runtime_seconds_max`  | Maximum observed Stage 1 runtime for the same `M`, in seconds.                                |
| `runtime_observations`        | Number of runtime observations summarized for the corresponding `network`, `method`, and `M`. |

Stage 1 location decisions depend on `M` and the network, but not on the technician budget `T`. The runtime summary is therefore aggregated over the tested `T` settings for a given `M`.

## processed/*_stage2_allocation_results.csv

| Column                   | Description                                                                                                                                                                                           |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `network`                | Network identifier.                                                                                                                                                                                   |
| `method`                 | Processed method label: `ea_ra`, `heuristic`, or `exact`.                                                                                                                                             |
| `algorithm_variant`      | Detailed method label.                                                                                                                                                                                |
| `M`                      | Number of depots.                                                                                                                                                                                     |
| `T`                      | Number of technicians.                                                                                                                                                                                |
| `stage2_fixed_depots`    | Ordered list of depots fixed for the Stage 2 technician-allocation problem based on the optimal Stage 1 depot-location solution. Its order corresponds element by element to `technician_allocation`. |
| `technician_allocation`  | List of technician allocations ordered consistently with the depot set used in the corresponding Stage 2 experiment.                                                                                  |
| `system_reliability`     | System reliability achieved by the allocation method under the aggregate-capacity formulation.                                                                                                        |
| `stage2_runtime_seconds` | Runtime of the Stage 2 allocation procedure, in seconds.                                                                                                                                              |

## processed/*_total_runtime_results.csv

| Column                   | Description                                           |
| ------------------------ | ----------------------------------------------------- |
| `network`                | Network identifier.                                   |
| `method`                 | Processed method label.                               |
| `algorithm_variant`      | Detailed method label.                                |
| `M`                      | Number of depots.                                     |
| `T`                      | Number of technicians.                                |
| `stage1_runtime_seconds` | Stage 1 runtime, in seconds.                          |
| `stage2_runtime_seconds` | Stage 2 runtime, in seconds.                          |
| `total_runtime_seconds`  | Total runtime of the two-stage procedure, in seconds. |

## representative_solutions/representative_solution_small_M5_T30.csv

| Column                     | Description                                                                         |
| :------------------------- | :---------------------------------------------------------------------------------- |
| `network_size`             | Number of stations in the network.                                                  |
| `M`                        | Number of selected depots in the representative scenario.                           |
| `T`                        | Total number of technicians in the representative scenario.                         |
| `depot_id`                 | Selected depot station ID.                                                          |
| `coverage_size`            | Number of demand stations assigned to the depot.                                    |
| `covered_stations`         | List of station IDs assigned to the depot.                                          |
| `allocated_technicians`    | Number of technicians allocated to the depot.                                       |
| `total_demand_in_coverage` | Sum of aggregate station-level demand probabilities over the depot's coverage area. |

## representative_solutions/representative_solution_large_M10_T70.csv

This file uses the same column definitions as `representative_solutions/representative_solution_small_M5_T30.csv`.

## Units and Precision

| Quantity              | Unit or Scale                                        |
| --------------------- | ---------------------------------------------------- |
| `WMRD`                | Weighted inter-station-segment distance.             |
| Runtime columns       | Seconds.                                             |
| `demand_probability`  | Probability in the representative planning interval. |
| `system_reliability`  | Dimensionless reliability value in `[0,1]`.          |
| Station and depot IDs | Anonymized numeric identifiers.                      |

List-valued columns such as `selected_depots`, `technician_allocation`, and `covered_stations` are stored as text using square brackets.
