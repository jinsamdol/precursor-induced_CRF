  
# Precursor-induced CRF for named entity recognition  

## reference: 
Lee, W., & Choi, J. (2019). Precursor-induced conditional random fields: connecting separate entities by induction for improved clinical named entity recognition. BMC Medical Informatics and Decision Making. 2019. 19:132 (https://doi.org/10.1186/s12911-019-0865-1)  
contact: jinsamdol at snu ac kr  
 
## Model-jar file description  
the conventional first-order CRF  
 - trainer: original_crf_trainer_o1_dev.jar  
 - labeler: original_crf_tester_o1_dev.jar  
  
the conventional second-order CRF  
 - trainer: original_crf_trainer_o2_dev.jar  
 - labeler: original_crf_tester_o2_dev.jar  
   
 the precursor-induced CRF (pi-CRF)  
 - trainer: pre-induced_crf_basic_trainer_dev.jar  
 - labeler: pre-induced_crf_basic_tester_dev.jar  
  
## Usage  
  
### training  
  
java -jar pre-induced_crf_basic_trainer_dev.jar --train_text="YOUR_TRAINING_DATA_ABSOLUTE_PATH" --model="YOUR_MODEL_ABSOLUTE_PATH" --report="YOUR_REPORT_FILE_ABSOLUTE_PATH" --valid_fraction=1.0 --sigma=10.0 --start_label="A*START" --null_feature="null"  
  
* all paths are absolute path  
* --valid_fraction=DECIMAL : the decimal indicates how much you adjust the portion of your training data out of your input data. In the case the value is 1.0, you use whole data as the training data and you have no validation data. In the case the value is 0.8, you use the 80% of the input data for the training data and 20% for the validation data. default is 1.0  
* --sigma=DECIMAL : this is the hyperparameter for sigma in the L2-regularization function. default is 10.0  
* --start_label="A*START" : setting the label for the starting label.  
* --null_feature="null" : many words may have no feature for certain feature dimensions for the features, for the case, you can indicate the null feature as a special tag and the default setting for the tag is 'null'.    
* --iter=INT : you can constrain the maximum iteration number.  
  
### labeling  
  
java -jar pre-induced_crf_opaque_tester_dev.jar "YOUR_TESTING_DATA_ABSOLUTE_PATH (DIRECTORY)" "SUFFIX_OF_YOUR_TRAINING_DATA" "YOUR_MODEL_ABSOLUTE_PATH" "OUTPUT_DATA_ABSOLUTE_PATH" "EVALUATION_DATA_ABSOLUTE_PATH"  
   
 * for instance, you can use the tester like this  
 java -jar pre-induced_crf_opaque_tester_dev.jar "D:\Dropbox\crf test" ".cntx.test.txt" "D:\Dropbox\crf test\my_pi_crf.crf" "D:\Dropbox\crf test\my_pi_crf_output.txt" "D:\Dropbox\crf test\my_pi_crf_eval.txt"  
   
 * We would like to note the last one is for evaluation result, however, the current evaluation module is unstable. Thus, please use your own evaluation code.  
   
 ## Important: chunk scheme: IOB2  
 B for the first token of an entity expanse.  
 I for the other tokens of an entity expanse.  
 O for the outside.  
 You have to use O (uppercase o) for the outside token for your data.  
   
 ## Data format  
 - single line is for one timestep.  
 - 'space' is delimiter for features  
 - single blank line indicates the end of an instance.  
 - the first column is for the word, the last column is for the label, and the others are for features.  
   
 For instance, we have two word sequences "The patient also reports blood-streaked diarrhea for two days ." and "The patient was exacerbated" and we use the word itself, next word and the previous word as features. Then it can be abstracted as a feature map as below  
 
 The t1:patient null O  
 patient t1:also t-1:The O  
 also t1:reports t-1:patient O  
 reports t1:blood-streaked t-1:also O  
 blood-streaked t1:diarrhea t-1:reports B-PROBLEM  
 diarrhea t1:for t-1:blood-streaked I-PROBLEM  
 for t1:two t-1:diarrhea O  
 two t1:days t-1:for O  
 days t1:. t-1:two O  
 . null t-1:days O  
   
 The t1:patient null O  
 patient t1:was t-1:The O  
 was t1:exacerbated t-1:patient O  
 exacerbated null t-1:was B-PROBLEM  
   
 the prefixes t1:... and t-1:... are used for distinguishing features each other. You may freely use arbitrary features, and you may freely use arbitrary prefixes for features, as well as, you may not use prefixes. However, I prefer to use prefixes in order to make distinction between different features because the CRF uses features in the form of a bag-of-features.   
   
  

