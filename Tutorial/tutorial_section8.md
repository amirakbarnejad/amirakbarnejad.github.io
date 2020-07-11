## Section 8: Call Count

In [section 5](tutorial_section5.html) we reviewed the life cycle of a `BigChunkLoader`s and `SmallChunkCollector`s.


In regular intervals:
1. The scheduler selects a patient from the dataset.
2. A `BigChunkLoader` extracts a big chunk from the patient's records. The `BigChunkLoader` has access to the patient by `self.patient`.
3. The extracted big chunk is passed to a `SmallChunkCollector`. The `SmallChunkCollector` has access to the patient by `self.patient`.


The `SmallChunkCollector`s and `BigChunkLoader`s can be reschedulled any time. 

```python
class SampleSmallchunkCollector(SmallChunkCollector):

    @abstractmethod 
    def extract_smallchunk(self, call_count, bigchunk, last_message_fromroot):
        checkpoint = self.get_checkpoint()
        if(checkpoint == None):
            #If it is the very first `SmallChunk` to be extracted from
            #  the patient, checkpoin is None.
            num_extracted_smallchunks = 0
        else:
            num_extracted_smallchunks =
                checkpoin["num_extracted_smallchunks"]
        
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

[![button](prevsectionv3.png)](tutorial_section7.html) | [![button](nextsectionv3.png)](tutorial_section9.html)
