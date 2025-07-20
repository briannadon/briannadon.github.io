---
layout: post
title:  "Where is digital amp modelling and tone capture headed?"
date:   2025-07-17 16:08:00 -0700
categories: audio
author: Brian Nadon
---

Yes, my first real post is going to be about music, not biology! Oh well.

# Where we were

For decades, true guitar tone snobs turned their nose up at the thought of turning their guitar signal into a bunch of *ones and zeroes* to make sound. "It just doesn't have that *real tube feel*", they'd say.

But the foundational research most contemporary digital guitar amplifier modeling is based on was done in the 1980s, and the first acceptable "pro-level" modeling units hit the mass market in the [early 1990s](https://www.vintagedigital.com.au/roland-gp-100/). As the 2000s rolled in, these became cheaper and more popular. This is approximately when the much reviled "red bean pods" gained their ([unfair](https://www.youtube.com/watch?v=FXdwsktHa6k)) reputation. I personally spent much of my teenage years noodling through a [Digitech RP-100A](https://www.manualslib.com/manual/380807/Digitech-Rp100a.html). I even played a few live shows through a red bean pod (I think the Pod X3) hidden behind a stack of cabinets - which got me a surprising number of compliments from audience members who were none the wiser. Despite popular opinion, even as early as the 2000s, these modelers were plenty "good enough" for working musicians.

# Why the mystique?

So what's really going on in these modelers? Frankly, nothing too complicated. There are really only two kinds of elements in a guitar's signal chain: linear (e.g. EQ) and non-linear (compression, distortion). Linear effects are very easy to "model", nonlinear not so much. However, even as early as the 1980s as noted above, methods to emulate saturation and distortion as seen in vacuum tube circuits were well-established. So when we ask the question "how *good* is this digital modeler?" we're *really* asking "how accurate is the analog nonlinear distortion modeling", because everything else is fairly straightforward.

So how *do* we model analog distortion in the digital domain? Well, I'm no DSP expert, but [this paper](https://ccrma.stanford.edu/~dtyeh/papers/yeh07_dafx_distortion.pdf) explains it pretty well. Here is a simplifed diagram of what a basic distortion pedal is doing under the hood from that paper:

![Block diagram of distortion pedal](/assets/images/posts/image1.png). 

The gist is that even though the nonlinear clipping portions of the pedal are more difficult to model, they're doable with good accuracy and good performance. 

These building blocks have been the basis of modeling software and hardware for many years now. With smart circuit modeling and analysis, engineers can accurately recreate the linear and nonlinear behavior of amps, including the elusive "touch sensitivity" so many guitarists crave.

I think part of the confusion around, and preponderance of magical beliefs about, modeling analog audio signals come largely from marketing in the audio world. Obviously if you are a large company and have a huge amount of capital invested in making vacuum tube-based amplifiers, you are going to want to convince people they don't want the new, cheaper, highly accurate technology. 

# The new hotness: AI

Everyone's heard of neural networks and machine learning (or "AI" if you're chasing VC money) by now, so if you haven't, [here's a primer](https://www.slideserve.com/aulii/neural-networks-primer-powerpoint-ppt-presentation).  Nonlinear saturation and distortion are great targets for machine learning: they're very easy to sample (AI/ML relies on huge sample sizes), and they represent systems that can be challenging to model perfectly accurately using first principles - though it can be done as mentioned above.

I think [Hotone's white paper](https://hotoneaudio.oss-cn-shenzhen.aliyuncs.com/prod/support/CDCM%20HD_White%20Paper_V01_200323.1584955064392.pdf) breaks down the state of play on using AI/ML in amp modeling well. There is no need to use ML methods on linear components like EQ, but it's well-suited to model distortion. Newer hardware like powerful GPUs and, in Hotone's case, SHARC DSP chips, allow for very fast and efficient processing of guitar signals through machine learning models to get novel output (i.e. a realistic guitar amplifier tone).

Then, [Neural Amp Modeler](https://www.neuralampmodeler.com/) came along and shook things up. Steven Atkinson showed you could use off-the-shelf machine learning libraries to match guitar amplifier characteristics extremely accurately in real-time. What's more, he open-sourced it, so anyone could benefit from his work.

The community really took off with NAM in a way I never expected - usually open source tools like this don't fare well in the audio world due to the magical marketing beliefs I discussed earlier, but this seems to be an exception. To date, Hotone, DimeHead, and NeuralDSP (among others) now support NAM models in their hardware or software.

# The catch

Neural Amp Modeler has one issue: it is very CPU-intensive, and relies on a fairly large neural network. It's tough to pack a full-powered x86 or ARM processor into a pedal or rack-based form factor (and not have it melt), so most companies opt for purpose built DSP chips (like the SHARC mentioned earlier) that are very efficient and generate little heat. To date, I believe the only *pedal* form-factor product that can load full-size NAM models is the DimeHead. 

All other manfacturers "convert" models to their own proprietary format (e.g. their own smaller neural network architecture). I suspect that this "conversion" is actually a retraining: I think these companies are taking the NAM model, sending pre-computed test/training signals (usually sine sweeps combined with some other reference sounds - remember, we need to model linearity and nonlinearity), getting the "output" from the NAM model, and training *their* model to learn how to do what the NAM model did to the test signal. Intuitively, you probably know or suspect that training a neural network on the output of another neural network introduces some loss of quality. My personal experience, and that of many others, is exactly this: [these conversions introduce some loss of model accuracy.](https://www.youtube.com/watch?v=PRN7KeDtScI).

# The solution?

Maybe in the future we'll see some of these manufacturers introduce some kind of [model distillation](https://medium.com/data-science-in-your-pocket/what-are-deepseek-r1-distilled-models-329629968d5d) to more accurately convert NAM models. I think this is probably the next frontier here.

Alternatively, fully ARM-based solutions that run on a basic OS like [PiPedal](github.com/rerdavies/pipedal) might flip everything on its head: what if we stopped even thinking about "pedals" and just loaded up plugins on a basic OS host?

The future is bright indeed.
