 

Here is the video for the talk
<iframe width="560" height="315" src="https://www.youtube.com/embed/fqDJ5ahFRxY" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

and here is the [slides](/assets/doc/DDS-talk-short.pptx). Below are the scripts.

# 1. Lead-in
 
Hi. Today I am going to present the paper "Server-Driven Video Streaming for Deep Learning Inference". I am Kuntai Du, a first-year Ph.D. student from University of Chicago. This is a joint work with google brain.
After this ten-minutes talk, you will be able to learn about a new server-driven approach in video streaming for analytics scenario. So now, let's start!
 
# 2. Video streaming for analytics become more and more pervasive
 
Nowadays, video streaming for deep-neural-net-based analytics is more and more pervasive. For example, people use wild-life camera for the animals,
On the street, people deploy traffic camera to monitor the traffic.
People also use drone camera to efficiently collect information in a wide area.
 
As so many video streams are waiting for analytics to generate valuable insight, our goal is to scale out video streaming for analytics. To achieve this goal,
 
# 3. Design goals of video streaming protocol
 
We need to design a video streaming protocol that streams the video from the camera, through the network and to the server-side deep-neural-net, DNN for short.
To make video streaming protocol scalable, we focus on two critical design goals. First and foremost, in order to make the analytic results valuable, a video streaming protocol must preserve high inference accuracy as if the video is sending to the server-side DNN in its entirety. 
Second, a video streaming protocol should also save bandwidth.
This is because the bandwidth cost of, for example, the cellular network, will be proportional to the total data sent.
By saving the bandwidth, we can save more and more bandwidth cost as the video streaming time grows.
On the other hand, the streaming delay per frame is proportional to bandwidth usage per frame. Hence, saving bandwidth will reduce the streaming delay per frame and thus reduce the overall delay.
 
But, where is the research opportunity for us to meet these two design goals?
 
# 4. Bandwidth saving opportunity: aggressive compression
 
Luckily, video streaming for analytics, as a new type of streaming, opens new research opportunity in terms of bandwidth saving. Traditionally, the video is streamed from the video server to the human viewers, who care about the global visual quality. As shown on the left side, since every pixel contributed to the overall visual quality, the video streaming codec has to stream all pixels with nearly the same quality, including those pixels that are not relevant to the inference results, like the street and the tree.
But in our scenario, the video is streamed from the camera to the server-side DNN, which only cares about the inference accuracy. So, we can aggressively compress those pixels that are not related to the inference accuracy. In the right figure, we mark those pixels that can be aggressively compressed as grey.
 
In short, video analytics enable aggressive compression on non-object pixels, like tree pixels and street pixels.
 
However, we are not the first one who see this bandwidth saving opportunity.
 
# 5. Previous works: Camera-side heuristics
 
So now let's see how previous works explore this opportunity.
One common approach is real-time camera-side heuristics.
These approaches use real-time camera-side heuristics as a filter to drop pixels that are possibly irrelevant to the final analytic results.
Concretely, the camera first capture the frame,
and the camera runs camera-side heuristics in real-time to filter out some pixels that are irrelevant to inference accuracy.
The camera then sends the remaining part to the server. After that, the server performs DNN inference and gets the analytic results.
 
But real-time camera-side heuristics are bound to be sub-optimal. For example, let's compare the frame sent by real-time camera-side heuristics and the ideal frame that includes all object-related pixels. We see that camera-side heuristics may miss many objects, like the four objects on the top-right corner and the two objects on the bottom-left corner.
 
This is because camera-side compute is too limited to support accurate heuristics. Based on this observation, 
 
# 6. Challenge
 
We argue that, in order to be accurate, we must put the real-time heuristics on the server instead of on the camera.

 
It seems simple to meet this requirement on the first look, but after we dive in deeper, surprisingly, we find that there is a critical chicken-egg problem lying behind and make the whole thing difficult. To let the server-side DNN model drive the pipeline in real-time, the camera needs to use the feedback from server-side DNN on current video to encode the current video! How can we use the egg, feedback, to encode the chicken, video?
 
Our solution is to let the server see the video first and then iterate on how to encode the video.
 
Based on this concept
 
# 7. DDS: overall iterative workflow
 
we present DNN-Driven Streaming, DDS for short. Specifically, the camera first buffers several frames to form a video segment. Then, the camera encodes this video segment in low quality and sends it to the server. Upon receival, the server feeds this video segment to the server-side DNN and gets the inference results and the feedback regions. 
Here comes the key of DDS. Different from previous works, DDS choose to perform an iteration by re-encode the server-side DNN feedback regions in higher quality
Concretely, the server sends the feedback regions back to the camera. The camera then re-encode feedback regions in higher quality and sends the video back to the server. Last, the server feeds the resulting video to the server-side DNN and updates the inference results. With these illustration in mind,

# 8. Demo
 
let's take a look at a demo. Here, we use blue bounding box to mark the objects detected before the iteration, and use orange to mark the objects detected after the iteration. The first video shows the detection results before the iteration. The second video shows the video that DDS sent for the iteration, the third video shows the detection results after the iteration.
 
So now, let's watch some videos!
 
We can see that, when the first video misses objects, DDS will try to encode them in the second video, and ultimately recall them in the third video.
 
From this demo, we can get a sense that DDS encodes and recalls undetected objects through the iteration. Why DDS can recall the undetected objects? The secret lies in the way we generate the video sent for the iteration. To illustrate our method,
 
# 9. How DDS generates feedback regions
 
we take object detection as an example application. First, from the inference process, DDS gets all regions that may contain objects, as shown in the blue bounding boxes in the figure. These bounding boxes are generated from the intermediate output of DNN, so there is little overhead. Second, we eliminate those regions that are already confidently detected by the DNN. So in the figure, three orange bounding boxes are eliminated since DNN are already confident about them. Third, we encode these regions in a  higher quality.
In short, DDS finds the regions that are almost detected but not. This "but not" is a key differentiator between previous works and DDS. Previous works pursue a complete coverage of objects, but DDS covers the subset of objects that are not detected.
 
After introducing the technical details of DDS, 
 
# 10. Bandwidth-accuracy trade-off
 
let's take a look at experimental results. We demonstrate that DDS is able to hit a better bandwidth-accuracy trade-off. In this figure, the X-axis is the normalized bandwidth consumption, the Y-axis is the accuracy. So the top-left direction is the better direction. These ellipses show the standard deviation of DDS and the baselines. From these ellipses, we can see that DDS, outperforms the baselines. Quantitatively, DDS can save upto 59% bandwidth as well as achieve higher accuracy.

# 11. Wrap up
 
To wrap up, our contribution is that, as for bandwidth-accuracy trade-off, we need to use a real-time server-driven approach. To implement this concept, we present DDS, which uses an iterative workflow that let the server see the video first, and then iterate on how the video should be encoded. Experimental results demonstrate that DDS is able to hit better bandwidth-accuracy trade-off. Please check my personal webpage for DDS-related resources and lessons that I learned from DDS.
 
That's all, thank you.