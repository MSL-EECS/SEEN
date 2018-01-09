# **QoE Testbed for spherical video** 

Welcome to the repository for extra content concerning "Gaze-aware (or Foveated) Content Delivery for VR(https://mega.nz/#F!FuwCzAQA!6_satN2CuiImlUiFYLdykw).

This repository contains the QoE testbed for spherical video made for the Smart Eye-tracking Enabled Networking (SEEN) project, a joint project by KTH, Tobii and Ericsson.

The testbed allows playing spherical videos as if they were being streamed through SEEN's proposed optimized streaming system. The system will stream tiled videos, using eye-tracking to select which tiles should be sent on a higher quality. This testbed simulates this streaming locally, in order to study how it is subjectively perceived by users. After playing a video it generates files that can be used to calculate how much data would need to be sent through the network in a full system.

## **Repository structure**
This repository does not use branches as usually used with Git. Each branch contains a different version of the testbed, not meant to be merged into the the master branch. The master branch holds the main video player. Other branches include a player for images, a player without any streaming simulation (used to show the unimpaired videos) and players that don't use eye-tracking, only viewport tracking.

**Command line interface**
> 360Player.exe [videoName] [coneFile] [rttFile] [background] [tilesFile] [directionThreshold] [useCenter] [useLatestDirection]

> Parameters:
>   videoName          : Name of the video to be played. Must correspond to the
>                        name of a directory inside bin/data/videos.
> 
>   coneFile           : File with the description of the cone used to select
>                        the high quality tiles, without .txt extension.
> 
>   rttFile            : File with the description of the RTT to be used during
>                        playback. Must correspond to the name of a file in bin/data/rtts.
> 
>   background         : Background resolution to use in MxN format.
> 
>   tilesFile          : File with the description of the layout of the tiles,
>                        without .txt extension.
> 
>   directionThreshold : Threshold, in degrees, used for stabilization of the
>                        foreground region. Tiles are only removed from the
>                        foreground region if the angular distance from the current
>                        gaze direction to the last recorded reference direction
>                        surpasses this threshold (the gaze direction will then be
>                        used as the next reference).
> 
>   useCenter          : 0 to use eye-tracking, 1 to track only the center of
>                        the viewport.
> 
>   useLatestDirection : 0 to use only the recorded reference direction to
>                        select the tiles in the foreground region (less bandwidth,
>                        worse QoE), 1 to use the latest gaze direction.

Once the video is playing, the playback can be stopped before it ends using the escape key, the 't' key shows which tiles are active, and the 'd' key shows where the cone vectors are hitting the sphere.

**Files needed**
The testbed needs a set of files in the bin/data folder to work. A sample data folder was added in the samples folder of this repository, but the video files are empty.

- Videos: videos each have their own folder inside bin/data/videos, with subfolders for every tiling, and a separate video for each available background. The path to a specific file looks like bin/data/myvideo/30x15/myvideo_960x540.mp4. The resolution for the high quality must be 3840x2048.

- Cones: a discrete approximation of a cone consistent of several vectors, is used to find out which tiles should be in high quality. A file describes the vectors: on the first line, the aperture angle for the vectors, and on the second line, the number of vectors for that angle. Several angle/number combinations can be specified in the file, separating them with ','. So if coneFile is 20, the file used is bin/data/cones/20.txt.

- RTTs: network round trip times are specified on a similar way: first line shows the round trip time, and the second one the limit of the time period for which that RTT is used. Both values are in seconds. If using rttFile some-values.csv, the file path used is bin/data/rtts/some-values.csv.

- Tiles: The layout of the tiles is specified in a similar way. First line marks the horizontal splits, and the second line the vertical splits. The limit of the frame is included in both cases, so a tiling file may look like:

> 256,512,768,1024,1280,1536,1792,2048,2304,2560,2816,3072,3328,3584,3840
> 192,384,576,768,1024,1216,1408,1600,1792,2048

