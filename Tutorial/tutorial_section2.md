## Section 2: Making a Dataloader

In this scetion, we firstly introduce the two main objects in PyDmed: `BigChunk` and `SmallChunk`.

The below video illustrates the concept:

<div class="myvideo">
   <video  style="display:block; width:100%; height:auto;" autoplay controls loop="loop">
       <source src="sec2bigandsmallchunkv2.mp4" type="video/mp4" />
   </video>
</div>



1. Big chunk: is a relatively big chunk from an instnace. It can be, e.g., a 5000x5000 patch from a huge whole-slide-image.
   In the above video, `BigChunk`s are the big patches that slowly get loaded from hard disk. 
2. Small chunk: a small data chunk collected from a big chunk. It can be, e.g., a 224x224 patch cropped from a 5000x5000 big chunk.
   In the above video, `SmallChunk`s are the red patches which are pushed to queues 1-5 and are then collected for gpu. 


## Making a BigChunkLoader
To specify how to load a bigchunks, you should make a subclass of `lightdl.BigChunkLoader` and implement the
abstract method `lightdl.BigChunkLoader.extract_bigchunk`. 

Here is an example of making a `BigChunkLoader`:

```python
class WSIRandomBigchunkLoader(BigChunkLoader):
    @abstractmethod
    def extract_bigchunk(self, arg_msg):
        '''
        Extract and return a bigchunk.
        Inputs:
            - `arg_msg`: we won't need this argument for now. 
        In this function you have access to `self.patient` and some
        other functions and fields to be covered later on.
        '''
        record_HandE = self.patient.dict_records["H&E"]
        fname_hande = record_HandE.rootdir + record_HandE.relativedir
        osimage = openslide.OpenSlide(fname_hande)
        w, h = 224, 224
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
        return bigchunk
```


## Making a SmallChunkCollector:
To specify how to collect a smallchunk from a bigchunk, you should make a subclass of `lightdl.SmallChunkCollector` and implement the 
abstract method `lightdl.SmallChunkCollector.extract_smallchunk(self, bigchunk)`. 
Please note that in this function you have access to `self.patient` and `self.const_global_info`.
***The return value of `lightdl.SmallChunkCollector.extract_smallchunk` must be an instance of `lightdl.SmallChunk`.***

Here is an example of making a `SmallChunkCollector`:
```python
class WSIRandomSmallchunkCollector(SmallChunkCollector):

    @abstractmethod 
    def extract_smallchunk(self, returnvalue_bigchunkloader):
        '''
        Extract and return a smallchunk. Please note that in this function you have access to 
        self.bigchunk, self.patient, self.const_global_info.
        Inputs:
            - returnvalue_bigchunkloader: the value that you return in BigChunkLoader.extract_bigchunk(self).
        '''
        bigchunk = returnvalue_bigchunkloader
        W, H = bigchunk.data.shape[1], bigchunk.data.shape[0]
        w, h = self.const_global_info["width_smallchunk"],\
               self.const_global_info["heigth_smallchunk"]
        rand_x, rand_y = np.random.randint(0, W-w), np.random.randint(0, H-h)
        np_smallchunk = bigchunk.data[rand_y:rand_y+h, rand_x:rand_x+w, :]
        #wrap in SmallChunk
        smallchunk = SmallChunk(data=np_smallchunk,\
                                dict_info_of_smallchunk={"x":rand_x, "y":rand_y},\
                                dict_info_of_bigchunk = bigchunk.dict_info_of_bigchunk,\
                                patient=bigchunk.patient)
        return smallchunk
```


[![button](prevsectionv3.png)](tutorial_section1.html) | [![button](nextsectionv3.png)](tutorial_section3.html)



