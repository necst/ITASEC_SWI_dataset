# ITASEC_SWI_dataset
This repo contains the code and dataset to launch the synthetic attacks presented at ITASEC 2025 in the paper titled "A Deep Learning Approach for False Data Injection Attacks Detection in Smart Water Infrastructure"

## Setup
We have used Visual Studio Code with Jupyter exstension and Python v3.12.7. *Requirements* folder contains two txt files that list all the tools used with their version.

## Dataset
Inside the folder you can find the csv containing all the original measurements taken from the public repository. As you can see there, for each station there are 2 csv files. the reason behind this distinction is that one file has been used for training the model and the other one has been used to generate the attacked dataset.
- 'station_name' - 2021-12-31 -> used to train the model
- 'station_name' - 2024-08-19 -> used to create the attack dataset

### Types of attack
We have implemented three types of attack:

1. Random
   - In the implementation of the random attack, we begin by loading and cleaning our testing data. Once the dataset is cleaned, we select a range of values above the mean to identify periods when water levels are higher than average. Attack intervals are chosen carefully to ensure each starting point supports the full duration of the attack sequence. Once an interval is selected, it is marked and removed from the list of available indices to prevent reuse. Within each chosen interval, the water height values are deliberately altered. Specifically, the attack simulates intentional deviations by replacing the sensor readings with new, randomly generated values that fall within a predefined range. The range is constrained as follows: `Range = min_value + (min_value + max_value) / 3`. These new values are specifically chosen within this range to ensure low water height readings while still remaining within the acceptable range for each sensor.

![Single-plot_Sensor_5_Attack_2024-11-06_20-59-59_RANDOM_ALTERED](https://github.com/user-attachments/assets/35683271-add7-4412-838e-62fb5add4894)

2. Replay
   - In the replay attack, we first load and clean the dataset. We then select intervals within the dataset where water levels exceed the average, identifying periods associated with higher flow conditions. Each chosen interval satisfies criteria to ensure it aligns with the specified attack duration. Within each selected interval, sensor values are replaced with prior valid readings from within a defined range, effectively ‘replaying’ earlier water-level data. Initially, we tested a range defined as the one for the random attack. However, we found that no historical values fell within this narrower range. As a result, we adjusted it as follows: `Mod_Range = min_value + (min_value + max_value) / 2.9`. This modification provides realistic but deceptive data that subtly misleads the system. This approach maintains realistic fluctuations within the targeted range, subtly introducing misleading data into the system.

![Single-plot_Sensor_5_Attack_2024-11-11_14-29-02_REPLAY_ALTERED](https://github.com/user-attachments/assets/b2696ebd-f51c-4e50-9a96-623fa954b688)


3. Gradual decrement
   - In the gradual decrement attack, we begin by loading and cleaning the dataset in the same way as for the other attack types. We then identify intervals where water levels are above the average, selecting periods where higher water flow conditions prevail. For each identified interval, we progressively reduce the water height values in a controlled manner, decrementing each subsequent reading by a small, predefined amount until reaching a target threshold. This target is set just above the minimum measurable water level, ensuring the data remains plausible while introducing a gradual and subtle decrease. At each selected starting point for the attack, we calculate the difference between the current reading and the target level to determine how many steps are needed for a smooth reduction. This allows the attack to simulate a realistic lowering of water levels over time. Once the target threshold is reached in every targeted sensor, the sensor value remains constant at this target level for the remaining duration of the attack. This gradual decrease provides a slight way to introduce misleading data without causing abrupt changes, which could increase the likelihood of bypassing simple detection mechanisms and obscuring the true water level trends over time.
  
![Single-plot_Sensor_4_Attack_2024-11-06_22-07-21_GRADUAL_ALTERED](https://github.com/user-attachments/assets/38bf9aa3-ccf9-4f90-8540-891378dc5799)

The plots feature varying y-axis scales; however, they all cover the same temporal windows.

## Scripts
- **Attack_1.ipynb** carries on random and replay attacks on just one sensor, Pozzolo - Idrometro Monte

- **Attack_2.ipynb** carries on random and replay attacks on all sensors

- **Attack_3_with_wait.ipynb** carries on gradual decrement attack on just one sensor, Pozzolo - Idrometro Monte, or on all sensors