If the specified argument is tilesFile 15x10, the corresponding file must be at bin/data/tiles/15x10.txt

**Output**

This testbed outputs two kinds of files:

- data.csv: This file contains the information necessary to calculate how much data needs to be transmitted. It includes the frame number, the frame timestamp the time the gaze-point was captured, the gaze point in the 2D space, and the number of each foreground tile used for the frame.

- extra.csv: contains other data obtained from the HMD and the eye-tracker. In summary, columns 0-10 (inclusive) contain the times the gaze sample is available for use, the time it was recorded, head position, and gaze and viewport direction, columns 11-25 contain the data for the left eye, and columns 26-40 the data for the right eye. The data for each eye includes: gaze origin validity (boolean), gaze origin (3 values, millimeters), gaze direction validity (boolean), gaze direction normalized (3 values), pupil diameter validity (boolean), pupil diameter in millimeters, eye openness validity (boolean), eye openness, pupil position in sensor area validity (boolean), and pupil position in sensor area (2 values). Boolean values are stored as 0 (false) or 1 (true).

**Installation**

This project can only be installed on Windows, and has only been tested in Windows 10 64-bits. After compilation, you can take the binaries to other Win10 x64 computers fairly easily.

**Compilation**

This testbed was built using OpenFrameworks, last tested commit was 2c7b719bb94ac96f7f13802596dd3e09ead44696. Follow the OpenFrameworks documentation for how to install the toolkit. You'll need to install the K-Lite codec pack Full 12.10.

For eye-tracking you need to install the Tobii Eye-tracking Core Software. Last version tested was 2.9.0.6265. This software used the Tobii Stream-Engine API. You will need to copy the binaries for this API in the bin folder. The src folder already includes the necessary header files.

We interact with the HMD using OpenVR. Last version tested was 1.0.6, commit 7fa6470f2972970859f2395f1390f6d87d4b0fc3. On runtime, OpenVR depends on SteamVR, which can be downloaded and installed through Steam. The OpenVR header files are already included in the src folder, so you just need to drop the precompiled binaries in bin.

After all dependencies are met, you can download this repository into <yourOpenFrameworksRoot>/apps/myApps/<name for the project>. Add the binaries mentioned above to bin and you should be able to compile it using Visual Studio 2017 (last tested version 15.2).

**Moving the binaries**

You can install in a different computer by simply copying over the bin folder, as long as it has the same operating system and CPU architecture. You will need to install SteamVR, the Tobii Core Software and the K-Lite codec pack, because they are necessary at runtime.

**Other versions of the testbed**

For documentation on the other versions of the testbed, consult the README.md file in the corresponding branch.

**Content preparation and cost calculation**

Once the content is prepared and stored in the bin/data folder, the testbed can be used independently, invoking it for the command line. However, we have built several scripts for cost calculation and content. You can consult the documentation here:

- Content preparation

- Cost calculation

**About the code**

The testbed follows the usual architecture for OpenFrameworks projects: the main file just creates and runs an ofApp object. This object includes an implicit event loop that runs the methods defined in ofApp.cpp as fast as it cans. If you want to get a general idea of the code, thats the place to start.

The communication with the headset is done using a modified version of the ofxOpenVR addon. The addon is released under the MIT license (Copyright (c) Hugues Bruyère). The code for the addon is in ofxOpenVR.cpp, ofxOpenVR.h, CGLRenderModel.cpp and CGLRenderModel.h. It depends on the OpenVR header and JSON file.

The code that deals with the eye-tracker is in GazeStream.cpp and GazeStream.h. It depends on the Tobii headers. It setups the connection to the tracker and stores all the samples, and deals with the RTT simulation.

3DGazeApproximation.cpp contains the functions and classes used to transform the eye-tracker data into useful directions in the 3D space and points in the 2D frames. utils.cpp contains other miscellaneous functions to read our textual formats, write eye-tracker samples to disk, etc.
