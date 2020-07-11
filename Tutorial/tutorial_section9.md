
[![button](prevsectionv3.png)](tutorial_section8.html) | [![button](nextsectionv3.png)](tutorial_section10.html)


## Section 9: The "send_message" Function

Assume that, e.g., our attention model wants patches from a specific location from a whole-slide-image.

In this case we can use the `send_message` function as follows:

```python
dataloader.start()
time.sleep(10) #wait for the dataloader to load initial BigChunks.
while True:
    x, list_patients, list_smallchunks = dataloader.get()
    '''
    `x` is now a tensor of shape [`batch_size` x 3 x 224 x 224].
    `list_patients` is a list of lenght `batch_size`.
     TODO: You can use these values to, e.g., update model parameters.
     .
     .
     .
    '''
    for patient in list_patients:
        dataloader.send_message(
                            patient = patient,
                            message = {"new attention":[todo_x, todo_y]}
                            )
    if(flag_finish_running == True):
        dataloader.pause_loading()
        break
```

The last message is passed to the `SmallChunkCollector` and the `BigChunkLoader` as the input arguments 
`last_message_fromroot`. Please note that this is the last message associated with the `Patient` (i.e. `self.patient`
in `SmallChunkCollector` and `BigChunkLoader`
). The message is not required to be a string and can be any picklable object. 

[![button](prevsectionv3.png)](tutorial_section8.html) | [![button](nextsectionv3.png)](tutorial_section10.html)
