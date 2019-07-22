# NLPtextclassificationforRSA

Procedures of building a model and evaluation

(1)	Model Building  (for refreshed model building)

1.a. Data preparation
Getting the hisotry IBC data, 
Code:data_generator-Copy2_modified.ipynb
Saved the dataset:
Analytics_Data_training/IBC_data_260319_1.csv   

split into training and testing dataset, and 
IBCdata_prepare4modeling_April022019.ipynb

(out of 41025 charts, 2000 charts are randomly selected  for testing and the rest for training)
train_df.to_csv('ibc_trainingdata_040219.csv')
test_df.to_csv('ibc_testingdata_040219.csv')

Later, added some data from  coder feedback to training data
(the new ibc charts (6000-1000) but only the coder feedback instances to the IBC_data_260319_1.csv
I put the previously training and testing all back to training dataset, use the new 1000 charts we always used recently as testing data.
So now the training has almost 46171 charts, and the testing data has about 1000 charts (could be only 800 charts)
UpdatedIBCdata_prepare4modeling_May282019_exclude1000charts.ipynb
It turned out that the deletion instances from the coder feedback are just too few compared to the IBC_data_260319_1.csv, so it won’t make an impact even we use this version of training data.)


1.b. Building the models:

Use the hisotry ibc data only:

28 models 
Three groups of model ( phrase, code, deletion)

updatedIBC_phrase_code_deletion_based_general_April29.ipynb


Deep Larning Neural Network  LSTM model building(use the hisotry ibc data only):

deletionbasedalldata_bclass_DL_0327-updateon%20April24.ipynb

Save both the tokenizer and models as pickle files


With new coder feedback data:
28 models and one consolidated model
Four groups of model ( phrase, code, deletion, consolidated)

updatedIBC_phrase_code_deletion_based_general_May282019.ipynb
Save the models as pickle files 


(2)	Evaluating the Models (simulating the pipeline run process)

2.a.  Extract all the instances before AI  from link json and text files

First 1000 charts are downloaded to Drive in 106 server, and 
The code to extract text from link_json and txt files and the data are all in D drive.
D:\chartai_qa 
extract1000charts_jsontxt.py 
all instances are saved as run1000charts_0521.csv
Coder feedback is from 6000 conditions_with_chartids_29042019.csv
2.b. Extract recent coder reviewed charts from S3 
7500 charts are extracted as of June 07,2019
arango_querying_conditions_and_medications_uptodate-uselinkjson_gettext75_0607-Copy1.ipynb
First from ArangoDB query and obtain the chart_ids, then connect to S3 bucket and from componentJson and txt_full folders to extract the features (75 char before and after text)
All instances are saved as bfAI_allinst_7502charts_061019.csv in the same folder
Coder feedback file is saved as conditions_output_withchartid_090619.csv  with coder labels

2.c.  Evaluate using all instances dataset, then compare with coder feedback to get the model numbers
There are many different versions of code under 
/model_comparison

For 1000 charts as example:
run1000charts_individualmodelevaluation_fromlinkjson_usingAMmethod_0624.ipynb
This one can evaluate each individual model against coder feedback

run1000charts_refreshedmodelscombineLSTM_fromlinkjson_evaluateusingAMmethod_0624.ipynb
This one combines consolidated models (LR or/and LSTM), but you have to know how to combine 

run1000charts_refreshedmodels_fromlinkjson_evaluateusingAMmethod_0620_copy1.ipynb
Simpler version of just evaluating combined models, the FN part is modified in this code (and it should be modified in other codes if not)

7500 charts
run7500charts_refreshedmodel_fromlinkjson-0610-Copy2.ipynb
This one combines 29 models (28 refreshed models + consolidated LR model)

FP = Model predicted as pass overlap with coder (deleted) + Model predicted as pass – Model Predicted as pass overlap with coder (added + agreed) 
TP = Model predicted as pass overlap with coder (added + agreed)
FN = Model predicted as deleted overlap with coder (added + agreed)
TN = Model predicted as deleted – Model predicted as deleted overlap with coder (added + agreed) + Model predicted as deleted overlap with coder (deleted)
