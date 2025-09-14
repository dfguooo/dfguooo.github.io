---
layout: page
title: GhostStripe
description: Invisible Optical Adversarial Stripes on Traffic Sign against Autonomous Vehicles
img: assets/img/ghoststripe_drawing.png
importance: 3
category: work
---



In intelligent driving systems, cameras are among the most critical perception sensors, playing a central role in tasks such as traffic sign recognition, lane detection, and obstacle identification. However, our research reveals that these seemingly reliable and well-functioning camera systems may harbor unexpected security vulnerabilities. This research project, **GhostStripe**, uncovers an attack method that leverages optical interference -- invisible to the human eye yet capable of deceiving intelligent driving perception systems. This work was published at **ACM MobiSys 2024** [1] and received the **Best Demo Award at ACM SenSys 2024** [2].

**Attack Effectiveness**

Before diving into the technical details, we first demonstrate the attack effectiveness. In the video below, under the GhostStripe attack, a “STOP” sign is consistently and stably misclassified by the target vehicle as a “Priority Road” sign (indicating that other vehicles must give way to the target vehicle).

<div class="col-sm-12 text-center">
    <iframe src="https://drive.google.com/file/d/1_9rpWnpCamqBNOEU5J7o9Nfh0x7FmWGm/preview"
            style="width:100%; height:500px;" allowfullscreen>
    </iframe>
    <div class="caption" style="text-align: center; margin-top: 10px;">
        Demo: GhostStripe achieves stable adversarial stripes on a "stop" sign, spoofing the traffic sign recognition to misclassify it as a "priority road".
    </div>
</div>



**Physical-Layer Vulnerability in the Cameras**

Many vehicle cameras are built with Complementary Metal-Oxide Semiconductor (CMOS) image sensors. These sensors typically employ a rolling shutter, which exposes the image line by line—each row of pixels is exposed sequentially within a short time window, rather than exposing the entire frame simultaneously (as illustrated in Fig. 1).
This row-wise exposure mechanism stems from readout bottlenecks inherent in CMOS architecture. Its advantages in terms of cost and power efficiency have led to its widespread adoption in most commercial cameras, including those used in automotive vision systems [3, 4].

<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_rs_example.gif" title="gs_rs" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 1: Left: Rolling shutter (row-wise exposure, balanced between image quality and cost); Right: Global shutter (simultaneous exposure, more expensive and hardware-intensive). GIF source: www.baumer.com
        </div>
    </div>
</div>



However, this row-wise exposure mechanism introduces a critical side effect: when the external light source changes rapidly, different scanlines of the image are exposed at slightly different moments, resulting in artifacts such as stripes or distortions. This phenomenon is known as the Rolling Shutter Effect (RSE). More specifically, if a fast-flashing light source illuminates the target object, each scanline receives a different intensity of light during its exposure time. This leads to colored stripe-like artifacts in the captured image. While these stripes are invisible to the human eye, they constitute a visible disturbance in the image formed by rolling shutter cameras. The GhostStripe attack exploits this imaging characteristic by introducing temporal-domain optical interference without altering the physical appearance of the traffic sign. As a result, specially crafted adversarial examples are injected into the camera's perception pipeline, misleading deep neural network–based traffic sign recognition.



**Attack Mechanism: "Invisibility" and "Deception"**

GhostStripe leverages a high-frequency modulated LED light source to project rapidly flickering illumination onto the surface of a traffic sign. The flickering frequency exceeds the human visual perception threshold (typically 50–90 Hz) [5], making the LED appear as a continuously lit and benign light source to human observers. However, on traffic sign images captured by rolling shutter cameras, this invisible flickering leaves behind distinct interference stripes. These stripes can be carefully designed to induce misclassification by traffic sign recognition models based on deep neural networks. The attack requires no physical contact with the vehicle or modification to the traffic sign itself. It can be deployed remotely, making it both stealthy and practically feasible in real-world scenarios.


<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_invisible.png" title="gs_invisible" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 2: Invisible optical adversarial attack.
        </div>
    </div>
</div>

**Challenges in Achieving Stable Attacks**

