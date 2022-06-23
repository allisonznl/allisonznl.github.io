---
layout: post
title: 'Creating an Improved Dataset for Solar Flare Prediction using Generative Adversarial Networks (GANs)'
tag: Machine Learning
featured_image_thumbnail: assets/images/posts/2021/GAN-solarflare/images.png
featured_image: assets/images/posts/2021/GAN-solarflare/images.png
featured: true
excerpt_separator: <!--more-->
---

Currently, I am working to augment solar magnetogram data using GANS to improve solar flare prediction models. It is incredibly important to be able to predict solar flares accurately, as they affect the Earth’s magnetic field which can disrupt the power grid, GPS and radio communications, and harm astronauts in space. <!--more--> This work is being done with the Laboratory for Atmospheric and Space Physics [LASP](https://lasp.colorado.edu/) and the Space Weather Technology and Research Center [SWxTREC](https://www.colorado.edu/spaceweather/) at the Univ. of Colorado Boulder.

I presented this work at the American Geophysical Union conference in December 2021 and at the Machine Learning in Heliophysics Conference, held at CU Boulder this March 2022.

<section class="download-box inner">
	<div class="download-box-links">
	    <a href="https://doi.org/10.1002/essoar.10510080.1" target="_blank">Link</a>
	    <a href="/assets/documents/POSTER_AGU2021_LiuA.pdf" target="_blank">AGU 2021 Poster</a>
	</div>
</section>

<section class="download-box inner">
	<div class="download-box-links">
	    <a href="https://doi.org/10.1002/essoar.10510080.1" target="_blank">Link</a>
	    <a href="/assets/documents/POSTER_AGU2021_LiuA.pdf" target="_blank">ML Helio 2022 Presentation</a>
	</div>
</section>

**Abstract:** Space weather forecasting remains a national priority in the United States due to the impacts of events like solar flares to life on Earth. High energy bursts of radiation originating from solar flares have the potential to disrupt critical infrastructure systems, including the power grid and GPS and radio communications. The rise of machine learning and the development of higher-quality instruments has greatly improved solar flare prediction models over the past decade. However, the magnetogram data used for solar flare forecasting—taken by the Solar and Heliospheric Observatory/Michelson Doppler Interferometer (SOHO/MDI) and the NASA Solar Dynamic Observatory/Helioseismic and Magnetic Imager (SDO/HMI) instruments—are incompatible due to differences in the cadence, resolution, and size of the data. Furthermore, many studies only focus on data from a single instrument which disregards decades worth of potential training data that is necessary to understand solar cycles. In this work, we show Generative Adversarial Networks (GANs) can be used to super-resolve the historic lower-quality SOHO/MDI data set to match SDO/HMI quality to create a standardized magnetogram data set. The implementation of a Pix2Pix GAN produced some undesirable artifacts in the synthetic image while image translation methods CycleGAN and CUT preserved solar features present in the data more accurately, even in the absence of paired data. The resulting combined, higher-quality data set will be used to improve the predictive power of current solar flare forecasting models.


I began working on this project in February 2021 and began to make significant progress in Summer 2021, while a part of the Boulder Solar Alliance REU. Through the REU, I was given the opportunity to present my work at the American Geophysical Union Conference in New Orleans that December.
![standing at poster]({{ a11isonliu.github.io}}assets/images/posts/2021/GAN-solarflare/at_poster.jpg)
![pic with REU friends]({{ a11isonliu.github.io}}assets/images/posts/2021/GAN-solarflare/reu_crew.jpg)
![goofy pic with REU friends]({{ a11isonliu.github.io}}assets/images/posts/2021/GAN-solarflare/reu_crew1.jpg)
