---
layout: article
title: Video, slides and scripts for DDS
key: video-slides-scripts-for-DDS
---

Here is the video of my talk. Subtitle included in the video.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Bn3NpanZ1mM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Here is the [slides](/assets/doc/DDS-talk-long.pptx) of my talk.

Below is my scripts.


# 1. Lead-in

Hello, everyone. Today I am going to present the paper "Server-Driven Video Streaming for Deep Learning Inference". I am Kuntai Du, a first-year Ph.D. student from University of Chicago. This is a joint work with google brain.

# 2. Overview

Maybe some of you don't have enough time to watch the full video. So here we condense our idea into a short elevator pitch.
First, video analytics becomes more and more pervasive, but fortunately, video streaming for analytics allows for aggressive video compression on those regions that do not contain objects of interest.
We argue that only the server-side deep-neural-network (DNN for short) has sufficient information guide efficient video compression for streaming.
Based on this observation, our idea is that: we drive the video streaming protocol by the real-time feedback of server-side DNN. In other word, we advocate for a new approach for streaming, called DNN-Driven Streaming, DDS for short.

# 3. Video analytics become more and more pervasive

Nowadays, DNN-based video analytics becomes more and more pervasive. For example, people deploy wild-life camera for the animals,
On the street, people deploy traffic camera.
Last but not least, people also use drone.

So, our goal is to scale out video analytics. But how should we achieve this goal? To illustrate this,

# 4. Video needs to be streamed out for accurate analytics

Let's take a look at an example. In the following examples, we use orange to mark missed objects, and use turquoise to mark detected objects.
As shown on the left side, if we analyse the video merely on the camera, the inference results will be very inaccurate and nearly half of the objects are left undetected. This is because that the compute power of the camera is limited and can only support a cheap model.
Instead, if we stream the video from the camera to a GPU-equipped server and run inference at the server, we can deploy an expensive model at the server and detect all objects as shown on the right side.
From the comparision above, we see that limited by camera-side compute power, the camera's local inference is inaccurate. Thus, we need to stream the video to the server for accurate video analytics.

Further, to build system-level support for video streaming, 

# 5. Design goals of video streaming protocol

we need to design a video streaming protocol. The video streaming protocol streams the video from the camera, through the network and to the server-side DNN model.
We focus on two critical design goals of video streaming protocol. First and foremost, in order to make the analytic results valuable, a video streaming protocol must preserve high inference accuracy as if the video is sending to the server-side DNN in its entirety. 
Second, a video streaming protocol should also save bandwidth.
This is because the bandwidth cost of, for example, the cellular network will be proportional to the totoal data sent.
By saving the bandwidth, we can save more and more bandwidth cost as the video streaming time grows.
On the other hand, the streaming delay per frame is proportional to bandwidth usage per frame. Hence, saving bandwidth will reduce the streaming delay per frame and thus reduce the overall delay.

But, where is the research opportunity for us to meet these two requiements?

# 6. Bandwidth saving opportunity: aggressive compression

Luckily, video streaming for analytics, as a new type of streaming, opens new research opportunity in terms of bandwidth saving. Traditionally, the video is streamed from the video server to the human viewers, who cares about the global visual quality. As shown on the left side, since every pixel contributed to the overall visual quality, the video streaming codec has to stream all pixels with nearly the same quality, including those pixels that are not relevant to the inference results, like the street and the tree.
But in our scenario, the video is streamed from the camera to the server-side DNN, whcih only care about the inference accuracy. So, we can aggressively compress those pixels that are not related to the inference accuracy. In the right figure, we mark those pixels that can be aggressively compressed as grey.

Now that we see this research opportunity, how much bandwidth can we save in practice?

# 7. Potential bandwidth savings

To evaluate the potential bandwidth savings of this research opportunity, we plot the CDF of the area of objects over the area of whole frame on the videos from our dataset. From this figure, we find that objects only occupy twenty percent area of the whole frame in over fifty percent to eighty percent cases. In other word, for most of the time, we can aggressively compress over eighty percent pixels in these videos without hurting accuracy!

We are not the first one who tries to leverage this bandwidth saving opportunity.

# 8. Previous work type 1: Camera-side heuristics

So now let's take a look at how previous works save the bandwidth. We classify the previous works into two categories. The first type of prrevious works are camera-side heuristics. Specifically, the camera first capture the frame,
and the camera runs camera-side heuristics to filter out those pixels irrelevant to inference accuracy,
The camera then send the remaining part to the server. After that, the server perform DNN inference and obtain the analytic results.

