
[![button](prevsectionv3.png)](tutorial_section2.html) | [![button](nextsectionv3.png)](tutorial_section4.html)


## Section 3: Adding More Randomness

> :warning: Besides the workaround introduced below, a good practice is to set the queue lenghts to small values. Because otherwise the queues will be overwhlemed by initially-loaded bigchunks. You can check out "sample_notebooks/sample_1_train_classifier.ipynb" for typical queue lenghts (typical values for `const_global_info["maxlength_queue_smallchunk"]` and `const_global_info["maxlength_queue_lightdl"]`).  

Assume we want a dataloader that repeatedly does the following:
1. randomly select one of the huge images in dataset.
2. return a 224x224 crop from a random location on the huge image.

The dataloader that we made in previous section is far from the above dataloader. 
Indeed, the patches are very "localized".


To handle this issue, a good practice is shown in the below video:
<div class="myvideo">
   <video  style="display:block; width:100%; height:auto;" autoplay controls loop="loop">
       <source src="addingmorerandomnessv2.mp4" type="video/mp4" />
   </video>
</div>

The idea is simple: In the `BigChunkLoader`, instead of loading a very large (e.g. 5000x5000) patch, we make/return a list of
relatively smaller (e.g. 1000x1000) patches.

The `BigChunkLoader` code of previous section would change as follows.
Specially, note the lines `for count in range(5):` and `w, h = 1000, 1000`.
```python
class SampleBigchunkLoader(BigChunkLoader):
    @abstractmethod
    def extract_bigchunk(self, arg_msg):
        '''
        Extract and return a bigchunk.
        Inputs:
            - `arg_msg`: we won't need this argument for now. 
        In this function you have access to `self.patient` and some
        other functions and fields to be covered later on.
        '''
        list_bigchunk = []
        for count in range(5): #Note: for loop added
            record_HandE = self.patient.dict_records["H&E"]
            fname_hande = record_HandE.rootdir + record_HandE.relativedir
            osimage = openslide.OpenSlide(fname_hande)
            w, h = 1000, 1000 #Note: 5000,5000 changed to 1000,1000
            W, H = osimage.dimensions
            rand_x, rand_y = np.random.randint(0, W-w),\
                             np.random.randint(0, H-h)
            pil_bigchunk = osimage.read_region(location=[rand_x, rand_y],\
                                               level=0,\
                                               size=[w,h])
            np_bigchunk = np.array(pil_bigchunk)[:,:,0:3]
            bigchunk = BigChunk(\
                        data = np_bigchunk,\
                        dict_info_of_bigchunk = {"x":rand_x, "y":rand_y},\
                        patient = self.patient)
            list_bigchunk.append(bigchunk)
        return list_bigchunk
```

The `SmallChunkCollector` of previous section would change as follows.
Specially, note the line `bigchunk = random.choice(list_bigchunk)`. 
```python
class SampleSmallchunkCollector(SmallChunkCollector):

    @abstractmethod 
    def extract_smallchunk(self, call_count, list_bigchunk, last_message_fromroot):
        '''
        Extract and return a smallchunk. 
        Note that in this function you have access to 
        self.patient and some other fields/functions to be covered.
        Inputs:
            - `call_count`: not needed for now.
            - `list_bigchunk`: the bigchunks that we just extracted.
            - `last_message_fromroot`: we won't need this argument for now.
        In this function you have access to `self.patient` and some
        other functions and fields to be covered later on.
        '''
        bigchunk = random.choice(list_bigchunk) #Note: this line added.
        np_bigchunk = bigchunk.data
        W, H = np_bigchunk.shape[1], np_bigchunk.shape[0]
        w, h = 224, 224
        rand_x, rand_y = np.random.randint(0, W-w),\
                         np.random.randint(0, H-h)
        np_smallchunk = np_bigchunk[rand_y:rand_y+h, rand_x:rand_x+w, :]
        #wrap in SmallChunk
        smallchunk = SmallChunk(\
                        data=np_smallchunk,\
                        dict_info_of_smallchunk=\
                                    {"x":rand_x, "y":rand_y},\
                        dict_info_of_bigchunk = \
                                    bigchunk.dict_info_of_bigchunk,\
                        patient=bigchunk.patient
                      )
        return smallchunk
```

The return value from `BigChunkLoader.extract_bigchunk` can be of any type.
Any value that you return in `BigChunkLoader.extract_bigchunk` will be passed to `SmallChunkCollector.extract_smallchunk`.
However, the return value from `SmallChunkCollector.extract_smallchunk` is required to be either an instance of `pydmed.lightdl.SmallChunk` or None.

[![button](prevsectionv3.png)](tutorial_section2.html) | [![button](nextsectionv3.png)](tutorial_section4.html)





