# Pass Lane & Defensive Pressure Analyzer

This extension of Abdullah Tarek's Github's football_analysis repository introduces two new analytical modules for football/ soccer match analysis. These components visualize passing options and quantify the defensive pressure on players in possession of the ball.

## Key Components

### Pass Lane Analyzer
This module identifies and visualizes the most effective passing opportunities during gameplay by:
- Detecting available passing channels between teammates
- Visualizing the width and safety margin of each passing lane
- Analyzing defender positioning to determine lane viability
- Providing clear visual feedback with "Pass Lane: OPEN" or "Pass Lane: CLOSED" indicators

### Defensive Pressure Index (DPI)
This analytical tool measures the level of defensive pressure on the ball carrier using a comprehensive 0-10 scale:
- 2.5 points are assigned when no viable passing lanes exist
- Each defender within 20 meters contributes 1.0 point (maximum of 3 defenders counted)
- Proximity increases pressure contribution:
  - Defenders within 10 meters: 1.5 points each
  - Defenders within 5 meters: 2.5 points each

The resulting score provides a measure pressure intensity, showing how defensive pressure evolves throughout the match.

## Integration Guide

Follow the proceeding steps to integrate the new modules into the football_analysis repository.

### 1. File Placement
Add these module files to the root directory of the football_analysis repository:
- `pass_lane_analyzer.py`
- `defensive_pressure_index.py`

### 2. Import Statements
In the `main.py` file, add the following import statements with your other dependencies:

```python

from pass_lane_analyzer import PassLaneAnalyzer
from defensive_pressure_index import DefensivePressureIndex
```

### 3. Module Initialization
Initialize the analyzers where you set up your other components:

```python
pass_lane_analyzer = PassLaneAnalyzer()
defensive_pressure_index = DefensivePressureIndex(debug=True)  
```

### 4. Data Processing
After team assignment and ball possession detection, implement the analysis pipeline:

```python
print(f"Batch {batch_num + 1}: Analyzing passing lanes...")
batch_pass_lanes = pass_lane_analyzer.calculate_pass_lanes(batch_tracks, batch_team_ball_control)
print(f"Batch {batch_num + 1}: Pass lane analysis complete")

print(f"Batch {batch_num + 1}: Computing defensive pressure indices...")
batch_pressure_data = []

for frame_num in range(len(batch_frames)):
    pressure_data = defensive_pressure_index.calculate_pressure_index(
        batch_tracks, batch_team_ball_control, batch_pass_lanes, frame_num)
    batch_pressure_data.append(pressure_data)
```

### 5. Visualization Implementation
Lastly, incorporate the visualization components into the frame rendering pipeline:

```python
for frame_num, frame in enumerate(batch_output_frames):

    if frame_num < len(batch_pass_lanes):
        frame = pass_lane_analyzer.draw_pass_lanes(
            frame, batch_pass_lanes[frame_num], frame_num)
            
    if frame_num < len(batch_pressure_data):
        frame = defensive_pressure_index.draw_pressure_index(
            frame, batch_pressure_data[frame_num])
            
    batch_output_frames[frame_num] = frame
```

## Additional Notes

### System Dependencies
These modules require the following components from the base implementation:
- TeamAssigner (for team affiliation determination)
- PlayerBallAssigner (for ball possession detection)
- ViewTransformer (for coordinate space conversion)
- SpeedAndDistanceEstimator (for accurate distance measurements)

### Configuration Parameters
The following parameters can be adjusted to customize analysis sensitivity:

- `pass_lane_analyzer.quality_threshold`: Minimum quality threshold for viable passing lanes (default: 0.3)
- `defensive_pressure_index.max_pressure_score`: Maximum attainable pressure score (default: 10)
- `defensive_pressure_index.defender_count_limit`: Maximum number of defenders factored into pressure calculation (default: 3)