Prior studies on rolling shutter–based adversarial attacks have primarily focused on single-frame attacks in controlled environments [6, 7]. GhostStripe advances beyond this by targeting the sequence of image frames captured during autonomous driving, aiming to interfere with perception consistently and persistently over time. Specifically, a truly threatening attack must satisfy cross-frame stability: as the victim vehicle passes through the attack region, the adversarial interference must cause continuous and stable misclassification across multiple consecutive frames, effectively misleading the vehicle's decision-making pipeline. If the attack is unstable (e.g., as illustrated in Fig. 3), the system may detect anomalies, trigger emergency braking, or fall back to manual driving, thereby mitigating the attack's impact.


<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_unstable.png" title="gs_unstable" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 3: Unstable attack caused by stripe rolling and vehicle movement.
        </div>
    </div>
</div>

<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_stable.png" title="gs_stable" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 4: Stable attack achieved by adapting to camera operation and vehicle motion.
        </div>
    </div>
</div>


To achieve a stable attack effect (as shown in Fig. 4), GhostStripe addresses two key challenges:

1. **Stable stripes on traffic sign.**
The adversarial stripe pattern must remain stationary in the captured image; otherwise, the recognition result may fluctuate across frames. Whether the stripes appear to “move” or “roll” in the image depends on the mismatch between the LED flickering frequency and the camera’s rolling shutter frame capturing rate.
GhostStripe calibrates and controls the LED’s flickering frequency to match the camera’s capturing frequency, allowing the adversarial stripes to remain static over the traffic sign region. This ensures consistent misclassification results over time.

2. **Field of View (FoV) adaptation.**
As the vehicle moves, the position and size of the traffic sign in the camera’s field of view change continuously. Without adaptation, the projected interference may fall out of alignment or produce varying perturbations, reducing attack effectiveness.
To address this, we design a location-aware dynamic adjustment mechanism that continuously adapts the attack replaying parameters based on the vehicle’s position, ensuring the adversarial stripes remain aligned and effective throughout the sign’s appearance in the moving camera’s view.




**GhostStripe: System Design**


<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:800px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_system.png" title="gs_system" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 2: Overview of GhostStripe.
        </div>
    </div>
</div>

We design and implement two versions of the GhostStripe attack system:

- **GhostStripe 1**:
This version does not require access to the victim vehicle. It achieves stable, untargeted attacks by tracking the vehicle’s position and adjusting the LED flickering frequency accordingly. While it can reliably disrupt traffic sign recognition, it cannot control the specific misclassification results.

- **GhostStripe 2**:
Built on GhostStripe 1, this version introduces a framing sniffer deployed on the target vehicle. By sensing the electromagnetic radiation on the camera’s power wire (without tapping into the camera circuit or video stream), it detects the framing moments of the rolling shutter camera. This enables precise timing control of the LED, allowing the adversarial stripes to be positioned deterministically in the image.
As a result, GhostStripe 2 supports targeted attacks, misclassifying traffic signs into attacker-specified classes with high stability.



**Deployment and Evaluation on Real-Road Testbed**

We deployed and evaluated the effectiveness of GhostStripe in real-world outdoor driving environments. The evaluation results demonstrate the following:

- GhostStripe 1 (untargeted attack) achieves up to 94% attack success rate in outdoor tests;

- GhostStripe 2 (targeted attack) further improves the attack success rate to 97%, and is able to mislead the recognition results into attacker-specified target classes.

These results demonstrate that GhostStripe is highly feasible and threatening in realistic autonomous driving settings.






<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:800px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_implementation.png" title="gs_implementation" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 6: Deployment in real-road testbed (top right: human visual perception; bottom right: camera-captured image)
        </div>
    </div>
</div>


**Countermeasure: GhostBuster**

To address the attack surface revealed by GhostStripe, we design a software-based defense mechanism named GhostBuster. Its core components include:

- **Perturbation Detector**: Identifies potential rolling shutter interference by analyzing temporal optical texture anomalies in the captured image, which may result from flickering light sources.

- **Sign Restorer**: Employs generative models (e.g., CycleGAN) to reconstruct the affected image regions, thereby enhancing the robustness of recognition results.

In our experiments, GhostBuster demonstrated promising defense performance:

- In over 5% of attack trials, it restored classification accuracy to 100%.

- In over 50% of attack trials, it recovered accuracy to 80%.

