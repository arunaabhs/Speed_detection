# Python/OpenCV Speed Detector #

This is a program that uses OpenCV to calculate cars' speeds from a traffic cam livestream.

### How it works ###

This demo gif will be referenced multiple times in the explanation, so I'll just leave it here.

![Example](./demo.gif)

#### Cropping ####

The first step in the pipeline involves removing irrelevant areas from the video. As shown in the demo GIF, some regions—such as areas with motion that should not be considered for vehicle detection—are excluded. These cropped regions can be manually selected during runtime by clicking and dragging on the "Source Image" window. When the 's' key is pressed, the selected regions are saved to settings.json and automatically applied on startup.

#### Vehicle Detection ####

Now that the unwanted areas are removed, we can use computer vision to isolate the vehicles (after all, that's what we really care about!). 

I use KNN background subtraction and morphology to isolate the vehicles and detect their contours. I'm not going to explain too much since these are default OpenCV functions, but you can see how I use them in the first part of `process_frame()` and `filter_mask()` in `main.py`.

#### Vehicle Tracking ####

To calculate vehicle speed, it’s necessary to track vehicles across multiple frames. While individual frames can show vehicle positions, tracking provides the temporal continuity needed for motion analysis. The program compares current detections to those from previous frames using heuristics and configurable parameters to determine valid motion and ensure accurate tracking.

#### Speed Calculation ####

The program supports two modes of speed calculation:

Distance Mode: Requires a predefined "real-world distance" (e.g., the physical length of the road in the video). Speed is calculated using this distance and the time a vehicle is visible on screen.

Average Mode: Computes the average speed of a sample set of vehicles in pixels per second. New detections are then compared to this average, with results reported as percentage differences. This mode is particularly useful when the actual road length is unknown.

Speed is calculated once a vehicle crosses a user-defined detection line (e.g., the light blue line shown in the demo GIF). The position and orientation of this line can be customized for different scenarios.

#### Settings ####

The application is designed to support a variety of video sources (referred to as "roads"). Configuration data for each road is stored in settings.json, including:

1. Video source URL

2. Detection line coordinates and orientation

3. Cropped regions

Refer to the settings.json file for the specific structure and examples of these configurations.

### Improvements ###

While my program works decently well, there are some things I'd like to work on, namely:
* A manual livestream loader to reduce lag/random jumps (it would need to be manual because it needs to access [.m3u8 metadata](https://tools.ietf.org/html/rfc8216#section-4.3))
* Lane detection so that this program could work on multi-lane roads