However, the camera-side heuristics are sub-optimal. For example, let's compare the frame sent by camera-side heuristics and the ideal frame that includes all object-related pixels. We see that many objects are missed by camera-side heuristics. So, we conclude that the camera-side heuristics may miss many objects which will never be recovered by the server DNN.

Realizing this limitation, the other type of works instead using the server-side DNN to guide the video encoding process.


# 9. Previous work type 2: Video encoding informed by server DNN

We call this type of work as video encoding informed by the DNN. First, based on previous video, they choose a video codec configuration for the incoming video and stream it to the camera. Upon receival, the camera encode the video using the configurations and stream the video back to the server. Then the server performs DNN inference and generates the analytic results.

But these works are also suboptimal. To show this, let us compare between the frame these methods sends and the ideal frame. We can see that these methods can stream all objects, but pay way too much bandwidth on those non-object pixels like streets and trees. Also, these methods use optimal configurations by looking **previous** video content to guide the compression of **future** video content. Typically, they need several mitues to update their optimal configurations. So they cannot react to real-time video content change. But,

# 10. Why real-time content matters?

Why real-time content matters? Let's check an example. Assume that a car is driving at 5 am and thus their is no cars or people on the street. After several minutes, these methods figure out that the best configuration is to encode the video using very low quality. But suddenly, two children start to run a cross the street. Limited by the low quality, these methods cannot detect these two children, which results in accuracy 0.

OK, now let's do a recap on previous approaches.


# 11. Recap on previous works

First, due to the lack of camera-side compute power, the first type of previous works, camera-side heuristics, will filter out pixels that contain objects and thus trigger accuracy degradation. On the other hand, the approaches that leverage server-side compute only tune the video codec configurations of current video content based on previous video content. Hence, they waste bandwidth on those non-object pixels and cannot react to real-time video content, which results in high bandwidth consumption.

So these two kinds of methods all suffers from sub-optimal bandwidth-accuracy trade-off.

But, what are the reasons for this sub-optimality?

# 12. Core design choices of DDS

This is because previous works made wrong design choices. To efficiently save bandwidth, the video streaming protocol must meet two criteria. First, the video streaming protocol should be completely driven by the server-side DNN feedback. Second, the video streaming protocol should react to real-time video content.

It seems simple to meet such requirements on the first look, but their is a critical chicken-egg problem lies in these two criteria and make the whole thing difficult. These two criteria implie that the camera needs to use the server-side feedback of current video to encode current video! How can we use the egg, feedback, to edit the chicekn, video?

I hope you can pause the video and think through this a little bit.

Our solution is to let the server see the video first and then iterate on how to encode the video.

To instantiate this concept,

# 13. DDS: overall iterative workflow

we present DNN-Driven Streaming, DDS for short, which takes an iterative workflow to optimize the bandwidth-accuracy trade-off. First, the camera buffer several frames to form a video segment. Then, the camera encode this segment in low quality and send it to the server. Upon receival, the server feed this video chunk to the server-side DNN and get the inference results and the feedback regions. After that, the server send the feedback regions back to the camera. The camera then re-encode feedback regions as a video segment based on the original video segment in high quality and send the video back to the server. Last, the server feed the new video to the server-side DNN and update the inference results.

So we see that the key to DDS design is the algorithm that generates the feedback regions.

# 14. How DDS generates feedback regions

We take object detection as an example to demonstrate our algorithm. First, from the inference process, DDS gets all regions that may contain objects, as shown in the blue bounding boxes in the figure. We will tak about how to generate these regions soon. Second, we eliminate those regions that overlaps with existing high-confidence inference results. So in the figure, three orange bounding boxes are eliminated since DNN are already confident about them. The rationale is that these objects are confidently detected in the low quality and thus don't need to be reencoded again. Third, we encode these regions in high quality in a codec-friendly manner. In object detection, we just directly encode the bounding boxes since bounding boxes are pretty friendly to codec.

Now let's see how to generate regions that may contain objects. For Faster-RCNN-based neural networks, these regions can be generated from the output of region proposal network that has high confidence score.
For YoLo-based neural networks, we generate these regions from the detection bounding boxes with high confidence, where the confidence is the sum of the score of all non-background classes.

Our algorithm can generalize over different vision applications. Let's take a look at semantic segmentation as an example.

# 15. DDS can generalize to other applications. Take semantic segmentation as an example.

