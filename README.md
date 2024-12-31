# DYCP Machine Learning Scripts 

Developed for the dance film I'm making for my Develop Your Creative Practice grant project Points Of Origin. 

A collection of ml tools/scripts for extracting information about dancers and chaining the inputs and outputs of these processes together. For eventual use in Blender, openframeworks, Unreal Engine, After Effects and Davinci Resolve.

Eventually I want to use the depth map to make something like this
https://www.youtube.com/watch?v=etSfYfIIoSE

## Deployment
We can chain these together by making a batch script that calls everything in sequence. 
For this, we can use a watchdog script which triggers this all when a video file or series of files is moved into a folder and pulls the correct locations for the files from a yaml file so it is easy to update if I move anything.
For monitoring the completion of switchlight inference, we can use ffmpeg to work out the number of frames in the file and check against the number of files in all the folders. In the case that there is an error here somehow eg. if the number of frames doesn't match up for some reason, we can set a timeout before automatically moving onto the next stage.

In some instances, switchlight may not output for every frame. To address this, we can check for gaps in the sequence once inference is completed and run the inference for just those frames. There is a CLI for switchlight but it is paid so I'm not using it at the moment so it is a manual process at the moment to run switchlight on a folder which contains just the frames that we are missing.


## Performer Matting
Switchlight generally does a good job of this for one person but gets a little confused when there are other objects in the scene behind the performer or if there is multiple people in a scene, which is bad for me if I have multiple performers.
The nuke cattery offers a couple of options for this 

This seems the best
https://peterl1n.github.io/RobustVideoMatting/

But otherwise theres
https://github.com/PeterL1n/BackgroundMattingV2
and
https://github.com/ZHKKKe/MODNet


## Depth Estimation
There are several depth estimation models that could be used, I'm not sure which is best at the moment but here are a few options:

DepthAnything v2
https://promptda.github.io/

LeRes (maybe not temporally consistent)

Davinci (but not very high quality)

Midas (not very high quality)

Marigold

Sapiens

Depth Pro (can't get working right now, but gives us a metric depth and focal length estimation)

DepthCrafter

Adelaidepth


Ideally we want something metric so we can project the points into a scene and create a 3d surface.
Switchlight gives us a depth map as an exr file with values above the range a monitor can produce.
 
For one of the rehearsal clips with Laura it gave us a range of values from:
 427.65 to 568.6875 with 0 for areas where the alpha channel is 0.
this could be maybe centimeters (ie. 4.27 meters to 5.68 meters away from camera is plausible)


In any case, it would be useful to have a scale factor and an offset so we can tweak it for specific frames and animate over time in Unreal/Blender.
Displacing a plane to give the illusion of 3d depth is straightforward but isn't what we really want. What we REALLY want is to project rays out from the camera according to the FOV and aspect ratio of the image.

## Segmentation

Sapiens does a good job of this but annoyingly the pytorch sapiens inference installed from pip only works well for images that are at a size of 1024h x 768w which is a portrait format, clearly not ideal for landscape filmmaking. 
It does not work well for images where the body has been cropped off.

The gradio app seems to do a much better job of this bizarrely so I need to investigate.

https://github.com/ibaiGorordo/Sapiens-Pytorch-Inference

https://huggingface.co/spaces/facebook/sapiens-seg

## Normal Estimation

Switchlight does a great job of this, assuming its background removal has worked correctly. I haven't had much luck with the python package **sapiens pytorch inference** for getting usable normal maps, maybe meta's official repo will give better results. 

Normality is wonderful (and free!) in After Effects for working with these normal maps, specifically for relighting with positioned lights, rim lighting, refraction and refraction, matcaps.


## Pose estimation

Sapiens

Rokoko video

posenet

mediapipe

openpose

## 3d model estimation

The issue with our effects so far is they are great at getting information that is visible from the current camera pose, however if we want to get true 3 dimensional effects, it is handy to have a surface on the areas that are hidden from a front-on camera.
There are a few ways to do this:

Rig a 3d model, this could be a generic model or one tweaked to the dimensions of the performer (ie. how the St Vincent Broken Man music video was done before houdini pyro sim), could use mixamo to rig this or blender autorig

Use 4Dhumans to create full 3d model, there is a blender plugin for this, so far I have used a google colab to work out poses from still images. I understand you can get a pkl out of the python script.
The problem is we get a generic model that is stuck in jazz hands.


