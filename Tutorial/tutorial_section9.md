

[![button](prevsectionv3.png)](tutorial_section8.html) | [![button](nextsectionv3.png)](tutorial_section10.html)


## Section 8: Set/Get Checkpoints

In [section 6](tutorial_section6.html) we reviewed the life cycle of `BigChunkLoader`s and `SmallChunkCollector`s.


In regular intervals:
1. The scheduler selects a patient from the dataset.
2. A `BigChunkLoader` extracts a big chunk from the patient's records. The `BigChunkLoader` has access to the patient by `self.patient`.
3. The extracted big chunk is passed to a `SmallChunkCollector`. The `SmallChunkCollector` has access to the patient by `self.patient`.


The `SmallChunkCollector`s and `BigChunkLoader`s can be reschedulled any time. 
Therefore, they may need to memorize some information or "checkpoint". For this purpose, PyDmed provideds the two functions
`set_checkpoint` and `get_checkpoint`. 

For instance, here is how a `SmallChunkCollector` can set/get the number of patches which are extracted for it's patient. 


```python
class SampleSmallchunkCollector(SmallChunkCollector):

    @abstractmethod 
    def extract_smallchunk(self, call_count, bigchunk, last_message_fromroot):
        checkpoint = self.get_checkpoint()
        if(checkpoint == None):
            #If it is the very first `SmallChunk` to be extracted from
            #  the patient, checkpoint is None.
            num_extracted_smallchunks = 0
        else:
            num_extracted_smallchunks =
                checkpoint["num_extracted_smallchunks"]
        
        '''
        .
        .
        .
        same as before 
        .
        .
        .
        '''
        self.set_checkpoint({"num_extracted_smallchunks":
                                      num_extracted_smallchunks+1})
        return smallchunk
```
Please note that each "checkpoint" is indeed associated with a unique patient, rather than the `SmallChunkCollector` or the `BigChunkLoader`.

[![button](prevsectionv3.png)](tutorial_section8.html) | [![button](nextsectionv3.png)](tutorial_section10.html)

