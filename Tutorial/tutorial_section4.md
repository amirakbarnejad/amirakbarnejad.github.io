


[![button](prevsectionv3.png)](tutorial_section3.html) | [![button](nextsectionv3.png)](tutorial_section5.html)

## Section 4: Handling Label Imbalance

In medical datasets, classes are often highly imbalanced. 
For instance, it may so happen that only 5% of instances belong to class 1 and the remaining 95% belong to class 0.

This is an issue for machine learning methods:
1. When splitting the dataset to train/validation/test sets, one split may not have any instances from a class.
2. When training the model, the fed mini-batches may contain long bursts of the majority class and rare occurrences of the minotrity class.
This can prevent the model from learning a meaningful pattern.

We discuss how to handle the above issues in the following two subsections.

### Splitting Dataset While Having Label Imbalance
After creating a `Dataset`, you can split it by calling the function `Dataset.labelbalanced_splits_from`. 
By doing so, each split (e.g. training set) will have an equal share (e.g. 70%) from different classes. 
```python
#first let PyDmed know how to get a Patient's label.
def func_getlabel_of_patient(patient_input):
    '''
    This function has to return the label of the sample input patient. 
    These labels will be used to make the splits balanced. 
    '''
    return patient_input.dict_records["her2score"]

#now we should pass the above function to the split function
ds_train, ds_val, ds_test =\
                pydmed.utils.data.Dataset.labelbalanced_splits_from(
                    dataset=dataset,
                    percentage_partitions=[60, 10, 30],\
                    func_getlabel_of_patient = func_getlabel_of_patient,
                    verbose=False
                 )

```

### Training a Model While Having Label Imbalance
It might be desired to have label-balanced batches in training phase. 
Because otherwise the fed batches may contain long bursts of the majority class(es)
and rare occurences of minority class(es).
To prevent this issue, you can use PyDmed's `LabelBalancedDL` class as follows:
```python
#first let PyDmed know how to get a Patient's label.
def func_getlabel_of_patient(patient_input):
    '''
    This function has to return the label of the sample input patient. 
    The mini-batches will be balanced with respect to these labels. 
    '''
    return patient_input.dict_records["her2score"]

#now we should pass the above function to the dataloader. 
dl = pydmed.extensions.dl.LabelBalancedDL(
            func_getlabel_of_patient=func_getlabel_of_patient,\
            "... other args same as those of the normal LightDL."
        )

```  


[![button](prevsectionv3.png)](tutorial_section3.html) | [![button](nextsectionv3.png)](tutorial_section5.html)

