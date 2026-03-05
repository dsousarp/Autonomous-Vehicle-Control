# Backlog

Tracking planned features, improvements, and known issues.

## Completed

- [x] **Core DeePC implementation** — Hankel matrices, parametric CVXPY QP, receding horizon loop
- [x] **Nonlinear vehicle simulator** — Kinematic bicycle model with configurable dimensions
- [x] **Data collection** — PRBS + multisine + uniform random excitation with PE verification
- [x] **Sinusoidal reference tracking** — Closed-loop trajectory following with 100% optimal solves
- [x] **Plotting** — 2D trajectory, per-output tracking errors, control inputs with bounds
- [x] **Centralized config** — All parameters in a single dataclass

## In Progress

_(nothing currently)_

## Planned

### Performance
- [ ] Reduce per-solve time (currently ~0.5s) — investigate OSQP tuning or problem reformulation
- [ ] Profile and optimize Hankel matrix construction for large T_data
- [ ] Explore warm-starting strategies between consecutive solves

### Features
- [ ] Process and measurement noise injection (configurable)
- [ ] Output constraints (e.g., speed limits, lane boundaries)
- [ ] Rate-of-change constraints on inputs (delta_u penalties)
- [ ] Waypoint-based reference trajectories (not just sinusoidal)
- [ ] Multiple reference trajectory types (lane change, figure-8, parking)
- [ ] Online data update — extend Hankel matrices with new observations
- [ ] Adaptive regularization (tune lambda_g, lambda_y online)

### Robustness
- [ ] Robust DeePC formulation with distributionally robust constraints
- [ ] Investigate effect of data length T_data on tracking quality
- [ ] Sensitivity analysis for Tini and N horizon parameters
- [ ] Handle solver failures gracefully (fallback control law)

### Extensions
- [ ] MIMO support validation — test with higher-dimensional systems
- [ ] Comparison with MPC (using identified model) as baseline
- [ ] Nonlinear DeePC variants
- [ ] Real-time plotting / live simulation visualization
- [ ] Integration with ROS2 or CARLA simulator

### Code Quality
- [ ] Unit tests for Hankel construction, PE check, and simulator
- [ ] Integration test: run full simulation and assert tracking error bounds
- [ ] Type checking with mypy
- [ ] CI pipeline (GitHub Actions)
- [ ] Logging instead of print statements

## Known Issues

- PE condition may fail with T_data < 100 due to insufficient spectral content
- First few control steps show larger tracking error (warm-up transient from zero-input initialization)
- CLARABEL solver does not support `max_iter` parameter — solver settings passed via config are limited

## Changelog

### v0.1.0 (2026-03-05)
- Initial implementation of DeePC for autonomous vehicle trajectory tracking
- Kinematic bicycle model simulator
- PRBS + multisine + uniform random data collection
- CVXPY parametric QP with CLARABEL solver and SCS fallback
- Sinusoidal reference trajectory tracking (150 steps, 100% optimal)
- Three output plots: trajectory, tracking errors, control inputs
