---
layout: page
title: ILLOC
description: In-Hall Localization with Standard LoRaWAN Uplink Frames
img: assets/img/illoc_bg.jpeg
importance: 2
category: work
---


LoRa (<ins>Lo</ins>ng <ins>Ra</ins>nge)  is a physical radio communication technique which can support long-range low-power communications. LoRa Wide Area Network (LoRaWAN) defines the communication protocol and system architecture.
There are many LoRaWAN systems deployed in large-scale indoor environments, such as expo centers, warehouses, museums, supermarkets, and airport terminals. While LoRaWAN is mainly designed for establishing connectivity, being able to localize LoRaWAN devices *unobtrusively* using their uplink frames is desirable. The unobtrusiveness here means that no special software instrumentation is needed for the LoRaWAN end devices. As such, the localization service is free from entanglement with any other applications running on the LoRaWAN devices; the already deployed LoRaWANs can develop the localization capability seamlessly. For example, in a warehouse, the operators can track the assets and detect their misplacement. In airports, LoRaWAN radios have been used for trolley management, where knowing the coarse location is very useful.  

We design and evaluate a time difference of arrival (TDoA)-based, unobtrusive in-hall LoRaWAN localization (**ILLOC**) system for any off-the-shelf LoRaWAN end devices. ILLOC deploys multiple software-defined radio (SDR)-based anchors with known positions and estimates the position of an end device based on the anchors’ TDoA measurements regarding any single uplink frame from the end device. The design of ILLOC faces two challenges: (1) Narrowband: LoRa signals use narrow frequency bands, thus are long and smooth in time domain. Without special treatment, the uncertainty of arrival time estimation with 125 kHz bandwidth is up to 8 microseconds, which is translated to 2.4 km distance in ranging. (2) Inter-anchor clock skews: tight synchronization of the anchors’ clocks (ideally, to nanoseconds accuracy) is a basis of TDoA-based multilateration. However, implementing highly accurate clock synchronization via cabling can incur large deployment overhead.


<div class="row">
    <div class="col-sm-12 text-center">
        {% include figure.html path="assets/img/illoc_system.png" title="illoc_system" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    ILLOC system overview.
</div>


To address these technical challenges, we propose the ILLOC system featuring two LoRaWAN-specific techniques: (1) the cross-correlation among the differential phase sequences (DPS) received by two anchors to estimate TDoA, exploiting LoRa’s CCS characteristic; and (2) the just-in-time (JIT) synchronization enabled by a specially deployed LoRaWAN end device providing time reference upon detecting a target device’s transmission, using LoRa’s Channel Activity Detection (CAD) feature. In a long tunnel corridor, a 70 × 32 sqm sports hall, and a 110 × 70 sqm indoor plaza with extensive non-line-of-sight (NLOS) propagation paths, ILLOC achieves median localization errors of 6 m (with 2 anchors), 8.36 m (with 6 anchors), and 15.16 m (with 6 anchors and frame fusion), respectively. The achieved accuracy makes ILLOC useful for applications including zone-level asset tracking, misplacement detection, airport trolley management, and cybersecurity enforcement like detecting impersonation attacks launched by remote radios. This research was published on ACM IMWUT/UbiComp'22 ([PDF](/assets/pdf/ILLOC-final.pdf)).

<div class="row">
    <div class="col-sm-12 text-center">
        {% include figure.html path="assets/img/illoc_plaza.png" title="illoc_plaza" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The indoor plaza deployment (with extensive NLOS paths). The crosses in the call-out figure are the localization results for position 5.
</div>