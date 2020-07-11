## Section 6: Logging to a File (for, e.g., debuggin)
As disscussed in previous section PyDmed uses multiprocessing. 
Different processes do not have synchronized access to a console or terminal.
But for debugging we often need to log some information. For this purpose, PyDmed provides the function `log`. 

We need to first make a log file for the dataloader as follows:
```python
dataloader = LightDL(fname_logfile="logfile.txt",\
                     TODO:otherarguments )
```
Now the file "logfile.txt" will be automatically created, and will contain all logged information.


Note that you can call the function `log` everywhere. 
For instance, in `SmallChunkCollector`
```python
class SampleSmallchunkCollector(SmallChunkCollector):

    @abstractmethod 
    def extract_smallchunk(self, bigchunk, last_message_fromroot):
        
        self.log("in time {} a SmallChunk loaded.\n".format(time.time()))
        '''
        .
        .
        same as before
        . 
        .
        '''
        return smallchunk
```

, or in `BigChunkLoader`

```python
class SampleBigchunkLoader(BigChunkLoader):
    @abstractmethod
    def extract_bigchunk(self, arg_msg):
        self.log("in time {} a BigChunk loaded.\n".format(time.time()))
        '''
        .
        .
        same as before
        . 
        .
        '''
        return bigchunk
```




