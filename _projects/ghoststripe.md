---
layout: page
title: GhostStripe
description: Invisible Optical Adversarial Stripes on Traffic Sign against Autonomous Vehicles
img: assets/img/ghoststripe_drawing.png
importance: 3
category: work
---




Autonomous vehicles rely heavily on camera-based perception to interpret their surroundings, particularly for recognizing traffic signs. However, cameras using CMOS sensors are vulnerable to the **rolling shutter effect (RSE)**, where images are captured line by line instead of instantaneously. This phenomenon allows adversaries to introduce invisible perturbations using flickering light sources, creating adversarial patterns that mislead traffic sign recognition systems. 
<!-- To pose a real threat, such attacks must be stable, meaning the misclassification remains consistent over consecutive frames. Without stability, anomalies may be detected, and defensive mechanisms could trigger fail-safe actions, reducing the impact of the attack. -->

<div class="row">
    <div class="col-sm-12 text-center">
        <div style="max-width:500px; margin:auto;">
            {% include figure.html path="assets/img/gs_envisaged.png" title="gs_envisaged" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Figure 1: Invisible optical adversarial-example attack against traffic sign recognition.
        </div>
    </div>
</div>

This paper aims to achieve stable attack results which render clearer security implications in the autonomous driving
context. In the envisaged attack as illustrated in Fig. 1a, an LED is deployed in the proximity of a traffic sign plate and projects
controlled flickering light onto the plate surface. As the flickering frequency is beyond human eye’s perception limit (up to 50-90 Hz), the flickering is invisible to human and the LED appears as a benign illumination device. Meanwhile, on the image captured by the camera, as illustrated in Fig. 1a, the RSE-induced colored stripes mislead the traffic sign recognition. For the attack to mislead the autonomous driving program to make erroneous decisions unconsciously, the traffic sign recognition results should be wrong and same across a sufficient number of consecutive frames (Fig. 1c). We call the attack meeting this requirement stable.
If the attack is not stable (Fig. 1b), an anomaly detector may identify the malfunction of the recognition and activate a fail-safe mechanism, e.g., falling back to manual driving or emergency safe stopping, rendering the attack less threatening.

<div class="row">
    <div class="col-sm-12 text-center">
        <div style="margin:auto;">
            {% include figure.html path="assets/img/gs_system.png" title="gs_system" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Overview of GhostStripe.
        </div>
    </div>
</div>


We design an attack system called **GhostStripe** in two versions with different requirements on the attack deployment. The first version, GhostStripe1, maintains stationary adversarial stripes in the FoV by calibrating the LED flickering frequency.   GhostStripe1 employs a vehicle tracker to monitor the victim vehicle's real-time location and dynamically adjusts the LED flickering accordingly. GhostStripe1 does not require any instrumentation on the victim vehicle. It aims to maintain the victim's traffic sign recognition result stable over time. However, it is an untargeted attack, in that the recognition result is unpredictable (i.e., not deliberately spoofed towards a certain class) because the vertical positions of the adversarial stripes  are not controlled by the attacker. To achieve targeted attack (i.e., the attacker can control the victim's recognition result as a certain class), on top of GhostStripe1, GhostStripe2 deploys framing sniffer to sense the victim camera's framing moments via a current transducer clipped on the power wire of the camera. The sniffer transmits the detected framing moments to the LED controller to refine the timing control of the flickering. Although installing the framing sniffer requires physical access to the victim vehicle, it is possible, say, during maintenance by an auto care provider colluding with the attacker. We evaluate GhostStripe on a real outdoor testbed and a lab testbed with Baidu Apollo’s default camera. On the outdoor testbed, GhostStripe1 and GhostStripe2 can achieve up to 94% and 97% success rates in launching untargeted and targeted attacks, respectively.




<div class="row">
    <div class="col-sm-12 text-center">
        <div style="margin:auto;">
            {% include figure.html path="assets/img/gs_implementation.png" title="gs_implementation" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="caption" style="text-align: center; margin-top: 10px;">
            Outdoor testbed implementation.
        </div>
    </div>
</div>



<div class="col-sm-12 text-center">
    <iframe src="https://drive.google.com/file/d/1_9rpWnpCamqBNOEU5J7o9Nfh0x7FmWGm/preview"
            style="width:100%; height:500px;" allowfullscreen>
    </iframe>
    <div class="caption" style="text-align: center; margin-top: 10px;">
        Demo: GhostStripe achieves stable adversarial stripes on a "stop" sign, spoofing the traffic sign recognition to misclassify it as a "priority road".
    </div>
</div>




To counteract GhostStripe, we introduce **GhostBuster**, a software-based defense mechanism that detects and mitigates the attack’s effects. GhostBuster consists of a perturbation detector that identifies suspicious pixel fluctuations indicative of adversarial stripes and a sign restorer that reconstructs the natural traffic sign appearance. Experimental results show that GhostBuster successfully restores classification accuracy to 100% in 5% of trials and above 80% in 50% of trials, significantly reducing the attack’s impact. This research highlights the importance of developing robust defenses against adversarial optical attacks to ensure the safety and reliability of autonomous vehicle perception systems. We also discuss other possible countermeasures.


For a detailed breakdown of our research, check out our full conference paper at ACM MobiSys 2024. [[PDF](/assets/pdf/MobiSys24-GhostStripe.pdf)]

This work also won the **Best Demo Award** at ACM SenSys 2024 🏆. [[Abstract](/assets/pdf/GhostStripe-SenSys-Demo.pdf)] [[NTU News](https://www.ntu.edu.sg/computing/news-events/news/detail/best-demo-award-at-the-22nd-acm-conference-on-embedded-networked-sensor-systems-(sensys-2024))] [[ACM News](https://www.linkedin.com/posts/association-for-computing-machinery_we-are-thrilled-to-share-the-outstanding-activity-7274460854056542213-Pxuj/)]

The extended version with the detailed design of GhostBuster is under review.