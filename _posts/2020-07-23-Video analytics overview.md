---
layout: article
title: Overview on video analytics
key: Video-analytic-overview
---

Here is a small literature review with a focus on system for **video inference**.

## Single video stream

#### [Vigil MobiCom '15](https://old.sigmobile.org/mobicom/2015/papers/p426-zhangA.pdf)

#### [Glimpse SenSys '15](http://people.csail.mit.edu/yuhan/doc/sen060-chenA.pdf)

#### [WEG CVPR '17](http://haneul.github.io/papers/cvpr2017.pdf)

#### [NoScope VLDB '17](https://www.vldb.org/pvldb/vol10/p1586-kang.pdf)
This paper targets object detection. It says that by biasing the model to current video and specific classes, the model could be much more cheaper.

#### [Neurosurgeon ASPLOS'17](https://www.cl.cam.ac.uk/~ey204/teaching/ACS/R244_2019_2020/papers/kang_asplos_2017.pdf)

#### [AWStream SIGCOMM '18](https://awstream.github.io/paper/awstream.pdf)
Tune configurations of encoder to adapt to video dynamics.

#### [Chameleon SIGCOMM '18](https://people.cs.uchicago.edu/~junchenj/docs/Chameleon_SIGCOMM_CameraReady_faceblurred.pdf)
Tune configurations to adapt to video dynamics.

#### [CloudSeg HotCloud '18](https://www.usenix.org/system/files/hotcloud19-paper-wang.pdf)
This paper uses super resolution to save bandwidth.
It trains a super resolution model based on some ground truth by minimizing analytic error, and then uses this model to super-resolute the input video clip for analytic purpose.

#### [DeepDecision INFOCOM '18](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8485905)

#### [Neural Networks Meet Physical Networks: Distributed Inference Between Edge Devices and the Cloud HotNets '18](https://dl.acm.org/doi/pdf/10.1145/3286062.3286070)

#### [Bandwidth-efficient Live Video Analytics for Drones via Edge Computing SEC '18](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8567664)

#### [AdaScale MLSys '19](https://arxiv.org/pdf/1902.02910.pdf)

#### [Edge Assisted Real-time Object Detection for Mobile Augmented Reality MobiCom '19](http://www.winlab.rutgers.edu/~luyang/papers/mobicom19_augmented_reality.pdf)
This paper explores AR scenario, so the key thing here to minimize the latency.
This paper uses parallel streaming and inference to reduce latency, motion-vector-based tracking & ROI encoding to reduce bandwidth (thus reduce streaming latency), and adaptive offloading to reduce compute cost.

#### [Cracking open the DNN black-box: Video Analytics with DNNs across the Camera-Cloud Boundary HotEdgeVideo '19](https://www.microsoft.com/en-us/research/uploads/prod/2019/08/Split-brain_HotEdgeVideo19.pdf)


#### [DSL INFOCOM '19](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8737614)

#### [DDS SIGCOMM '20](https://kuntaidu.github.io/assets/doc/DDS.pdf)
This paper uses server-driven approach to encode the video in different spatial quality for bandwidth saving purpose.

#### [Reductro SIGCOMM '20](http://web.cs.ucla.edu/~harryxu/papers/li-sigcomm20.pdf)
This paper extracts **cheap features**, exameine the frames sequentially and filter out those frames that don't change these features much.

#### [ELF Mobicomm '21](https://www.msra.cn/wp-content/uploads/2021/03/mobicom21-elf.pdf)
This paper partits the frames into several slices and offload them to different server.

#### [CiNet SIAM '21](https://tianguo.info/project/mobile-deep-inference/cinet_sdm21.pdf)


## Multiple camera video stream

#### [Vigil MobiCom '15](https://old.sigmobile.org/mobicom/2015/papers/p426-zhangA.pdf)

#### [Chameleon SIGCOMM '18](https://people.cs.uchicago.edu/~junchenj/docs/Chameleon_SIGCOMM_CameraReady_faceblurred.pdf)

#### [Caeser SenSys '19](https://nsl.usc.edu/wp-content/uploads/2019/09/Caesar_SenSys19.pdf)


## Optimize encoding

### Auto-encoder-based approaches

These are mainly works from computer vision community.

#### [Video coding for Machine IEEE Image Processing '20](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9180095)

#### [NLAM AAAI '20](https://arxiv.org/pdf/1912.06348.pdf)

#### [Rate-distortion autoencoder ICCV'19](https://arxiv.org/pdf/1908.05717.pdf)

## Scale up inference

### Through building effective library/database

The key research topic here is how to choose correct API set and provide clever implementation to save unnecessary cost.

#### [Starfish MobiSys '15](http://roblkw.com/likamwa2015starfish-mobisys.pdf)
This paper argues that multiple applications will reuse the same video and invoke the same library. So one could cache the result for future use.


#### [Optasia SoCC '16](https://dl.acm.org/doi/pdf/10.1145/2987550.2987564)
This paper aims to support and optimize relational queries.
It tears specific video analytic task to four parts: extractors, processors, reducers and combiners, and then conduct optimization based on it.

#### [Scanner TOG '18](http://graphics.stanford.edu/papers/scanner/poms18_scanner.pdf)
It expresses the analytic pipeline as a static computation graph, bound the temporal dependency through pre-defined API, and scale the pipeline to a cluster.

#### [Focus OSDI '18](https://www.usenix.org/system/files/osdi18-hsieh.pdf)
This paper tries to detect all frames that contains certain objects. They use cheap models to reduce ingest-time cost, and identify same/similiar objects to reduce query-time latency.


#### [DeepLens CIDR '19](http://cidrdb.org/cidr2019/papers/p40-krishnan-cidr19.pdf)
This paper says that each step of video analytic pipeline will map from a stream to another stream.
Based on this abstraction, they explore how to encode, index the vido; and how to reuse previous results.

#### [Blazelt VLDB '20](https://cs.stanford.edu/~matei/papers/2020/vldb_blazeit.pdf)


### Through resource management

#### [MCDNN MobiSys '16](https://homes.cs.washington.edu/~arvind/papers/mcdnn.pdf)

#### [VideoStorm NSDI '17](https://www.usenix.org/system/files/conference/nsdi17/nsdi17-zhang.pdf)

#### [NestDNN MobiCom '18](https://dl.acm.org/doi/pdf/10.1145/3241539.3241559)



## Scale up training

#### [Mainstream ATC '18](https://www.usenix.org/system/files/conference/atc18/atc18-jiang.pdf)
Here is the [paper link].
This paper argues that people train multiple models based on same backbone on same videos.
Thus, the backbone could be shared between different models.

## Dataset and benchmarking

#### [Visual Road SIGMOD '19](https://db.cs.washington.edu/projects/visualroad/p300-haynes.pdf)
This paper generates synthetic videos through computer graphics for benchmarking purpose.

#### [Yoda SEC'21](https://arxiv.org/pdf/2105.08694.pdf)
This paper collects a large set of videos that highlights the pros and cons for video anlaytic pipelines.