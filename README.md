# Building a Decision-Based Digital Twin of Rodri

This project builds a simple, explainable **decision-based digital twin** of Rodri using StatsBomb Open Data.

The goal is not to create a black-box prediction model, but to understand and simulate how Rodri makes passing decisions depending on the game context.

The digital twin is based on four core signals:

- field zone
- pressure
- pass direction
- pass progression

## Project Overview

Rodri is one of the most influential midfielders in modern football because of his ability to control possession, progress play, and make high-quality decisions under pressure.

This notebook analyzes his event data across 24 selected matches and creates a probabilistic model that simulates likely passing behavior in different contexts.

The project answers questions such as:

- Does Rodri pass forward more often in deeper zones?
- How does his progression change by field zone?
- Does pressure change his decision-making?
- Can we simulate a Rodri-like passing decision given zone and pressure?

## Dataset

The project uses **StatsBomb Open Data** event files in JSON format.

Only 24 selected matches where Rodri appears are loaded, instead of loading the full dataset. This keeps the notebook lighter, faster, and less memory-intensive.

Main event features used:

- player
- event type
- location
- pass end location
- under pressure
- match id

## Methodology

The workflow is structured as follows:

1. Load selected StatsBomb event files
2. Extract player and event names from nested JSON fields
3. Filter events for Rodri
4. Extract pass start and end coordinates
5. Assign each pass to a field zone
6. Classify pass direction
7. Calculate pass progression
8. Analyze behavior under pressure
9. Build a probabilistic decision model
10. Simulate Rodri-like passing decisions

## Field Zones

StatsBomb uses a 120x80 pitch coordinate system.

Passes are assigned to three zones based on the starting x-coordinate:

- Defensive zone: x < 40
- Central zone: 40 <= x < 80
- Offensive zone: x >= 80

## Pass Direction

Pass direction is classified using the x-coordinate progression:

- Forward: end_x - start_x > 5
- Backward: end_x - start_x < -5
- Lateral: between -5 and +5

The 5-meter threshold avoids over-interpreting small coordinate differences.

## Key Results

Across the selected 24-match sample:

- Rodri events: 5,245
- Valid Rodri passes analyzed: 1,695
- Average pass progression: +3.65 meters

Pass direction distribution:

- Forward: 43.7%
- Lateral: 34.9%
- Backward: 21.4%

Forward pass share by zone:

- Defensive zone: 53.1%
- Central zone: 42.1%
- Offensive zone: 37.4%

Average progression by zone:

- Defensive zone: +6.18 meters
- Central zone: +3.33 meters
- Offensive zone: +1.22 meters

Defensive-zone progression under pressure:

- No pressure: +5.88 meters
- Under pressure: +7.83 meters

## Interpretation

The results suggest that Rodri's passing behavior is context-dependent.

He progresses the ball more from deeper areas, where there is more pitch available ahead of him. In offensive zones, average progression naturally decreases because the remaining space is smaller.

The defensive-zone pressure result is especially interesting: Rodri gains more meters on average when passing under pressure in his defensive zone. This does not necessarily mean pressure makes passing easier. A more realistic interpretation is that pressure situations often force or invite more vertical escape passes.

This supports the idea of a decision-based digital twin: Rodri's behavior should be modeled by context, not only by global averages.

## Digital Twin Simulation

The simulation model estimates, for each combination of field zone and pressure state:

- probability of a forward, lateral, or backward pass
- average progression for each pass direction
- sample size supporting the estimate

Given a zone and pressure state, the model can simulate likely Rodri-like passing decisions.

Example scenario:

```python
simulate_player_behavior(
    model=model,
    zone="Defensive",
    under_pressure=True,
    n=20,
    random_state=7,
)
```

The model includes fallback logic for missing combinations, so it does not fail silently when a specific context has insufficient data.

## Notebook Structure

The final notebook is organized as:

1. Introduction
2. Dataset
3. Data Loading
4. Data Cleaning
5. Decision Profile
6. Spatial Analysis
7. Passing Direction
8. Progression Analysis
9. Pressure Analysis
10. Digital Twin Simulation
11. Key Insights
12. Limitations
13. Future Improvements

## Code Quality Improvements

The notebook was refactored to be cleaner and more portfolio-ready.

Main improvements:

- modular functions
- clear markdown sections
- safer filtering with `.copy()`
- no `SettingWithCopyWarning`
- no risky `fillna(...).astype(bool)` pattern
- selected-match loading to reduce memory usage
- error handling for missing files and columns
- readable charts with titles, labels, and bar values
- simple dependencies suitable for a junior data analyst project

## Technologies Used

- Python
- pandas
- numpy
- matplotlib
- Jupyter Notebook
- StatsBomb Open Data

## Limitations

This is an explainable portfolio project, not a full professional tracking-based model.

Current limitations:

- no tracking data
- pressure is treated as a binary event flag
- opponent location is not modeled
- body orientation is not included
- pass difficulty is not directly estimated
- tactical phase is simplified
- the model simulates decision style, not pass success or tactical optimality

## Future Improvements

Possible extensions:

- include pass outcome
- estimate pass completion probability
- compare Rodri with other midfielders
- add pass length and pass angle
- separate open play from set pieces
- model receiver zones
- create a dashboard version
- move functions into a reusable Python module

## Project Goal

The purpose of this project is to show how football event data can be transformed into an interpretable behavioral model.

It combines football analysis, data cleaning, spatial reasoning, and probabilistic simulation in a way that is understandable, reproducible, and suitable for a data analytics portfolio.
