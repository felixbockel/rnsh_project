# In Short

## Code

1) matlab_itools_analysis and matlab_transform_hnd_to_image help to preprocess raw data before running 2).
2) first script is called matlab_run_all_patients_start_main_scipt  that includes all patients and initiates 3) script.
3) matlab_main_script_image_processing_workflow includes all matlab written functions in 4).
4) all matlab functions start with matlab_func.
5) matlab_plot_all_data is to help plotting transformed data 1)-4). Some of them are listed in section "In Detail".6) 

# In Detail
## Commissioning report of auto chestwall segmentation on MV- cine images for breast deep inspiration breath-hold (DIBH) whole breast and supraclavicular fossa treatments


## Contents

### Introduction & Scope	3

#### Functionality of auto segmentation software	4

#### Read .hnd files and delete noisy images, Matlab name: “read_hndfolder_save_as_image.m”	4

#### Prior to run matlab code “image_processing_workflow_V4.m”	5

#### Matlab code “image_processing_workflow_V4.m” is running	5

#### Image enhancement, Matlab name: “image_processing_workflow_V4.m”	6

#### Segmentation of chestwall & breast tissue	7

#### Analysis of segmentation	11

#### Analysis of all fractions of each patient, Matlab name: “plot_all_data_each_pat.m”	12

### Verification of amplitude and beam-on time by using abdominal CIRS phantom with hexamotion platform	14

#### Long-term stability of segmentation & reproducibility tests	15

#### Segmentation accuracy	16

#### Amplitude and time accuracy	16

#### Segmentation accuracy under more realistic circumstances	17

### Tables and Figures	21

### Literature	22


 
## Introduction & Scope

The idea of treating in deep inspiration breath-hold (DIBH) technique is to spare dose to organs at risks (OARs) such as heart and ipsilateral lung by increasing the distance between OARs and target area for left-sided breast cancer treatments1. Common surrogates for DIBH gating are skin markers together with video surveillance, real-time position management (RPM) provided by Varian, surface electromagnetic transponder or 3d surface imaging (i.e. AlignRT)2–6. A widely known setup strategy for surface guided gating is the xiphoid process which is located at the cartilaginous section at the lower end of the sternum7. This location may delivers a motion pattern similar to patients’ chestwall motion which can be used as a surrogate for whole breast treatment, but eventually misinterprets the motion to supraclavicular fossa (SCF) lymph nodes due to its far superior location. Megavoltage cinematographic (cine) imaging which is a well-known electronic portal imaging device (EPID) based image acquisition collects live images during a patient treatment. It gives an inside of chestwall motion for whole breast and SCF treatments by segmenting the chestwall on cine images which can be used as a distinct surrogate1,2,8–10. An automatic segmentation of the chestwall would speed up the process of locating chestwall in each image frame1,9,10. At RSNH we are also interested to determine intrafractional chestwall motion for EDW, IMRT and VMAT cases retrospectively. Despite segmentation challenges where MLCs and Yaws can cover parts of chestwall during beam-on time for dynamic treatments, it needs to be tested if it is feasible for at least IMRT and EDW treatments to capture enough cine images to observe patient motion. If additional data is required, breast tissue segmentation can provide motion data while patients’ cheswall is blocked out. Additionally, at RNSH we are seeing steady baseline drifts or rapid respiration of the breath-hold at beam-on time whilst keeping breath in breath-hold window for most of the time. Therefore our current breath-hold gating window is ± 5mm which is in compliance with other studies1,8,9. The scope of this commissioning is to review the automatic chestwall segmentation for DIBH treatments of whole breast as well as SCF beams on cine images and to assess intrafractional motion (maintenance of holding breath during beam-on time) and to examine the reproducibility of achieving same breath-hold level for each gating event.

 
### Functionality of auto segmentation software

Auto segmentation of chestwall and breast tissue was performed to be able to locate chestwall and breast tissue within each acquired MV image frame to assess intra-beam chestwall motion and maintanence for whole breast and scf treatment. Matlab (Version R2017a) was used to script the code. The software iTools (Version ) was used to save EPID based MV images during beam-on time at the Linac. The data is saved as .hnd. Seven left-sided breast cancer patients were treated utilizing Varian Trilogy machine under DIBH guidance by using RPM block as a gating device. Those seven patients were used as guidance to optimise the in-house segmentation software as part of commissioning. The segmentation software itself consists of several steps, such as:

	-Read .hnd files and delete noisy images, Matlab name: “read_hndfolder_save_as_image.m”
 
	-Image enhancement, Matlab name: “image_processing_workflow_V4.m”
 
	-Segmentation of chestwall & breast tissue
 
	-Analysis of segmentation
 
	-Analysis of all fractions of each patient, Matlab name: “plot_all_data_each_pat.m”
 
