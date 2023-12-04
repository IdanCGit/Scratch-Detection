# Scratch-Detection
Scratch detection on wafers given as coordinate maps.
The motivation behind the work represented here, is to detect scartches on wafers and mark wafers as with scratches or without.
Each Die on a wafer is represented in the dataset as a lin, where the wafer name (WaferName) is specified (same as ID), X coordinate (DieX) and Y coordinate (DieY), and wether the Die is good or not (IsGoodDie).  \
In addition, in the train dataset, there's an additional column that specifies wether a specific die is part of a scratch or not (IsScratchDie).

## Exploring the data

*Table 1 – Train dataset*
| WaferName |	DieX |	DieY |	IsGoodDie	| IsScratchDie |
| :---: | :---: | :---: | :---: | :---: |
| asw42 | 0 |	12 |	False |	False |
| asw42 | 0 |	13 |	True |	False |
| asw42 | 0 |	14 |	True |	False |
| asw42 | 0 |	15 |	False |	False | 

The main goal is to classify individual dies as scratch or not. 

This problem cannot/preferably not be solved/approached to as a tabular problem. 
Transforming it to a binary image segmentation problem may be better.

Business goals notes:
- Automation - the goal is to perform this procedure in a faster time and save the costs of the test.
- Quality - find scratches but reduce inking (minimal as possible). 
- Prediction Level - add binary classification per image (included in the helper function already), because sometimes it will help to get a classification at the wafer level, (binary classification, is there a scratch on this wafer or not?) because there are manufacturers who return scratched wafers to the factory.

### Problem:
Imbalance in data, naturally in this task

*Figure 1 - Imbalance in data*
<p align="center">
  <img alt="Light" src=https://github.com/IdanCGit/Scratch-Detection/assets/139128502/2ddd2801-83c2-48c2-bc0d-c37bf098003d width="45%">
</p>

## U-Net model
Semantic segmentation, FCN / CNN, further research showed that U-Net will be suitable here. 

Training a NNET on images require uniform sizes, therefore, I'll be padding the images with zeros, technicaly - padding with "not IsGoodDie" bool values. 

The solution was built using UNET model

*Figure 2 - UNET model illustration*
<p align="center">
  <img alt="Light" src=https://github.com/IdanCGit/Scratch-Detection/assets/139128502/3496c63b-576b-478d-b31f-e352b3c0afd9 width="45%">
</p>

## Results
*Table 2 - Final model results*
| Model Name  |  U-Net-Like Model |
| :---: | :---: |
| Binary IoU |               0.882 |
| Recall |                    0.84 |
| Precision |                0.896 |
| PRC |                      0.937 |
| AUC |                      0.997 |

**Notable evaluation metrics for this task:**
- Precision = tp / (tp+fp) == how many predicted scratches are actually scratches

- Recall = tp / (tp + fn) == how many scratches were actually found

- Binary IoU (Intersection-Over-Union) = tp / (tp + fp + fn) 

- AUC of ROC curve (tpv vs fpr)

- AUC of PRC curve (precision vs recall)

### The resulted maps after prediction on the test data
*Figure 3 - Resulted maps after predictions*
<p align="center">
  <img alt="Light" src=https://github.com/IdanCGit/Scratch-Detection/assets/139128502/2574b7b5-3dc9-4024-828d-e65e81e3f895 width="45%">
</p>

### Model can be improved by:
- Finding the best hyperparameter
- Changing/Tuning the arcitecture
- Further testing on attending to the imbalance in the dataset
- More preprocessing: can use mathematical morphologies with diagonal/vertical/horizontal(thin) structering elements on the wafer images to reduce noise but not not delete scratches. \
This can be risky as I described above, but would've been worth trying.
