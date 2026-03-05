# Autonomous Vehicle Control

Data-driven predictive control for autonomous vehicle trajectory tracking using only input-output data — no explicit system model required.

## Overview

This project implements **Data-Enabled Predictive Control (DeePC)** based on behavioral systems theory (Coulson, Lygeros, Dorfler 2019). The controller uses pre-collected input-output data to predict and optimize future vehicle behaviour through Hankel matrix representations, completely bypassing the need for system identification or model derivation.

### What DeePC Does

Instead of building a mathematical model of the vehicle, DeePC:

1. **Collects data** from the real system using persistently exciting inputs
2. **Builds Hankel matrices** that implicitly encode the system dynamics
3. **Solves an optimization problem** at each time step to find the best control action
4. **Applies receding-horizon control** — only the first input is applied, then re-optimized

This makes it particularly suited for systems where accurate modeling is difficult or impractical.

### Vehicle Model (Simulation Only)

The simulator uses a nonlinear kinematic bicycle model:
- **State**: `[x, y, theta, v]` — position, heading, velocity
- **Inputs**: `[delta, a]` — steering angle, acceleration
- **Outputs**: `[x, y, v]` — position and velocity (heading is hidden)

The controller never sees the model — it only receives output measurements.

## Project Structure

```
autonomous-vehicle-control/
  deepc/
    deepc_controller.py    # Core DeePC optimizer (CVXPY parametric QP)
    hankel.py              # Block Hankel matrix construction
    regularization.py      # Persistent excitation checks
  simulator/
    vehicle_simulator.py   # Nonlinear kinematic bicycle model
  data/
    data_generation.py     # PRBS + multisine excitation signals
  main.py                  # End-to-end closed-loop simulation
  config.py                # All tunable parameters (dataclass)
  plotting.py              # Trajectory, tracking, and input plots
```

## Quick Start

```bash
# Install dependencies
uv sync

# Run the full experiment
uv run python main.py
```

This will:
1. Collect 200 samples of persistently exciting data
2. Build the DeePC controller (Hankel matrices + CVXPY problem)
3. Run 150 steps of closed-loop trajectory tracking
4. Save plots to the project root

## Configuration

All parameters are centralized in `config.py` via the `DeePCConfig` dataclass:

| Parameter | Default | Description |
|-----------|---------|-------------|
| `T_data` | 200 | Offline data samples |
| `Tini` | 3 | Past window length |
| `N` | 15 | Prediction horizon |
| `lambda_g` | 100.0 | Hankel weight regularization |
| `lambda_y` | 10000.0 | Output slack penalty |
| `Q_diag` | [10, 10, 1] | Output tracking weights [x, y, v] |
| `R_diag` | [0.1, 0.1] | Input cost weights [delta, a] |
| `sim_steps` | 150 | Closed-loop simulation steps |
| `Ts` | 0.1 | Sampling time [s] |

## Results

The simulation produces three plots:

- **trajectory_2d.png** — Vehicle path vs sinusoidal reference in the x-y plane
- **tracking_errors.png** — Per-output tracking over time (x, y, velocity)
- **control_inputs.png** — Steering and acceleration with constraint bounds

## Dependencies

- Python >= 3.13
- NumPy, SciPy, Matplotlib
- CVXPY (with CLARABEL solver)

## References

- J. Coulson, J. Lygeros, F. Dorfler. *Data-Enabled Predictive Control: In the Shallows of the DeePC*. European Journal of Control, 2019.
- J.C. Willems, P. Rapisarda, I. Markovsky, B.L.M. De Moor. *A note on persistency of excitation*. Systems & Control Letters, 2005.