Currently there are 3 individual files (“read_hndfolder_save_as_image.m” for step 1, “image_processing_workflow_V4.m” for step 2-4 and “plot_all_data_each_pat.m” for step 5) to run to complete all steps. As you read further through the document, those three individual files are referred as follows: code step 1, code step 2 to 4 and code 5. Prior to running the in-house software, it is recommended to save the image data as follows step 1 to 3 (it is not required to change field names), otherwise it can lead to an error while running the code:

	-Main folder (naming convention is not important, i.e. it can contain patient ID, name, etc.)
 
	-Secondary folder (naming convention is not important, i.e. it should contain a fraction identifier such as fr1)
 
	-Tertiary folders:
 
		-Analysis
  
		-field001
  
		-field002
  
		-continue for all fields
  
Folder “Analysis” is created automatically when code step 1 is running. If for some instance this folder does not appear, then create one before running code step 2 to 4. It can cause an error, if this folder is not there. It is advised after running code 1 and prior to running code 2 to 4 to change the conventional field names from i.e. 20180918111558 to field001. Do that for all fields. If that is not been done, it can may cause as error since this code is looking for a specific naming convention likewise above (3a-d).

### Read .hnd files and delete noisy images, Matlab name: “read_hndfolder_save_as_image.m”

When running this code, it will ask you to select a folder. Please choose any secondary folder and press “select folder” afterwards. As a reminder, prior to start running this code, please add the path of all folders and subfolders to Matlab which belong to auto segmentation of chestwall and breast tissue. The software uses a compiler named IALMLToolbox that loads and opens .hnd files which is stored in folder “X”. If subfolders are not added, it may cause an error. The idea of this code is to delete very noisy images. Very noisy images don’t contain any information and are useless for further segmentation and motion analysis. The reason why those images are not kept, is to prevent the later code from having issues. Currently, the position of missing frames are not saved. It means when the code “Analysis of segmentation” is complete then the beam-on time calculated from the software may not match the beam-on time of the treatment (just a little mismatch for some fractions). During phantom measurement code 1 was not applied and therefore no missing frames were detected to ensure time and amplitude commissioning is done as precise as possible. However, the “missing frames” issue needs to be corrected in the next software version. One way of correcting it is to save the position of all missing frames and add them back in just prior to “Analysis of segmentation” and after “Segmentation of chestwall & breast tissue” is complete. This code also create the “Analysis” folder automatically. In the next version this code and the following one (meaning code 1 and code 2-4) can be combined.

### Prior to run matlab code “image_processing_workflow_V4.m”

Prior to running “image_processing_workflow_V4.m”, make sure the path of all folders and subfolders to Matlab belonging to auto segmentation of chestwall and breast tissue are added. Also an image dicom file .dcm of each fraction and patient needs to be added to “Analysis” folder. Please add a subfolder in “Analysis” folder named “SCF” and add the image dicom file for scf there. The reason for adding those specific files is to read out the EPID panel SID and the SSD which reflects isocenter to ensure the EPID projection will be corrected to isocenter. This is done as part of running “Analysis of segmentation”. The image dicom data needs to be pulled out from ARIA and then added to the folders, mentioned above. John K. has provided a script which gives an overview of treatment data such as date of treatment, SID and SSD, etc. for all fractions of each patient. It can be customised which hasn’t been done yet. Maybe come back to John K. for it. If the image dicom data is not added prior to running “image_processing_workflow_V4.m” the code will stop running at some point and ask you to add those files. The following will appear at the commant window: 

-'Please save image dicom (RI.file) of actual fraction and patient into Analysis folder and press ENTER if you wish to continue.';

-Matlab code “image_processing_workflow_V4.m” is running

-As soon as “image_processing_workflow_V4.m” is running, the code ask you to find a reference field if a patient receives IMRT fields. 

-show_str=strcat('field 00',num2str(count_file-1),': get ref img' )

