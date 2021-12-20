# phytophthora_mechanostat

Dear reader, this is the Readme file accompanying the publication in regards to mechano feedback btween the actin cytoskeleton and invasive stress generation of Phytophthora infestans hyphae. This is an README file describing all codes published on this git directory. We stress that some of the input data (displacement maps, Naifu model) is hosted in another git, namely: https://github.com/jorissprakel/Phytopthora_invasion

The input data forall matlab analysis is .tif format images (lossless saves from .nd2 original data format), imaged using a nikon C2 confocal using 60x magnification objectives in general. The following scripts work as an expansion on the previous publications set to work with establishing colocalizations and intensity profiles of the hyphal tube. We stress that ImageJ (Fiji) was used in most cases for simple max projection/visualization purposes, and Origin to plot datapoints in a clear format. 

To obtain XZ projections of hyphae and track the intensity, we developed a software called TubeAnalyser (TubeAnal for short) that loads in a local ROI z-stack, computes the XZ projection (amongs others), defines a centreline from the hyphae and walks along that centreline with a frame record the intensity along the hyphae. The code is elobarately described in the scripts itself as well; feel free to adapt and change. Some of the scripts require packages (i.e. bwmorph functions are employed), my apologies.

To start, areas were defined using the script:
From_ArbProfileData_GermTubeSelect_Analysis_v3_XZ
This script allows manual clicking to define the GFP intensity map (.tif), size of the step (XY and Z) and load in the z-stack. Then a step to define an ROI (manual, and also automated possibility for a set of given coordinates) using 2 coordinates to define a line and the amount of pixels to the side to define a small ROI. The script creates a new directory (based on the original name of the file), which is then stepped into. In the new folder, an image of the ROI at the whole image and zoomin are made. The raw ROI intensities is extracted and a plot of the intensity over the ROI from left to right is plotted. (all in the XY plane)
Then the code computes a 90 degree rotation of the original GFP z-stack to the XZ plane, and saves an image of the sideprojection. A latent part of code (ignored for the future) was also here to walk through the XZ projection and define a set of slices to use for curvature analysis (since been surpassed by the Naifu model analysis). Important saving parameter: 'Arb_profile_data_XZ1.mat' which contains the complete workspace.

The second script is called:
From_ArbProfileData_GermTubeSelect_Analysis_Step2_Jochem_vXZ
This is a script that  is adapted from a looped version; it can be restored using i as the iterator.
in this script, the data of the previous script is loaded and the XZ projection is made. On this projection an area of background signal is defined and isolated. The intensities of the background are fitted with a lognormal function, of which a threshold of the average + 5* sigma. A binarised map is computed of the XZ map of pixels above and below of this threshold, made and saved. This data is saved as Arb_profile_data_XZ1weighedThickness.mat, containing all of this data.

The thrid script is called:
From_ArbProfileData_GermTubeSelect_Analysis_Step3_TubeAnal_vXZ 
This is a script that  is adapted from a looped version; it can be restored using i as the iterator.
This script using thesame binarised map, defines a start of the tube (in pixel number) from the cyst and reduces the binarised to a centreline. A new folder is made called data_movie_tube, in which snapshots of the frame (size 15 pixels, approximately the thickness of the hyphae for the smalles XY step acquisitions) walking along the centreline are saved and the mean of the points within the frame is computed. (I used the mean function here, which was a bit stupid; sometimes NaN values border the bottom of the hyphae resulting in NaN values for the average. In the future, update with nanmean on line 130) The frame is moved 1 step along the centreline and imaged/mean computed until the end of the centreline is reached. The script then jumps back to the original directory, makes a plot of the intensity along the tube and computes the value of alpha (polarization).
Alpha is computed by (deviding the maximum fluorescent intensity along the entire profile by the intensity of the hyphae from the start to the minimal hyphal tube length)-1.

These 3 code sections form the core of the alpha analysis, and was the way how the alpha distributions etc were calculated.

To colocalize displacement and GFP intensity, a set of scripts was developed.
To be clear, the displacement map etc were first computed using the scripts from the previous paper, which are found at the link above. The goal is to have from thesame Region Of Interest (ROI) 2 files: the displacement map and the GFP intensity (XY projection)

