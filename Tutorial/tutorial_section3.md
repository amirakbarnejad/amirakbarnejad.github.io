## Section 3: Adding More Randomness

Assume we want a dataloader that repeatedly does the following:
1. randomly select one of the huge images in dataset.
2. return a 224x224 crop from a random position.

The dataloader that we made in previous section is far from the above dataloader. 
Indeed, the patches are very "localized".


To handle this issue, a good practice is show in the below video:
<div class="myvideo">
   <video  style="display:block; width:100%; height:auto;" autoplay controls loop="loop">
       <source src="addingmorerandomnessv2.mp4" type="video/mp4" />
   </video>
</div>

The idea is simple: In the `BigChunkLoader`, instead of loading a very large (e.g. 5000x5000) patch, we make/return a list of
relatively smaller (e.g. 1000x1000) patches.

The `BigChunkLoader` code of previous section will be changed as follows.
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





