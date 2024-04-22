# Body Posture Study Demo
 

Author: Kenan Suljić  
Date: 22.04.2024  
  
Under supervision of: Prof. Tobias Heed  


## Intro 

This woman's posture is unusual: her arms are crossed above the head. If limbs in unusual postures, e.g. crossed, are touched almost simultanusly, unusual things happen. For example touching the limbs in the order right > left in rapid successsion, can lead to the impression that one has been touched left > right. It can even be that a touch on the right hand is perceived as being on the left hand, alhtough there never was a touch. 

  <img src="/Intro/cross.webp" alt="crossedarms" height="500">

  
A touch on the body can be classified according to different frame of references, e.g. anatomical (skin based) or external (based on space).  

We wanted to investigate this strange behaviour with cognitive models. We used:  
- Drift Diffusion Model (DDM)
- Dual-Stage Two-Phase (DSTP) Model
  
  To ask:
- what happens during the processing of the touch if stimulus and/or response limbs are crossed?
- what changes if response modes change?  
  
  
**TLDR**;  
In our task, the touch process itself is impeded on the  
- stimulus coding level  
- response coding level
  
- in turn participants adjust their decision threshold to compensate for that  
  
- crossed response limbs led to impairments on the 
    - stimulus selection process
    - second response selection process  
    if response
    - in an anatomical reference frame and/or 
    - to an incongruent activation of the canonical reference frame 

- crossed stimulus limbs led to impairments
    - on the initial response selection phase

Bonus:
- top-down influence on the stimulus coding, but more so on the response coding level at later stages
  
- Dynamic reweighing of reference frames visible  



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