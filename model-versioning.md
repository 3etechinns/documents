# Model Versioning
rorodata platform provides users with a simple yet powerful way of tracking models, we call this feature Model Versioning. It builds on a simple idea that the user can save the model, as well as any associated metadata of his choosing,  to a central model repository. rorodata tracks all these changes centrally and provides users the ability to go back to any version stored on rorodata platform, inspect the metadata and even revert back to any of the earlier models.

### How-To
The following describes the code steps involved in using model management. This piece of code is part of the model training process and follows once the model has finished training.
From the current project, get the model repository for this project

```
p = roro.get_current_project() 
repo = p.get_model_repository("credit-scoring-models")
```
This (repo, in the example above) is the model repository to which all changes must be committed.   To do this, create a new entry or record in the model repository and pass the latest model object (log_lm) as argument to initialize the model_image.

```
model_img = repo.new_model_image(log_lm)
```

Now add metadata in the form of key-value pairs. Note, you can add any metadata, just that all keys and values must be strings. 

```
model_img ["Numerical-Columns"] = ",".join(numerical_cols) #save which numerical columns were used
model_img ["Categorical-Columns"] = ",".join(categorical_cols) #save which categorical columns were used
model_img ["Test-Score"] = str(test_score) # save model score on validation/test data for this run 
```

Once all metadata has been added, a final save statement saves all data to the rorodata platform repository (with an optional comment) and closes the record
```
model_img.save(comment="built new model")
```
#### Reviewing Model Versions
To review past versions of models, model management can navigate to the local project repository on command prompt and do the following 
```
> roro models  # lists all models in the current project
logistic-regression-model

> roro models:log  #shows all the model versions in this project

Model-ID: dd97b0e1a760
Model-Name: logistic-regression-model
Model-Version: 5
Date: 2017-09-28 11:53:12.296256

        updated model

Model-ID: 65b3bb0bb78a
Model-Name: logistic-regression-model
Model-Version: 4
Date: 2017-09-27 16:17:14.008374

        updated model

Model-ID: 9b91e940609d
Model-Name: logistic-regression-model
Model-Version: 3
Date: 2017-09-27 16:09:39.390075

        updated model

Model-ID: ea6e1309fd42
Model-Name: logistic-regression-model
Model-Version: 2
Date: 2017-09-27 13:12:26.859665

        updated model

Model-ID: 2a0c435d6f00
Model-Name: logistic-regression-model
Model-Version: 1
Date: 2017-09-27 13:10:56.606315

        built new model


#You can drill into a specific model version using models:show <modelname>:<version#>
> roro models:show logistic-regression-model:1
Model-ID: 2a0c435d6f00
Model-Name: logistic-regression-model
Model-Version: 1
Date: 2017-09-27 13:10:56.606315
Categorical-Columns: grade,home_ownership,purpose
Content-Encoding: joblib
Numerical-Columns: sub_grade_num,short_emp,emp_length_num,dti,payment_inc_ratio,delinq_2yrs,delinq_2yrs_zero,inq_last_6mths,last_delinq_none,last_major_derog_none,open_acc,pub_rec,pub_rec_zero,revol_util
Test-Score: 0.811846431485
```
#### Loading a model from model management
the command `roro models` lists all models avaliable in the project repository. Any of the models can be loaded from the project's repository as follows

```
repo = p.get_model_repository(<model_name>)
model_image=repo.get_model_image()
model=model_image.get_model()
```
