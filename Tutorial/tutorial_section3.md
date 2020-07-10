## Section 3: Adding More Randomness

Assume we want a dataloader that repeatedly does the following:
1. randomly select one of the huge images in dataset.
2. return a 224x224 crop from a random position.

The dataloader that we made in previous section is far from the above dataloader. 
Indeed, the patches are very "localized".\
\
\
To handle this issue, a good practice is show in the below video:
<div class="myvideo">
   <video  style="display:block; width:100%; height:auto;" autoplay controls loop="loop">
       <source src="addingmorerandomnessv2.mp4" type="video/mp4" />
   </video>
</div>