GhostBuster provides a lightweight and deployment-friendly front-end defense, contributing to improved security in intelligent driving systems.





<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.liquid path="assets/img/ghostbuster.png" title="gs_defense" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 7: GhostBuster’s defense pipeline.
        </div>
    </div>
</div>


**Conference Demonstration and Award**

We presented this paper at ACM MobiSys 2024, held in June 2024 in Tokyo, Japan. Later, in November 2024, we demonstrated the GhostStripe attack system live at ACM SenSys 2024 in Hangzhou, China. The project was honored with the Best Demo Award at ACM SenSys 2024.
<!-- The co-authors of this research include: Wu Yuting, Dai Yimin, Zhou Pengfei (University of Pittsburgh), Lou Xin (Singapore Institute of Technology), and Tan Rui. -->


<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:600px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_1min.jpg" title="gs_1min" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 8: One-minute Madness Talk at ACM SenSys 2024.
        </div>
    </div>
</div>

<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:600px; margin:auto;">
            {% include figure.liquid path="assets/img/gs_onsitedemo.jpg" title="gs_onsitedemo" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 8: On-site live demo at ACM SenSys 2024.
        </div>
    </div>
</div>


**Conclusion and Outlook**

GhostStripe reveals an underexplored vulnerability in rolling shutter cameras within intelligent driving scenarios. Through system design, physical deployment, and real-world experiments, we demonstrate the practical feasibility and security implications of such optical attacks.
We call upon both the academic and industrial communities to pay greater attention to physical-layer risks in vision-based perception systems. Meanwhile, we hope our proposed defense, GhostBuster, serves as a starting point for developing practical countermeasures, ultimately advancing the security and robustness of autonomous driving perception systems.




*The extended version with the detailed design of GhostBuster is under review.

**References**

[1] Dongfang Guo, Yuting Wu, Yimin Dai, Pengfei Zhou, Xin Lou, and Rui Tan. 2024. Invisible Optical Adversarial Stripes on Traffic Sign against Autonomous Vehicles. In The 22nd Annual International Conference on Mobile Systems, Applications and Services (MOBISYS ’24), June 3–7, 2024, Minato-ku, Tokyo, Japan. ACM, New York, NY, USA, 13 pages. [[PDF](/assets/pdf/MobiSys24-GhostStripe.pdf)]

[2] Dongfang Guo, Yuting Wu, Yimin Dai, Pengfei Zhou, Xin Lou, and Rui Tan. 2024. Demo: Invisible Adversarial Stripes against Traffic Sign Recognition in Autonomous Driving. In The 22nd ACM Conference on Embedded Networked Sensor Systems (SENSYS ’24), November 4–7, 2024, Hangzhou, China. ACM, New York, NY, USA, 2 pages. **Best Demo Award** 🏆 [[Abstract](/assets/pdf/GhostStripe-SenSys-Demo.pdf)] [[NTU News](https://www.ntu.edu.sg/computing/news-events/news/detail/best-demo-award-at-the-22nd-acm-conference-on-embedded-networked-sensor-systems-(sensys-2024))] [[ACM News](https://www.linkedin.com/posts/association-for-computing-machinery_we-are-thrilled-to-share-the-outstanding-activity-7274460854056542213-Pxuj/)]

[3] 2020. Teardown: Teslaś hardware retrofits for model 3. https://www.eetasia.com/teslas-hardware-retrofits-for-model-3/

[4] 2023. Apollo hardware development platform. https://developer.apollo.auto/platform/hardware.html

[5] Natalia D Mankowska, Anna B Marcinkowska, Monika Waskow, Rita I Sharma, Jacek Kot, and Pawel J Winklewski. 2021. Critical flicker fusion frequency: a narrative review. Medicina 57, 10 (2021), 1096.

[6] Athena Sayles, Ashish Hooda, Mohit Gupta, Rahul Chatterjee, and Earlence Fernandes. 2021. Invisible perturbations:Physical adversarial examples exploiting the rolling shutter effect. In IEEE/CVF CVPR. 14666–14675.

[7] Sebastian Köhler, Giulio Lovisotto, Simon Birnbach, Richard Baker, and Ivan Martinovic. 2021. They see me rollin’: Inherent vulnerability of the rolling shutter in cmos image sensors. In ACSAC. 399–413.