No worries, the code identifies IMRT fields automatically. A reference field is defined as an open field where primarily chestwall is visible and secondarily breast tissue is visible as well. Since MLCs are constantly moving when an IMRT field is applied, there are just a few image frames where you find an open field containing chestwall and breast tissue. The reason why the code needs a reference field, it is used as a ground truth when it comes to segmenting chestwall and breast tissue from a cohort of image frames. It means if a segmented chestwall or breast tissue of an image has a similar shape to the segmented chestwall or breast tissue of the reference field then the segmentation is successful. If this condition is not met, then the segmentation fails. Failing means that this particular image will not contain a segmented chestwall nor breast tissue. To find a reference field for conformal and EDW fields automatically, it is already been implemented. It is feasible to find a reference field automatically for IMRT fields. A possible approach can be first to find image frames which are similar to an open field. From experience (going through a large number of cine images) the chestwall can be found ranging in the middle from a cohort of image frames. Find an image which is nearly open field and ranging in the middle of a stack of image frames.

### Image enhancement, Matlab name: “image_processing_workflow_V4.m”

Image enhancement is important for analysing anything on a low contrast image such as MV- image, especially when it is combined with different noise intensity as well. There are multiple ways of increasing the contrast and reducing the noise of an image. Other studies which have shown a successful chestwall segmentation used a Gaussian filter to remove the majority of artefacts before applying a canny filter (edge detection filter) using certain thresholds to be able to locate the chestwall9,10. Performing a Gaussian filter to a certain degree can lead to wash out (reduce contrast) an image. At RNSH we are using different boluses and dynamic treatment techniques which can produce extra predictable and unpredictable noise to an MV- image. The ground truth data is not as straight forward as for other studies. The latter preferred to do their chestwall segmentation on conformal tangent field and if an image gets a bit noisier (when using EDW technique and the Yaw pulls in) it was excluded from the study9. The idea of coding an in-house procedure for RNSH was to include all less noisy and noisier relevant images to ensure a precise chestwall analysis. Since the segmentation process is based on grey value intensity profile, the challenge was to reduce noise as much as possible but still maintain image contrast. Additionally, it is important to remove all unnecessary information which can influence a smooth segmentation such as background signal. Figure 1 shows an example of pre- and post-image enhancement. First step is to get rid of background information by performing a grey value threshold. Next step is to remove any high grey value intensities which are at field borders. It was done by performing Fourier transformation and then using a low-pass filter. Now field edges are smoothed, but white stripes associated to MLC leakage still remain. Another low-pass filter in form of a Median filter smooths out white stripes. The image contrast is improved to get a clear peak to locate the chestwall. Additionally, a field edge is created the helps to determine the breast tissue border. 

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/6cdf1990-83f6-4f39-821d-177566bab4e7)  
 
Figure 1: Example of pre- and post-image enhancement- reducing noise and increasing contrast subsequently.

### Segmentation of chestwall & breast tissue

The segmentation of chestwall is straight forward for low noisy images after image enhancement is completed successfully. The chestwall can hereby defined by the global maxima by looking at grey value intensity profile. If bolus is used then for some instances the edge of the bolus appears to be on the image and creates a higher grey value intensity than chestwall. It means the global maxima belongs to the bolus. To overcome this issue, the code needs to identify the 2nd maxima which is then associated to chestwall. If images get noisier during beam-on time (can happen for dynamic and EDW fields) then the code needs to segment on an image with a lower contrast, see Figure 2. To be specific, extra noise occurs when a Yaw pulls in (EDW) or MLCs are moving along the image (IMRT). As long as the noise does not outweighing the signal of an image (chestwall and breast tissue), the segmentation is successful.

![image](https://github.com/felixbockel/rnsh_project/assets/128773755/21119506-cd48-4fd1-bda1-84beb208d083)
 
Figure 2: An example of a nosier image: the segmentation is failing on the left, whereas on the right segmentation is successful after adjusting the code to be able to segment on lower contrast images.

Breast tissue was first segmented by using a grey value threshold procedure. This however was not successful for most of the patient cases due to spontaneous grey value fluctuation from image to image. An example can be seen in Figure 3. Therefore a new technique needed to be implemented to ensure a more stable segmentation. It means the quality of breast segmentation is nearly similar to chestwall detection, see Figure 4. Figure 5 shows the methology of the field edge technique. The location of breast tissue can be assessed by the intersection of an imaginary line which sits on peak of the field edge and the slop of the grey value intensity profile. 

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/3e6e4ba1-e64f-45c8-9e1f-8827285385c6)

Figure 3: Breast tissue segmentation by using grey value threshold procedure on the left and field edge technique on the right.

