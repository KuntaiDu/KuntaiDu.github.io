---
layout: article
title: Tips on plotting figures like DDS
---

## Why you may need these tips?

Let's take [AWStream](https://awstream.github.io/paper/awstream.pdf) as an example:

<img src="/assets/2020-06-27/awstream1.jpg" width="40%">

And this is one similiar figure from DDS:

<img src="/assets/2020-06-27/dds1.jpg" width="40%">

which one would you like better?

Also, this is one of the figure from [EAAR](http://www.winlab.rutgers.edu/~luyang/papers/mobicom19_augmented_reality.pdf):

<img src="/assets/2020-06-27/eaar1.jpg" width="40%">

And this is one similiar figure from DDS:

<img src="/assets/2020-06-27/dds2.jpg" width="40%">

which one would you like better?

In general, well-designed figures could bring some extra benefits. It could deliver the information in a **clearer** way, make the work feel **well-polished** and also indicate that you have a good taste. So, let's start!

## Deliver simple correct message

First and foremost, the main function of the figures is to deliver the information. Don't compromise the true information, even when doing so will make the figure prettier.

For example, one of the previous version of this figure:

<img src="/assets/2020-06-27/dds3.jpg" width="40%">

is like this:

<img src="/assets/2020-06-27/dds4.jpg" width="40%">

Though the latter is definitely more good-looking than the former (since in the former, the textures of the bars are not the same), it did not deliver the message that DDS is worse than AWStream if AWStream does not profile, and DDS is better when AWStream profiles. In this case, we need to choose the former one.

Let's look at another example. This is one previous version that explains what is early reporting:

<img src="/assets/2020-06-27/dds5.jpg" width="40%">

and after several iterations, this figure becomes:

<img src="/assets/2020-06-27/dds6.jpg" width="40%">

These two figures are all delivering correct messages. The difference is that the first figure tries to deliver **too much** messages, and the second figure just illustrates how the camera combine the inference results. In general, delivering too much messages will make the figure feel overwhelming, and will **force** your readers to slow down and reexamine the figure again and again (reread the former figure and check if this align with what you will react :stuck_out_tongue_closed_eyes:). In this case, most people may just skip this figures, or even skip the whole paper. So it is also critical to keeep the message simple. The simple information will be the most effective information.

## Fast iteration

For drawing the figures, another critical suggestion is to iterate fast. For example, in this figure:

<img src="/assets/2020-06-27/dds1.jpg" width="40%">

Imagine you are going to annotate the figure with all these texts. 