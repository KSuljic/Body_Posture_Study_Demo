# Body Posture Study Demo
 

Author: Kenan Suljić  
Date: 22.04.2024  
  
Under supervision of: Prof. Tobias Heed  


## Intro 

# A Cognitive Model Study on Touch Perception

This repository dives into the fascinating interplay between body posture and sensory perception. When our limbs are positioned unusually, such as being crossed or intertwined, our brain's interpretation of touch can lead to intriguing sensory illusions. For example, touching the limbs in quick succession from right to left might be perceived in the reverse order, or a touch on the right hand might be felt on the left, without any actual contact. These phenomena underscore the complex and sometimes surprising ways our brain processes touch and body positions.

  <img src="/Intro/cross.webp" alt="crossedarms" height="500">

## Study Focus:
We investigate these sensory quirks using sophisticated cognitive models:
- **Drift Diffusion Model (DDM)**: Explores the decision-making process at a fundamental level.
- **Dual-Stage Two-Phase (DSTP) Model**: Offers deeper insights into decision-making dynamics during conflicting sensory inputs.


## Key Research Questions:
- How does the processing of touch change when stimulus and/or response limbs are crossed?
- What are the effects of changing response modes on sensory perception?


## Findings Summary:
- **Impairments Identified**: Touch processing is notably impeded at both the stimulus coding and response coding levels, compelling participants to adjust their decision thresholds.
- **Impact of Crossed Limbs**:
  - **Stimulus Limbs**: Crossing affects the initial response selection phase.
  - **Response Limbs**: Leads to challenges in the stimulus selection and the second response selection processes, when responses are within an anatomical reference frame or involve incongruent activations of the canonical reference frame.


## Bonus Insights:
- **Top-Down Influence**: There is a significant top-down effect on stimulus coding, with a more pronounced impact on response coding at later stages.
- **Dynamic Reweighing**: The study shows dynamic reweighing of reference frames, providing new insights into how our brains adapt to complex sensory environments.

**TL;DR**:
In our tasks, the tactile processing is hindered at various levels, leading to compensatory adjustments in decision thresholds. The crossing of limbs, whether for stimulating or responding, introduces significant impairments that influence both stimulus and response selection processes.


## Methods

### Setup

Experimental Setup. Body posture could be: arms [uncrossed/crossed], legs [uncrossed/crossed] with response mode [anatomical/external]
  
Figure (Kenan Suljić under All Rights Reserved) adapted from Ossandon et al. (2019).

![Setup](/Methods/setup.png)


### Cognitive Models

Figure (Kenan Suljić under CC BY-NC-SA) show cognitive models used, i.e. the DDM and DSTP model.
  
DDM:  
![DDM](/Intro/DDM.png)
  
DSTP:    
![DSTP](/Intro/DSTP.png)


  
## Results
  

### Behaviour
  
Visualization of the behaviour: response times and response accuracies.

![responses](/Results/Responses.png)
  

Code to make this plot:

