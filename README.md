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

The first script was 
