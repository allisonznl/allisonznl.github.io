---
layout: post
title: 'Modifying the Gerchberg-Saxton Phase-Retrieval Algorithm for Laser Beams containing Orbital Angular Momentum'
tags: [Gerchberg-Saxton, algorithms, phase retrieval, OAM, beam characterization, M2]
featured_image_thumbnail: assets/images/posts/2019/science_cover.jpg
featured_image: assets/images/posts/2019/l2.jpg
featured: true
hidden: true
---

This summer, I optimized the traditional Gerchberg-Saxton (GS) phase-retrieval algorithm to retrieve the phase of laser beams containing orbital angular momentum (OAM). Ultimately, my modified version of the algorithm was able to gather more information and achieve a higher resolution than traditional characterization methods using the same data set. The algorithm was used by members of my lab group to analyze their laser beams and resulted in a [publication](link) in Science Magazine. I worked with my graduate student mentor of 3 years, David Couch, but programs written are entirely my own.

![science cover]({{ a11isonliu.github.io}}assets/images/posts/2019/science_cover.jpg)

This project spun off of a project to create an M^2 beam characterization device and preliminary work was completed at the very end of last summer.

##  Importance

OAM beams are useful in optical communications, imaging, magnetics, quantum information science, and many other fields. For example, light containing OAM was shown to transmit up to 1.6 Tb of information per second-the equivalent of 8 Blu-ray discs per second. [(Source)](https://physicsworld.com/a/twisted-light-carries-data-over-1-km-in-optical-fibre/) Reliable phase detection is essential for the development of future technologies.

Beam-characterization is a necessity for all experiments involving a laser beam and phase contains powerful information that is necessary to completely characterize a laser beam. In order to completely characterize a laser beam (or any light source for that matter), we need both intensity and phase. An intensity profile is relatively easy to obtain; we can simply shine the laser on a camera which will produce a matrix of values. Phase is more complicated and involves the use of algorithms.

![simulated retrieval]({{ a11isonliu.github.io}}assets/images/posts/2019/retrieval.jpg)

## OAM beams

OAM beams are laser beams with a "twisted wavefront", meaning they contain angular momentum that is dependent on spatial distribution (as opposed to polarization- angular momentum that is dependent on the quantum spin of a photon).

![oam]({{ a11isonliu.github.io}}assets/images/posts/2019/oam_wiki.jpg)
[(Source)](https://creativecommons.org/licenses/by-sa/3.0)

## The algorithm
#### Theory
Having intensity and phase information in one plane allows the propagation of the beam to another plane. In other words, if information about phase and intensity is obtained at one point along the laser beam path, then we also have information about the laser beam at ANY other point. Furthermore, having intensity information in two different planes allows for the calculation of the phase in the two planes.
![GS background]({{ a11isonliu.github.io}}assets/images/posts/2019/GSbackground.jpg)

The original [Gerchberg-Saxton algorithm](https://en.wikipedia.org/wiki/Gerchberg%E2%80%93Saxton_algorithm) uses an image at the focal point and an image infinitely far away from the focal point to iteratively retrieve the phase of the beam.

Our algorithm uses an over-sampled intensity measurement. We take 9-30 images with at least half of the sampled points occurring within one Rayleigh range of the focal point (as recommended in the ISO standard for M^2) instead of only two. This oversampling ensures a much more robust and accurate characterization of the beam. There is an optimal solution and additional degrees of freedom that can be used to confirm the result. We also apply a phase perturbation of L = +- 1/2 every 10 iterations for the first 40 iterations, which helps the algorithm to converge more quickly. It also helps to prevent the GS algorithm from getting stuck in local minima.

![GSalg]({{ a11isonliu.github.io}}assets/images/posts/2019/GSalg.jpg)
![retrieval experimenta]({{ a11isonliu.github.io}}assets/images/posts/2019/retrieval_exp.jpg)

#### Handling Wavelength
In original GS and our main algorithm, the retrieval algorithm must know the wavelength of the laser. For a single wavelength, we found the algorithm can reliably determine the wavelength to within about 5 nm.

![perfect beam]({{ a11isonliu.github.io}}assets/images/posts/2019/wavelength_perfectbeam.jpg)
For multiple wavelengths, the algorithm does not perform very well.

![multicol beam]({{ a11isonliu.github.io}}assets/images/posts/2019/wavelength_multbeam.jpg)

![wrong wavelength]({{ a11isonliu.github.io}}assets/images/posts/2019/pretty2.jpg)
![wrong wavelength]({{ a11isonliu.github.io}}assets/images/posts/2019/pretty1.jpg)

#### Sampling on one side of the focus
For EUV OAM beams, like the in the Science article, optics have very poor efficiency. Without optics, we cannot get a full profile of the focal point of these beams. Additionally, if we can measure only planes after the focus, it is beneficial for projects in which the focal point is difficult to access, like projects where the light is focused inside a fiber. Preliminary tests have shown this looks promising.

![light fiber]({{ a11isonliu.github.io}}assets/images/posts/2019/lightfiber.jpg)
(Source: ThorLabs)

![half images]({{ a11isonliu.github.io}}assets/images/posts/2019/half_imgs.jpg)


## Comparison to Other Techniques
* **M^2** uses the same information as the GS algorithm to compute beam divergence characteristics (2 numbers)

![m2 schematic]({{ a11isonliu.github.io}}assets/images/posts/2019/m2.jpg)
* **Shack-Hartmann** is an expensive (~$4000) wavefront sensor that can measure OAM content but has low spatial resolution and relatively narrow useful wavelength range (cannot be used in VUV/EUV)

![shack hartmann]({{ a11isonliu.github.io}}assets/images/posts/2019/shackhartmann.jpg)
(Source: ThorLabs)

## Conclusion
* We have created a powerful beam characterization device that is capable of comprehensively characterizing a wide variety of beams.
* It is able to gather more information than traditional characterization methods from the same data set and achieve higher resolution
* The algorithm can determine wavelength for a single-wavelength beam
* Preliminary results show difficulty with several wavelengths and with sampling only on one side of the focus

## Next Steps
* Analyzing the algorithm performance
* Exploring the capabilities of the algorithm for multi-wavelength handling
* Optimizing for speed and accuracy, and extending this to handling images from only one side of the focus

## Code snippets


# Acknowledgements
This project was funded by STROBE and the NSF and was done as part of the STROBE SURS Program. Thank you to David Couch, Kevin Dorney, Michael Tanksalvala, Matthew Jacobs, Dr. William Peters, Prof. Margaret Murnane, Jatinder Sampathkumar, Sadie Stutzman, and Prof. Nicole Labbe.
