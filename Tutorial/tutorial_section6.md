## Section 6: Logging to a File (for, e.g., debuggin)
As disscussed in previous section PyDmed uses multiprocessing. 
Different processes do not have synchronized access to a console or terminal.
But for debugging we often need to log some information. For this purpose, PyDmed provides the `log` function. 

We need to first make a log file for the dataloader as follows:
```python
dataloader = LightDL(fname_logfile="logfile.txt",\
                     TODO:otherarguments )
```
Now the file "logfile.txt" will be automatically created, and will contain all logged information.


We can call the function `log` everywhere. 
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
, or even from your main python process:
```python
dataloader.start()
time.sleep(10) #wait for the dataloader to load initial BigChunks.
while True:
    x, list_patients, list_smallchunks = dataloader.get()
    dataloader.log("main process got a batch of size {}.\n"\
                   .format(len(list_patients)))
    '''
     .
     .
     as before
     .
     .
    '''
    if(flag_finish_running == True):
        dataloader.pause_loading()
        break
        
```
Please note that the log file will be flusehd once you call the function `dataloader.pause_loading()`.

[![button](prevsectionv3.png)](tutorial_section5.html) | [![button](nextsectionv3.png)](tutorial_section7.html)