```python

# Define the color palette for different conditions
palette_condition = ['#c0bcb5', '#3a7d44', '#22577a', '#ff101f']

# Define the order of conditions and corresponding color palette and labels for plotting
condition_order = ['ex_HU_LU', 'ex_HX_LU', 'ex_HU_LX', 'ex_HX_LX', 
                   'ana_HU_LU', 'ana_HX_LU', 'ana_HU_LX', 'ana_HX_LX']
color_palette = ['grey', 'green', 'blue', 'red']
condition_labels = ['AU/LU', 'AX/LU', 'AU/LX', 'AX/LX']

# Initialize a 2x2 subplot layout with figure size specified
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
response_modes = ['External', 'Anatomical']  # Define response modes for plotting

# Plot violin plots for response times in the upper row of subplots
for i, response_mode in enumerate(['ex', 'ana']):
    ax = sns.violinplot(x='condition', y='trial_RT_ms', hue='trial_correct',
                        data=df[df['block_condition'].str.startswith(response_mode)], 
                        order=condition_order[:4] if response_mode == 'ex' else condition_order[4:], 
                        palette=['red', 'darkgreen'], fill=False, inner="quart", split=True, ax=axes[0, i])
    ax.set_title(f'Response Time \n {response_modes[i]} Response Mode')
    ax.set_xlabel('Condition')
    ax.set_ylim(0, 2000)
    ax.set_ylabel('Response Time [ms]')
    ax.legend(title='Correctness')
    ax.set_xticklabels(condition_labels)
    ax.set_yticks(np.arange(0, 2100, step=100))
    ax.yaxis.grid(True, linestyle='-', alpha=0.20)  # Enhance grid visibility for better readability

    # Adjust legend to more intuitive labels
    handles, labels = ax.get_legend_handles_labels()
    new_labels = ['Error' if label == '0' else 'Correct' for label in labels]
    ax.legend(handles, new_labels, title='Correctness', bbox_to_anchor=(1.01, 1), loc='upper right')

# Plot boxplots and strip plots for accuracy in the lower row of subplots
for i, response_mode in enumerate(['ex', 'ana']):
    accuracy_df = df[df['block_condition'].str.startswith(response_mode)].groupby(['subjIndx', 'condition'])['trial_correct'].mean().reset_index()
    accuracy_df['trial_correct'] *= 100  # Convert accuracy to percentage
    ax = sns.boxplot(x='condition', y='trial_correct', data=accuracy_df, 
                     order=condition_order[:4] if response_mode == 'ex' else condition_order[4:], 
                     palette=palette_condition, fill=False, ax=axes[1, i])
    
    sns.stripplot(x='condition', y='trial_correct', data=accuracy_df, 
                  order=condition_order[:4] if response_mode == 'ex' else condition_order[4:], 
                  palette=palette_condition, edgecolor='white', linewidth=1, alpha=0.5, ax=axes[1, i])
    
    ax.set_title(f'Response Accuracy \n {response_modes[i]} Response Mode')
    ax.set_xlabel('Condition')
    ax.set_ylabel('Accuracy [%]')
    ax.set_ylim(45, 100)  # Set y-axis limits to show accuracy as percentage
    ax.set_xlim(-0.5, 3.5)  # Set x-axis limits for neat alignment
    ax.hlines(y=50, xmin=-0.5, xmax=3.5, colors='black', linestyles='--', label='Chance Level')
    ax.text(x=2.5, y=47, s='Chance Level', color='black')
    ax.set_xticklabels(condition_labels)
    ax.set_yticks(np.arange(45, 105, step=5))
    ax.yaxis.grid(True, linestyle='-', alpha=0.20)

# Adjust layout to prevent overlap and save the figure
plt.tight_layout()
plt.savefig('../../Plots/RT_RA_Plot.png', dpi=600)
plt.show()



```


### Model Comparison

#### DDM

Comparing DDM with empirical data.

Probability Density Estimations (PDEs):  
![DDM_PDE](/Results/DDM_PDE.png)

Conditional Accuracy Functions (CAFs):  
![DDM_CAF](/Results/DDM_CAF.png)
  
Delta Function (DFs)  
![DDM_DF](/Results/DDM_DF.png)
  

DDM Parameters:  

![DDM_Parameters](/Results/DDM_Parameters.png)


#### DSTP

Comparing DSTP with empirical data.

Probability Density Estimations (PDEs):  
![DSTP_PDE](/Results/DSTP_PDE.png)

Conditional Accuracy Functions (CAFs):  
![DSTP_CAF](/Results/DSTP_CAF.png)
  
Delta Function (DFs)  
![DSTP_DF](/Results/DSTP_DF.png)
  

DSTP Parameters:  

![DSTP_Parameters](/Results/DSTP_Parameters.png)

DSTP Parameter Comparisons:  

![DDM_Parameter_Comp](/Results/DSTP_Parameter_comparisons.png)


### Time Schema

I created a time schema for the DSTP model to better understand the timings of the phases for each condition.


![Schema](/Results/TimeSchema.png)


# References

Ossandon, Suljic, Heed (in prep)