![image](https://github.com/felixbockel/rnsh_project/assets/128773755/ead25655-c11b-415e-9f9a-07b2ac8299d4)
 
Figure 4: Comparison of chestwall and breast tissue segmentation with field edge technique by using a conformal tangent field. A 2.0 mm chestwall motion as well 1.9 mm breast tissue motion was detected during beam-on time.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/8bdaaaf1-ce90-4522-ad82-8107879b6b00)

Figure 5: Breast tissue segmentation using field edge technique.

To be able to detect the chestwall in SCF images, a similar segmentation approach has been used as for whole breast treatment. Since the chestwall in SCF images is located vertically and the chestwall in whole breast images appears to be horizontal, SCF images have be to rotated 90 degrees in order to comply with the segmentation algorithm. Figure 6 shows an example of chestwall segmentation methology in SCF. Segmentation errors can be related to fields with very low monitor units (MU) such as 20 in comparison to 150. It seems for such fields that the chestwall is barely visible by looking at the image and no distinct peak is detectable by looking at the grey value intensity profile. For the latter instance there is no recommendation so far how to improve segmentation.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/734a0c13-6b77-4b0b-943b-de58780d36ff)

Figure 6: SCF segmentation. Chestwall bone in SCF is usually found to be on the bottom of the field.

### Analysis of segmentation

The segmentation of chestwall and breast tissue has been complete. It means there are now two files available where one file includes chestwall and the other one breast tissue data. Each file consist of a matrix where each column stands for an image frame and each row defines the position of segmentation. When for instance segmentation failed, the matrix will be filled with NaNs. The code will continue to run without showing an error and without analysing the data. The aim of “Analysis of segmentation” code is to represent chestwall (primary) and breast tissue (secondary) change in amplitude [mm] over the period of beam-on time [sec], as seen in Figure 7. As long as chestwall is clearly visible throughout the image frames the plot will only show chestwall motion. When chestwall is covered and breast tissue is detectable then breast tissue data takes over from the moment chestwall is blocked out. In other words chestwall is considered to be the primary element of segmentation and when chestwall cannot be detected then breast tissue segmentation delivers a secondary motion data set. The plot also shows the direction of motion. For instance if the segmented motion is anterior or posterior. Since the beams are tangent fields, it can be discussed whether the direction of motion is a residual of lateral and anterior/posterior motion or anterior/posterior only. For simplicity the author decided to consider the detected motion as anterior/posterior only. In order to visualize the amplitude motion over time in isocenter, the EPID projection (SID) had to be corrected to be in isocenter (SAD). The following ratio was used11:

Vector of motion in isocenter=Vector of segmented data×(RadiationMachineSAD/RTImageSID)	(1)

Afterwards pixel shifts needed to be converted to mm shifts, can be seen as follows:

Vector of motion in mm=Pixel spacing× Vector of motion in isocenter		(2)

The “vector of segmented data” contains the motion of each image frame (chestwall or breast tissue) in relation to the first segmented frame. The latter is considered to be zero, as seen in Figure 7. In other words, each column of the matrix which contains the segmented data was subtracted from the column which had the position of the first segmented frame saved. Subsequently, the mean was taken of each column which provided a vector which displays the change in motion from frame to frame. As the last, the “vector of segmented data” was interpolated to make the motion visible in the plot. Additionally, the code “Analysis of segmentation” saves a .txt file which includes the interpolated “Vector of motion in mm” for further analysis.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/730f6e29-6593-427e-a022-5b8ace3d4f84)

Figure 7: Result of analysing the segmentation of chestwall.

### Analysis of all fractions of each patient, Matlab name: “plot_all_data_each_pat.m”

As soon as all fractions or part of it are analysed individually, “plot_all_data_each_pat.m” provides a plot that displays all individual plots into one to get an overview of overall motion, as seen in Figure 8 for whole breast and Figure 9 for SCF treatment.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/dff7822a-d9c0-45ef-a876-63fd1fea754f)

Figure 8: Analysis of motion of all fractions for whole breast treatment.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/666ed933-e235-49b7-84db-d57da59284e8)

Figure 9: Analysis of motion of all fractions for SCF treatment.

## Verification of amplitude and beam-on time by using abdominal CIRS phantom with hexamotion platform

A few tests outside had to be performed with a phantom to be able to verify accuracy of chestwall and breast tissue segmentation. Following tests were under the scope:

	-Long-term stability of segmentation & reproducibility tests
 
	-Segmentation accuracy
 
	-Amplitude and time accuracy
 
	-Segmentation accuracy under more realistic circumstances
 
