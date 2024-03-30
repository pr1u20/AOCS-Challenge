# AOCS Challenge - Competition Guide

Welcome to the AOCS Challenge! This guide provides all the necessary steps to participate in the competition, from setting up your environment to submitting your control strategy. 

Please find the competition portal [here](https://aocs-challenge-leaderboard.onrender.com).

## Setup Instructions

Before you start, ensure you have the required package installed:

### Install Required Packages

The `pyaocs` package is specifically developed for this competition and needs to be installed:

```bash
pip install pyaocs
```

For more details on the `pyaocs` package, visit its PyPI page [here](https://pypi.org/project/pyaocs/).

### Importing Packages

In your solution, make sure to import the necessary libraries:

```python
import numpy as np
from pyaocs import parameters as param
from pyaocs.simulation import digital_twin
import pickle
```

## Creating Your Control Class

Participants need to create a `Control` class that includes the following methods:

### `__init__` Method

This initializer method is where you should initialize variables if required.

### `compute` Method

This method takes an `obs` object as input, structured as follows:

```python
obs = {
    "current_position": np.array(position),
    "current_orientation": np.array(orientation),
    "target_position": np.array(target_position),
    "target_orientation": np.array(target_orientation),
    "current_velocity": np.array(velocity),
    "parameters": np.array(parameters)
}
```

It must return an `action` object, structured as `np.array([F1, 0, 0, 0, δ1, 0])`.

### `plot` Method

This method can be left as `pass` if no plotting is required. However, any plotting done here will automatically display at the end of the simulation and can be useful for debugging purposes.

#### Example Control Class:

Here's a simple example of a control class:

```python
class SimpleTestControl:
    def __init__(self):
        pass

    def compute(self, obs):
        F1 = 1 # Thruster ON
        δ1 = 2 # Angle
        return np.array([F1, 0, 0, 0, δ1, 0])

    def plot(self):
        pass
```

The thruster is fired at everytimestep with the angle at 2 degrees. Go through `example_submission.ipynb` for more details.

## Running the Simulation

To run the simulation with your control strategy:

```python
strategy = SimpleTestControl()

env, _, _ = digital_twin.run(strategy, 
                             render=True, 
                             real_time=False, 
                             use_disturbances=False,
                             noise=False)
```

## Scoring

The competition features three scoring categories based on different simulation conditions:

- **Ideal World:** No disturbances or sensor noise.
- **Disturbances Added:** External factors causing variations.
- **Disturbances and Noise Added:** Both external factors and sensor noise present.

### Scoring Function Example:

```python
def scoring(env):
    position_error = np.mean(np.abs((env.actual_positions - env.target_positions)))
    firing_time = np.sum(env.F1s) * 1 / param.sample_rate

    score = 400*position_error + firing_time
    return score
```

## Submission

Serialize and submit your control class as a pickle file:

```python
with open('submissions/example_submission.pickle', 'wb') as file:
    pickle.dump(strategy, file)
```

Now you can go to the [submission portal](https://aocs-challenge-leaderboard.onrender.com) sign up and upload your `submission.pickle`.

### Verifying Submission:

Ensure the class was correctly serialized by loading and testing it:

```python
with open('submissions/example_submission.pickle', 'rb') as file:
    strategy = pickle.load(file)
```

## Requirements for Submission

To ensure the successful participation in the AOCS Challenge, please adhere to the following requirements for your submission:

- **Python Version**: Make sure that your development environment is running Python version `3.11`. This ensures compatibility with all dependencies and features used in the competition.

- **pyaocs Package Version**: Your environment must have the `pyaocs` package version `0.0.5` installed. You can check your package version using `pip show pyaocs` and install the specific version with:

    ```bash
    pip install pyaocs==0.0.5
    ```

- **Control Class Methods**: Your submission must include a `Control` class that implements the following methods:
    - `__init__`: Initializer for any required variables.
    - `compute`: Takes an observation dictionary (`obs`) and returns an action array.
    - `plot`: Optional for any debugging plots. Can be left empty with `pass`.

- **Compute Method Output**: The output of the `compute` method must be in the specific format of an action array:

    ```python
    action = np.array([F1, 0, 0, 0, δ1, 0])
    ```

    Where `F1` is the thruster state (0 for OFF, 1 for ON) and `δ1` represents the angle parallel to the microgravity table, ranging from -16 to 16 degrees.

- **Evaluation under Disturbances and Noise**: To evaluate the robustness of your algorithm, make sure to set `use_disturbances` and/or `noise` to `True` when testing your strategy. This simulates more realistic conditions that your control system might face.

If this requirements are not followed the submission might not be succesful.

Thank you for participating in the AOCS Challenge. Best of luck with your control strategy!

If you have any questions please contact pr1u20@soton.ac.uk.
