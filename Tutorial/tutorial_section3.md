## Making a Dataloader
Now we are done with making an instance of `utils.data.Dataset`. Please follow the below steps to make a `utils.data.DataLoader`.
1. ***Making `const_global_info`***: ddsdf sd fsd
2. ***Making a `BigChunkLoader`***: To specify how to load a bigchunks, you should make a subclass of `lightdl.BigChunkLoader` and implement the 
abstract method `lightdl.BigChunkLoader.extract_bigchunk(self)`. 
Please note that in this function you have access to `self.patient` and `self.const_global_info`.

Here is an example of making a `BigChunkLoader`:

```python
class WSIRandomBigchunkLoader(BigChunkLoader):
    @abstractmethod
    def extract_bigchunk(self):
        '''
        Extract and return a bigchunk. 
        Please note that in this function you have access to
        self.patient and self.const_global_info.
        '''
        wsi = self.patient.dict_records["wsi"]
        fname_wsi = wsi.rootdir + wsi.relativedir
        osimage = openslide.OpenSlide(fname_wsi)
        w, h = self.const_global_info["width_bigchunk"],\
               self.const_global_info["heigth_bigchunk"] 
        W, H = osimage.dimensions
        rand_x, rand_y = np.random.randint(0, W-w), np.random.randint(0, H-h)
        pil_bigchunk = osimage.read_region(location=[rand_x, rand_y],\
                                           level=1,\
                                           size=[w,h])
        bigchunk = BigChunk(data=np_bigchunk,\
                                 dict_info_of_bigchunk={"x":rand_x, "y":rand_y},\
                                 patient=self.patient)
        return bigchunk
```
```diff
- Warning: the above code is only a sample.
Instead of returning, e.g., a 20000x20000 crop as a BigChunk, it is highly recommened to return a list of smaller BigChunks, e.g., 5 crops of size 10000x10000 in a list.
Please see the sample in TODO:pathtosample.
```
3. ***Making a `SmallChunkCollector`***:
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
