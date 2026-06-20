# kanban-production-system-simulink
SimEvents-based analysis and performance improvement of a Kanban production system under seasonal demand.
# Kanban Production System Simulation and Performance Optimization

![MATLAB](https://img.shields.io/badge/MATLAB-Live%20Script-orange)
![Simulink](https://img.shields.io/badge/Simulink-Discrete%20Event%20Simulation-blue)
![SimEvents](https://img.shields.io/badge/SimEvents-Kanban%20Model-green)
![Lean](https://img.shields.io/badge/Lean-DMAIC%20%7C%20Takt%20Time-lightgrey)

A simulation-based analysis of a **Kanban-controlled production system** created with **MATLAB, Simulink, and SimEvents**. The project studies how seasonal demand, work-in-process limits, inventory availability, and Part B production capacity affect completed and dropped customer orders.

<p align="center">
  <img src="assets/kanban_system_flow.png" alt="Kanban system flow" width="900">
</p>

## Project objective

The objective was to identify the cause of lost sales during peak demand and evaluate parameter-level improvements without redesigning the internal Simulink model.

The analysis focuses on four questions:

1. Why do dropped orders increase during peak season?
2. Which component is the main system bottleneck?
3. Can additional WIP Kanbans eliminate the shortage?
4. Can shorter Part B production time or seasonal takt-time tuning improve flow?

## System architecture

The model represents a pull-based manufacturing system containing:

- seasonal production-order generation;
- raw-material suppliers;
- Part A and Part B suppliers;
- withdrawal and WIP Kanban resource pools;
- inventory buffers;
- a final assembly line; and
- completed-order and dropped-order outputs.

One final product requires **one Part A and one Part B**. Production and withdrawal activities are controlled through finite Kanban resources.

## Key performance indicators

The simulation monitors:

- Part A and Part B withdrawal Kanbans in use;
- Part A and Part B work in process;
- Part A and Part B storage levels;
- products in final assembly;
- orders generated per day;
- completed orders; and
- dropped orders.

## Bottleneck analysis

Peak demand reaches **10 products per day**. Because every product requires one Part B, the system must also provide **10 Part B units per day**.

The baseline configuration uses:

- **12 Part B WIP Kanbans**; and
- **1.5 days** to produce one Part B.

Therefore, the theoretical maximum Part B production rate is:

```text
Part B capacity = WIP Kanbans / production delay
                = 12 / 1.5
                = 8 parts per day
```

The resulting **2-part-per-day capacity deficit** explains inventory starvation at the assembly line and the sharp increase in dropped orders during peak demand.

<p align="center">
  <img src="assets/part_b_capacity_comparison.png" alt="Part B capacity comparison" width="760">
</p>

## Improvement scenarios

### Solution 1 - Increase Part B WIP Kanbans

The number of Part B WIP Kanbans is increased from **12 to 15**, while the production delay remains **1.5 days**.

```text
New capacity = 15 / 1.5 = 10 parts per day
```

This configuration matches peak demand by allowing more Part B units to be processed in parallel. The submitted report records zero dropped orders, but the higher WIP level may increase inventory and storage exposure.

### Solution 2 - Reduce Part B production delay

The Part B production delay is reduced from **1.5 days to 1.2 days**, while the WIP limit remains **12 Kanbans**.

```text
New capacity = 12 / 1.2 = 10 parts per day
```

This configuration also matches peak demand. It improves throughput without increasing WIP, although achieving the shorter cycle time may require process redesign, training, automation, or additional equipment in a real factory.

### Solution 3 - Seasonal takt-time tuning

The year is divided into four demand zones. Each zone receives a different Part B WIP limit and production-delay setting.

| Zone | WIP Kanbans | Delay (days) | Days | Daily demand | Reported dropped | Reported completed |
|---|---:|---:|---:|---:|---:|---:|
| Jan-Feb | 9 | 1.6 | 60 | 2 | 4 | 116 |
| Mar-Jun | 11 | 1.5 | 120 | 6 | 4 | 716 |
| Jul-Sep | 13 | 1.4 | 90 | 10 | 4 | 896 |
| Oct-Dec | 10 | 1.6 | 90 | 6 | 4 | 536 |

Across the four reported zones, this corresponds to **2,264 completed orders out of 2,280**, or approximately **99.3% service**.

<p align="center">
  <img src="assets/seasonal_service_level.png" alt="Seasonal service level" width="760">
</p>

## Reported results

| Configuration | Part B setting | Theoretical capacity | Reported dropped orders | Main observation |
|---|---|---:|---:|---|
| Baseline | 12 Kanbans, 1.5-day delay | 8/day | 240+ | Part B starvation during peak demand |
| Solution 1 | 15 Kanbans, 1.5-day delay | 10/day | 0 | Smooth flow with higher WIP |
| Solution 2 | 12 Kanbans, 1.2-day delay | 10/day | 0 | Faster response without additional WIP |
| Solution 3 | Seasonal settings | Dynamic | 4 per zone | Adaptive response across the year |

> **Reproducibility note:** The capacity calculations were independently checked. The full SimEvents model was not included in the submitted files, so the reported simulation KPIs are presented as documented results rather than independently rerun results. See [`docs/REPRODUCIBILITY_NOTES.md`](docs/REPRODUCIBILITY_NOTES.md).

## Lean and Six Sigma interpretation

The project follows the logic of DMAIC:

- **Define:** Dropped orders during seasonal peaks were identified as the main problem.
- **Measure:** Demand, WIP, storage, final assembly, completed orders, and dropped orders were monitored.
- **Analyze:** Part B capacity was identified as the system constraint.
- **Improve:** Three parameter-level countermeasures were evaluated.
- **Control:** Scopes and KPI tracking provide a basis for future performance monitoring.

The main wastes observed in the baseline system were waiting at final assembly, inventory instability, and interrupted flow caused by Part B shortages.

## Repository structure

```text
kanban-production-system-simulink/
├── assets/      # README diagrams and result charts
├── data/        # Scenario and seasonal configuration tables
├── docs/        # Full report and reproducibility notes
├── notebooks/   # Original MATLAB Live Script
├── results/     # Checked calculations
├── src/         # Clean MATLAB scripts
├── CHANGELOG.md
├── GITHUB_UPDATE_GUIDE.md
└── README.md
```

## How to run

### Requirements

- MATLAB
- Simulink
- SimEvents
- access to the `seKanbanSystem` example model

### Steps

1. Clone or download this repository.
2. Open MATLAB and set the repository as the current folder.
3. Confirm that `seKanbanSystem` can be loaded.
4. Run `src/capacity_analysis.m` to reproduce the theoretical calculations.
5. Run `src/run_baseline_and_improvements.m` to simulate the baseline and Solutions 1 and 2.
6. Run `src/run_seasonal_takt_tuning.m` to apply the four seasonal settings.
7. Review the Simulink scopes for demand, Part B WIP, Part B storage, final assembly, and dropped orders.

## Files

- [MATLAB Live Script](notebooks/kanban_production_system_analysis.mlx)
- [Full project report](docs/kanban_production_system_report.pdf)
- [Capacity analysis script](src/capacity_analysis.m)
- [Baseline and improvements script](src/run_baseline_and_improvements.m)
- [Seasonal tuning script](src/run_seasonal_takt_tuning.m)

## Future work

- Log simulation outputs automatically instead of relying on visual scope interpretation.
- Add holding-cost, shortage-cost, and lost-sales penalties.
- Tune both Part A and Part B jointly.
- Connect demand forecasting to dynamic Kanban settings.
- Develop a digital-twin dashboard for real-time KPI monitoring.
- Compare parameter configurations using designed experiments or optimization algorithms.

## Author

**Balaji Sai Tolapu**  
M.S. Industrial and Systems Engineering  
Rutgers University - New Brunswick
