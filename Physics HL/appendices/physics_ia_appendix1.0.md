### Appendix 1.0
#### Mathematical Formula
Horizontal displacement from the equilibrium position: $x=\sin(\theta)$.
Vertical displacement from the floor:  $y=1-\cos(\theta)$.
#### Python Script
This is a script I wrote to calculate the displacements for the brick tower.
```python
import math
import pandas as pd

def calculate_pendulum_displacements(total_height: float, string_length: float, angles_deg: list[int]):
  results = []

  for theta_deg in angles_deg:
    theta_rad = math.radians(theta_deg)
      
    x = string_length * math.sin(theta_rad)
    delta_h = string_length * (1 - math.cos(theta_rad))
      
    height_from_floor = total_height - string_length * math.cos(theta_rad)

    x_mm = round(x * 1000)
    delta_h_mm = round(delta_h * 1000)
    height_floor_mm = round(height_from_floor * 1000)
    results.append((theta_deg, x_mm, delta_h_mm, height_floor_mm))
  
  df = pd.DataFrame(results, columns=[
    "Angle (°)",
    "Horizontal Displacement x (mm)",
    "Vertical Rise Δh (mm)",
    "Height from Floor (mm)",
  ])
  
  print(df.to_string(index=False))
  print("\nList of (angle, x_mm, delta_h_mm, height_from_floor_mm):")
  print(results)   

calculate_pendulum_displacements(
    total_height=1.5,
    string_length=1.0,
    angles_deg=[10, 15, 20, 25, 30, 35, 40, 45],
)
```