Prior to testing accuracy of segmentation it is mandatory to maintain a reproducibility setup, as shown in Figure 10. For this commissioning process the abdominal CIRS phantom was put ontop of hexamation platform. The CIRS phantom provides a lung chestwall boundary which is necessary chestwall segmentation and for measurements close to reality. The hexamotion platform provides a platform to setup the CIRS phantom reproducible and delivers a precise amplitude motion ranging ±5 mm in x, y, z direction (lateral, ant/post, sup/inf) to simulate a breathing trace within breath-hold gating window of ±5 mm. As a reminder, to be able to detect sort of chestwall and breast tissue it is recommended to shift the phantom to a location until you see a clear lung-chestwall boundary. It can be done by irradiating a couple of fields and look at the MV image right after. Move the phantom lateral until the image looks like in Figure 10 (right image).

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/0fd69d0c-cd42-450c-9ad1-dd7fdcd34202)

Figure 10: Phantom setup.

### Long-term stability of segmentation & reproducibility tests

Long-term stability test was done on 04/07/2018 on TS2 as part of a 1 hour procedure. The QA patient which was used for those measurement is called Breast, Daily Imaging/CIRS motion/CIRS mo 6x MU. Tangent fields were applied with 500 Mus per field which equals a treatment time of 45.6 seconds. A standard breast DIBH treatment goes ~20 seconds. Therefore a more than double time can be utilized to observe baseline drifts (Amplitude drifts) which may affect motion segmentation. Figure 11 shows an example of a stable amplitude over the course of beam-on time.

![image](https://github.com/felixbockel/rnsh_project/assets/128773755/f86f81d3-7183-4b5b-9bcd-1ae5efddeb3e)
 
Figure 11: Segmentation stability test.

The reproducibility test reflects if data outcome is the same on different measurements dates with having a reproducible setup. The measurements to test the reproducibility has not been done. 

### Segmentation accuracy

To test segmentation accuracy no motion to the phantom was applied whilst delivering tangent open fields. Figure 12 shows two measurement and in each the segmentation error was 0.1 mm.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/3cf95d20-c737-4b93-ba32-b213c1a73fc6)

Figure 12: Segmentation accuracy testing.

### Amplitude and time accuracy

Motion amplitude [mm] was tested by using a tangent open field. To be able to reproduce the phantom motion a lateral ±5 mm was applied with Gantry rotation of 0 degrees and Collimator angle of 0 degrees as well. The result can be seen in Figure 13. The amplitude indicated in the plot has an amplitude of ~10 mm which very similar to the amplitude applied to the phantom of 10 mm.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/76178c1c-24f4-46b1-a98a-924021dd9b29)

Figure 13: Amplitude accuracy.

Regarding time accuracy, please refer to Figure 11, the treatment field had ~500 MU which equalled to 45.6 seconds treatment time on the machine. 436 MV-images were acquired by iTools with a frame per second (fps) rate of 9.5. In general fps ranges form 7-9 for whole Breast and SCF treatment. The total calculated time would be 45.89 seconds. As a reminder, MV-images were segmented without using code 1. It means no missing data is to be expected. Therefore the plot in Figure 11 shows a time of ~45 seconds which means the plotted treatment time is very similar to the one on the treatment machine.

### Segmentation accuracy under more realistic circumstances

The segmentation under more realistic circumstances was investigated by using a tangent open field with gantry rotation of 340 degrees and applying 3 types of phantom motion (lateral, lateral + anterior/posterior and anterior\posterior) while keeping ±5 mm phantom amplitude the same for all measurements. Theoretically, it can be expected that lat+ ant/post motion show highest amplitude followed by lat and ant/post due to the X-ray source-to-EPID projection, shown in Figure 17. The measurement results in Figure 14- 16 confirm the expectation in Figure 17 where lat+ ant/post has ~ 10 mm, lat ~9 mm and ant/post ~4mm motion in amplitude.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/2eb3a48c-efa3-497c-81ba-d2b0e47acbfb)

Figure 14: Segmentation of 340 degrees tangent field. This image is associated with ant/post motion.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/affd27b5-3f0c-4172-8b58-51522eedc0fc)

Figure 15: Segmentation of 340 degrees tangent field. This image is associated with lateral + ant/post motion.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/2ab2d9fa-a851-4da4-a16a-0ca84a7fdee9)