The first script is called:
Data_Extraction_for_displacements.m 
In general, all of the timesteps are saved in a separate folder for each timestep when computing displacement fields. This is a bit annoying to index, so this script finds all the displacement maps in the folders underneath. It then defines a saving goal (t1 folder that will contain multiple timesteps of displacement data), and saves the files under a slightly modified name with an TXXXzpos_FPL_final.mat iterator where XXX is the timestep. This new list is then used in the next script. The script can be modified quite easily to look for other types of files (images, other .mat data).

The second script is called:
Joris_Colocalization_trial1_timestep_35_200128_Roi1_infestansv2 (name changes with different sampledates/names (timestep is iterator, manually adjusted))
In short, the code starts by defining where the GFP and zpos (displacement) data are to be found, and the correct timestep. It loads in both datasets, linearises both (from an image to a long vector) and plots them and saves an image of them. Then the code defines x_limits (displacement limits) above/between/below which 3 groups are defined (adhesive/non-interacting/indentive), and extracts the 3 groups of data from both the GFP and displacement maps. Some overlap pictures of 4 groups are made, this is not used in publication but practical information. The original displacement map is replotted in high resolution. The GFP map is replotted as well in the original gray colourmap and saved as figure and .tif format. Then the data is saved as a complete workspace.mat, which is the main output of this script. Note that the workspace saving is a manual operation; this could be improved in the future.

The third script is called:
Joris_Colocalization_step2_histogram_Intensity_DisplacementAbs.m 
This code loads in the workspace.mat file as provided by the previous code, removing excess data to clear up memory for the next steps. It makes an histogram of the raw GFP pixel intensities, fits a gaussian fit to these points, and computes a 5 sigma above normal background as the threshold value to establish cellular signal from background. Cellular pixels are then defined as being baoe this threshold, and made relative by deviding each celullar pixel value by the mean of all celullar pixel values. This data is then saved as DataCell_FluoRenorm_zpos_sigma_XXX.mat, does some more (obsolete) statistics to describe the distribution of fluorescence vs displacement, and saves all the data in 'workspace_afterstep2.mat'.

The Fourth script is called:
Joris_Colocalization_step3_aggregate_Ttest_data.m 
This script makes an overview of the files saved in the DataCell_FluoRenorm_zpos_sigma_XXX.mat format, loads them in, and structures all the test and puts them in an array called saving_array. This script is obsolete; I have only sved it here so that the stepwise numbering makes sense. Its output is not saved and not needed to run the final analysis.

The fifth script is called:
Joris_Colocalization_step4_aggregate_disp_fluo_data.m
This code is written to aggregate the displacement and relatife fluoscence data, to run t-tests etc later on in something called saving_array. Some plots of the data are made for overview purposes. Data is saved in Aggr_Data_RelFluoDispXXX.mat.

The sixth script is called:
Joris_Colocalization_stepr_aggregate_aggregate_disp_fluo_data.m
This code is written to aggregate the aggregate of the displacement and relatife fluoscence data, to run t-tests etc later on in something called data_aggr_save. Some plots of the data are made for overview purposes. Data is saved in AllT_AllCells_Data_RelFluoDisp.mat. T-tests were performed in thesame way as Joris_Colocalization_step2_histogram_Intensity_DisplacementAbs.m, but then adapted that script to tests the 3 group of data that were aggregated instead of the data of 1 cell at 1 timepoint.

Naifu invasion

The script is called:
Code_Arb_Profile_Avg_v2_MinMaxROI_V2
This script finds all the workspaces defined before by the previous script, and itrates the following process on all of them. It starts by loading in the GFP and Displacement map data. It then deinfes an area in the ROI that will be searched in for the highes and lowest value of displacement (sometimes the ROI contains a spot somewhere were a dead cell has settled, this thwors the code off if no subregion is identified). A linear interpolation between the points of highest and lowest displacement is performed, extended for 30 pixels in both directions and saved. This profile is the input data for the MC algorithmn to fit the Naifu model!
