# Welcome to PyDmed Quick Start

[![button](nextsectionv3.png)](tutorial_section2.html)


## Section 1: making a Dataset

Medical imaging datasets are often messy. For instance, different modalities may be available for different patients. 
PyDmed provides the following simple yet general abstraction: 
1. `utils.data.Record`: a record that belongs to a patient. It can be, e.g., a whole-slide-image.
A `Record` includes:
    - `rootdir`: the rootdirectory of the dataset, a string, e.g., "/usr/Dataset1/"
    - `relativedir`: the relative dir with respect to the rootdir, a string like "1010.svs"
    - `dict_infos`: a dictionary containing information about the `Record`, e.g., zooming "40x", "20x", "10x", the date that the record is collected, etc.
2. `utils.data.Patient`: each `Patient` corresponds to a patient in dataset. Its fields are: 
    - `int_uniqueid`: the unique id of the patient, an integer. 
    - `dict_records`: a dictionay. It may contain key-values like "H&E":Record("1.svs"), "HER2-status":1, ect.
3. `utils.data.Dataset`: a dataset is basically a list of patients, as well as a unique name for the dataset. 
    - dataset_name: the name of the dataset, a string.
    - list_patients: a list whose elements are an instance of `Patient`.

The code for making a dataset is roughly as follows:

```python
rootdir = "/NonGit/Data/"
list_relativedirs = ["1.svs", "2.svs", "3.svs"]
list_relativedirs.sort()
#make a list of patients
list_patients = []
for fname in list_relativedirs:
    new_patient = Patient(\
                    int_uniqueid = TODO_int_id,
                    dict_records = \
                      {"H&E":Record(rootdir, fname, {"resolution":"40x"}),\
                       "HER2-status":"TODO:your label"}) 
    list_patients.append(new_patient)
#make the dataset
dataset = utils.data.Dataset("myHER2dataset", list_patients)
```

[![button](nextsectionv2.png)](tutorial_section2.html)
