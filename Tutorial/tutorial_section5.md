## Section 5: PyDmed Process Tree
PyDmed is implemented using python multiprocessing.
You don't need to know about under the hood.
But this section provides some general information so that you can get the best performance from PyDmed
based on your machine (s).

### The hierarchy of processes
The below figure illustrates the process tree of PyDmed.

![PyDmed process tree](pydmed_process_tree_v2.png)

Each `BigChunkLoader` is a child (i.e. subprocess) of a `SmallChunkCollector`.
Once the `BigChunk` is loaded, the `BigChunkLoader` iteself is terminated. But the parent `SmallChunkCollector`
will continue working on the `BigChunk`.

Each `SmallChunkCollector` (and its child `BigChunkLoader`) correspond to one `Patient`.
Indeed, a `SmallChunkCollector` and the child `BigChunkLoader` work on a specific `Patient`.
Moreover, the dataloader makes sure that at all times at most one active `SmallChunkCollector` works on a specific `Patient`.

### Tailoring the dataloader to your machine (s)

![sample output 1](scshot_dataloader.png)

The parameters of the dataloader are managed by a dictionary called `const_global_info`.
It has the following fields:
- `const_global_info["num_bigchunkloaders"]`: an integer. Is the number of running `BigChunkLoader`s.
        According to the process tree, this number is also equal to the number of running `SmallChunkCollector`s.
- `const_global_info["maxlength_queue_smallchunk"]`: an integer. Maximum length of the queues containing `SmallChunk`s.
                            The above figure illustrates those queues: queue_1, queue_2, ..., and queue_5.
- `maxlength_queue_lightdl`: an integer. The maximum length of the dataloader's queue. In the above figure,
                             this queue is the queue that moves to right-left and collects `SmallChunk`s to send to GPU(s).
- 