Figure 16: Segmentation of 340 degrees tangent field. This image is associated with lateral motion.

 ![image](https://github.com/felixbockel/rnsh_project/assets/128773755/dc09e794-40ba-434f-a2e8-51477b77ba4f)

Figure 17: Different amplitudes can be expected at EPID projection when three types motion (lat, lat+ant/post and ant/post) is applied with constant phantom amplitude of 5mm.

## Tables and Figures

Figure 1: Example of pre- and post-image enhancement- reducing noise and increasing contrast subsequently.	7

Figure 2: An example of a nosier image: the segmentation is failing on the left, whereas on the right segmentation is successful after adjusting the code to be able to segment on lower contrast images.	8

Figure 3: Breast tissue segmentation by using grey value threshold procedure on the left and field edge technique on the right.	8

Figure 4: Comparison of chestwall and breast tissue segmentation with field edge technique by using a conformal tangent field. A 2.0 mm chestwall motion as well 1.9 mm breast tissue motion was detected during beam-on time.	9

Figure 5: Breast tissue segmentation using field edge technique.	10

Figure 6: SCF segmentation. Chestwall bone in SCF is usually found to be on the bottom of the field.	10

Figure 7: Result of analysing the segmentation of chestwall.	12

Figure 8: Analysis of motion of all fractions for whole breast treatment.	13

Figure 9: Analysis of motion of all fractions for SCF treatment.	14

Figure 10: Phantom setup.	15

Figure 11: Segmentation stability test.	15

Figure 12: Segmentation accuracy testing.	16

Figure 13: Amplitude accuracy.	17

Figure 14: Segmentation of 340 degrees tangent field. This image is associated with ant/post motion.	18

Figure 15: Segmentation of 340 degrees tangent field. This image is associated with lateral + ant/post motion.	19

Figure 16: Segmentation of 340 degrees tangent field. This image is associated with lateral motion.	20

Figure 17: Different amplitudes can be expected at EPID projection when three types motion (lat, lat+ant/post and ant/post) is applied with constant phantom amplitude of 5mm.	21
 
## Literature

1.	Doebrich, M., Downie, J. & Lehmann, J. Continuous breath-hold assessment during breast radiotherapy using portal imaging. Phys. Imaging Radiat. Oncol. 5, 64–68 (2018).
2.	Estoesta, R. P. et al. Assessment of voluntary deep inspiration breath-hold with CINE imaging for breast radiotherapy. J Med Imaging Radiat Oncol 61, 689–694 (2017).
3.	Fang, L. et al. Novel Method of Cardiac Sparing With Deep-Inspiration Breath-Hold Utilizing Surface Electromagnetic Transponders in Women With Left-Sided Breast Cancer. Int. J. Radiat. Oncol. 87, (2013).
4.	Betgen, A. et al. Assessment of set-up variability during deep inspiration breath hold radiotherapy for breast cancer patients by 3D-surface imaging. Radiother Oncol 106, 225–230 (2013).
5.	Hjelstuen, M. H., Mjaaland, I., Vikstrom, J. & Dybvik, K. I. Radiation during deep inspiration allows loco-regional treatment of left breast and axillary-, supraclavicular- and internal mammary lymph nodes without compromising target coverage or dose restrictions to organs at risk. Acta Oncol 51, 333–344 (2012).
6.	Rice, L., Goldsmith, C., Green, M. M., Cleator, S. & Price, P. M. An effective deep-inspiration breath-hold radiotherapy technique for left-breast cancer: impact of post-mastectomy treatment, nodal coverage, and dose schedule on organs at risk. Breast Cancer (Dove Med Press. 9, 437–446 (2017).
7.	Pedersen, A. N., Korreman, S., Nystrom, H. & Specht, L. Breathing adapted radiotherapy of breast cancer: reduction of cardiac and pulmonary doses using voluntary inspiration breath-hold. Radiother Oncol 72, 53–60 (2004).
8.	Göksel, E. O. Monitoring the Maintainability of Deep Inspiration Phase with Cine Acquisition in Patients with Left-Sided Breast Carcinoma Receiving Radiotherapy. J. Nucl. Med. Radiat. Ther. 04, (2013).
9.	Jensen, C. et al. Cine EPID evaluation of two non-commercial techniques for DIBH. Med Phys 41, 21730 (2014).
10.	l. Conroy  S. Quirk, T. Pham, W.L. Smith, R. Y. Retrospective evaluation of visually monitored deep inspiration breath hold for breast cancer patients using edge detection. (2015).
11.	Shiinoki, T. et al. Verification of respiratory-gated radiotherapy with new real-time tumour-tracking radiotherapy system using cine EPID images and a log file. Phys. Med. Biol. (2017). doi:10.1088/1361-6560/aa587d