In semantic segmentation, we generate the regions that may contain objects by labeling each pixel with the sum of the probability of all non-background classes. In the figure, the heatmap demonstrates this probability. Brighter the pixel, higher the probability. Second, we zero out the probability of those pixels that overlaps with high-confidence inference results. So we see that in the second figure, the interior of objects are detected with high-confidence and thus are not bright anymore. Third, we encode remaining pixels in codec-friendly manner. To do so, we find the bounding boxes that contain pixels with highest average probability. In other word, we find the brightest bounding boxes. Then we encode them through the codec.

To recap, DDS find the regions where the DNN is indecisive.

After introducing DDS technique on region generation,

# 16. Comparision between DDS and closely-related works.

let's take a look at the difference between DDS and other region-based streaming methods: Vigil and EAAR.

First, these methods choose different regions to encode in high quality. DDS only encode the regions that the server-side DNN is indecisive. Vigil encodes the regions proposed from inaccurate camera-side model, and may wrongfully filter out those pixels that are actually related to objects, as shown in orange. EAAR generates regions that may contain objects from server-side DNN and use it directly. However, a lot of regions overlaps with low-quality inference results and don't need to be re-encoded again. These regions are shown in orange.

Second, DDS chooses to encode the video segment by segment to leverage video codec while Vigil and EAAR choose to encode the video frame by frame. Thus, DDS can better save bandwidth through codec-level temporal compression.

After talking about the encoding-related details,

# 17. Reduce the response delay of DDS

let's dive into response delay. Thanks to the iterative flow, most of the inference results can be returned to the camera very early. Let's see an example. First, at time 0, the camera encode the low quality video, send it to the server. The server perform inference and stream the inference results to the camera at t_one. The t_one is very small since the streaming time for low-quality video is small. Then the camera encode the high-quality video and send it to the server. After that, the server perform inference and update the inference results at t_two. We see that most of the results are returned to the camera in t_one. So in average, the response delay is close to t1, which is pretty short.

To recap, while we improve accuracy in the second inference, over 90% results are produced by the first inference and can be returned very quickly. 

After talking about the techniques, 


# 18. Experimental setup

Let's go to the evaluations. As for the dataset, we download 49 videos of various camera types and content genres. These videos include Traffic camera videos, dacham videos (from YouTube), drone videos (from VisDrone), face videos (from sitcom videos), with a total length of about twenty thousand seconds.
As for the vision applications, we evaluate object detection, semantic segmentation and face recognition. We use FasterRCNN-ResNet101, FCN-ResNet101 and InsightFace for these applications respectively. 


# 19. Bandwidth-accuracy trade-off

As for the concrete experimental results, we first demonstrate that DDS is able to hit a better bandwidth-accuracy trade-off. In this figure, the X-axis is the normalized bandwidth consumption, the Y-axis is the accuracy. So the top-left direction is the better direction. These ellipses show the standard deviation of DDS and the baselines. From these ellipses, we can see that DDS, out-performs the baselines. Specifically, DDS can save upto 59% bandwidth and achieve higher accuracy

# 20. Bandwidth savings vary with videos and queries

Now let's see how bandwidth savings of DDS vary with videos and queries. In this figure, the X-axis represents the bandwidth saving, which is the ratio of the bandwidth consumption of AWStream over the bandwidth consumption of DDS. We see that bandwidth savings varies a lot. Specifically, in our dataset, bandwidths avings varies from one to four times in differnt video. If we change the query to motorcycle only, we see that DDS could achieve much more bandwidth savings. Specifically, DDS achieves two to four times more bandwidth saving on motorcycle only compared to DDS on all objects.

# 21. Streaming delay

Now let's see how the bandwidths saving transform to streaming delay. In this figure, the X-axis is the bandwidth and the Y-axis is the streaming delay. The blue bar is DDS and the red bar is AWStream. We see that DDS saves about fifty percent streaming delay under various bandwidth.

# 22. End-to-end delay

Now let's see the end-to-end delay of DDS. In this figure, the X-axis is the number of frames per segment, and the overall height of the bar is the end-to-end delay. The blue bar is DDS and the red bar is AWStream. From this figure, we conclude that the end-to-end delay of DDS is consistantly lower than AWStream.

# 23. More details

Due to the time limit, we only present the core idea, technical details and experimental results. Please check out our paper for details about cost-delay-accuracy analysis and so on.

# 24. Wrap up

To wrap up, video streaming for analytic purposes enables aggressive video compression on those pixels that are irrelevant to inference accuracy. We discover that only the server-side DNN has sufficient information to actually guide video streaming. To instantiate this observation, we present DDS, which uses an iterative workflow driven by DNN feedback on real-time video content. Experimental results demonstrate that DDS is able to hit a better bandwidth and accuracy trade-off and lower streaming delay. Please check my personal webpage for more DDS-related resources.