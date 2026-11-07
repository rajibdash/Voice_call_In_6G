# Voice call In 6G (__*Work in progress*__)
6G voice is a design and research topic, not yet a standardized end-to-end system. IMS is the mature 3GPP multimedia-service baseline to consider, but its precise role and evolution in 6G remain subject to Release 21 standardization.

# 6G Standalone (SA) Voice Call: Conceptual Architecture & Design

This work-in-progress document explores a possible end-to-end design for voice over a future 6G Standalone (SA) system. The diagrams, protocol labels, and signaling flows below are proposals, not a technical specification; the standards status and design boundaries are clarified in the addendum.

---

## 0. Draft initial layout

```

+-------------------------------------------------------------+
|              IMS / Next-Gen Communication Engine            |  <-- Session Control, Data Channels, AI Services
+-------------------------------------------------------------+
|               AI / Intelligence & Data Fabric               |  <-- AI Models (Codecs), Network Agents (NWDAF+)
+-------------------------------------------------------------+
|          6G Core Network (6GC) / Service-Based              |  <-- AMF, SMF/UPF, Network Slicing
+-------------------------------------------------------------+
|  6G Radio Access (RAN)  |  Non-Terrestrial Networks (NTN)   |  <-- Terahertz, Satellite Packet Forwarders
+-------------------------------------------------------------+

```

## 1. Architectural Blueprint & Network Topography

This is a **conceptual design**, not a 3GPP architecture. It explores how a future 6G system might build on service-based core principles while supporting AI-enabled services and terrestrial/non-terrestrial access. The eventual 6G architecture, interfaces, and voice procedures remain to be standardized.

### 1.1 Structural Topology Block Diagram

```
+-------------------------------------------------------------------------------------------------+
|                                 IMS / NEXT-GEN INTERACTIVE FABRIC                               |
|   +--------------------------+   +--------------------------+   +---------------------------+   |
|   |   S-CSCF (SIP Server)    |   |   P-CSCF (Proxy Edge)    |   |  IMS-DC (Data Channel)    |   |
|   +--------------------------+   +--------------------------+   +---------------------------+   |
+-----------------------------------------------+-------------------------------------------------+
                                                | Rx / N5 / Sh / Nims
+-----------------------------------------------+-------------------------------------------------+
|                         6G SERVICE-BASED CORE NETWORK (6GC-SA)                                  |
|   +--------------------------+   +--------------------------+   +---------------------------+   |
|   |  AMF (Mobility/Signaling)|   |  SMF (Session Control)   |   | NEF / Ad-Func (AI Agent)  |   |
|   +--------------------------+   +--------------------------+   +---------------------------+   |
|                                               |                                                 |
|   +-------------------------------------------+---------------------------------------------+   |
|   |                    UPF (User Plane Function) + MoE (Mixture of Experts Engine)          |   |
|   +-------------------------------------------+---------------------------------------------+   |
+-----------------------------------------------+-------------------------------------------------+
                                                | N3 / N9 (User Plane)
+-----------------------------------------------+-------------------------------------------------+
|                                 NATIVE 6G RADIO ACCESS NETWORK                                  |
|       +---------------------------------------+---------------------------------------+         |
|       |     6G gNB (Sub-THz / FR3 Small Cell) |     LEO Satellite Packet Forwarder    |         |
|       +---------------------------------------+---------------------------------------+         |
+-----------------------------------------------+-------------------------------------------------+
                                                | Air Interface (Uu / Un-NTN)
+-----------------------------------------------+-------------------------------------------------+
|                                    USER EQUIPMENT (UE-6G)                                       |
|    +------------------------------------------+--------------------------------------------+    |
|    |  Semantic Transceiver / Neural Codec Core|  Local Autonomous Conversational AI Agent  |    |
|    +------------------------------------------+--------------------------------------------+    |
+-------------------------------------------------------------------------------------------------+
```

### 1.2 Key Architectural Evolution Highlights
*   **Semantic Layer Integration:** The user plane incorporates a semantic processing layer utilizing a
    **Mixture of Experts (MoE)** engine to compress voice streams into high-level intent/feature vectors.
*   **IMS Data Channel Evolution (IMS-DC):** Extends IMS capabilities to concurrently transmit bootstrap
    application scripts, real-time spatial texturizers, and AI agent translation context alongside the audio
    sync block.
*   **Unified NTN Plane:** Satellite linkages share an integrated user plane control loop directly with edge
    nodes, removing the distinct delay boundaries typical in legacy inter-working modes.

---

## 2. Protocol Stack Design

The 6G SA Control Plane and User Plane are optimized for minimal framing overhead and native handling of AI/Semantic traffic components.

### 2.1 User Plane Protocol Stack

```
   +-----------------------------+
   |   Neural App Voice / XR     |  <-- Semantic Tokens & Media Synchronization Strings
   +-----------------------------+
   |             IMS-DC          |  <-- Data Channel Layer / WebRTC Framing Protocol
   +-----------------------------+
   |             RTP             |  <-- Real-Time Transport Protocol (Optimized RFC 3550)
   +-----------------------------+
   |             UDP             |  <-- User Datagram Protocol
   +-----------------------------+
   |             IP              |  <-- IPv6 Layer with Segment Routing (SRv6) Headers
   +-----------------------------+
   |           6G-SDAP           |  <-- Service Data Adaptation Protocol (QoS mapping to AI-Slices)
   +-----------------------------+
   |           6G-PDCP           |  <-- Packet Data Convergence Protocol (ROHC with AI State Prediction)
   +-----------------------------+
   |            6G-RLC           |  <-- Radio Link Control
   +-----------------------------+
   |            6G-MAC           |  <-- Medium Access Control
   +-----------------------------+
   |            6G-PHY           |  <-- sub-THz / FR3 Radio Layer (Spatial-multiplexed waveforms)
   +-----------------------------+
```

### 2.2 Control Plane Innovations
The Control Plane simplifies session setup by introducing an explicit **AI-Intent Signaling Layer** embedded inside the NAS (Non-Access Stratum) container, facilitating pre-allocation of Edge Compute engines prior to SIP Session Initiation.

---

## 3. End-to-End Signaling Sequence (Vo6G SA Session Establishment) - Draft

The following sequence details an end-to-end call establishment between UE-A (initiating agent/human) and UE-B within a Standalone 6G topology.

```
 UE-A (6G)           6G gNB            6G AMF/SMF          IMS/P-CSCF          UE-B (6G)
   |                   |                    |                   |                  |
   |-- 1. NAS Intent ->|                    |                   |                  |
   |   (Voice Request) |-- 2. Service Req ->|                   |                  |
   |                   |   & Slice Assign   |                   |                  |
   |                   |<-- 3. PDU Session -|                   |                  |
   |                   |       Modification |                   |                  |
   |<-- 4. RRC Reconf -|                    |                   |                  |
   |   (QoS-20 Flow)   |                    |                   |                  |
   |                   |                    |                   |                  |
   |============ 5. SIP INVITE (SDP: Semantic, Neural Codec)  =>|                  |
   |                                        |                   |-- 6. SIP INVITE->|
   |                                        |                   |   (P-CSCF Alloc) |
   |                                        |                   |<-- 7. 180 Ring --|
   |<=========== 8. SIP 180 Ringing ============================|                  |
   |                                        |                   |<-- 9. 200 OK ----|
   |<=========== 10. SIP 200 OK (SDP Negotiated Matrix) ========|                  |
   |                                        |                   |                  |
   |============ 11. SIP ACK ==================================>|-- 12. SIP ACK -->|
   |                                        |                   |                  |
   |<======================== 13. NATIVE VOICE SESSION ===========================>|
   |             (Bi-directional Semantic Token / Real-Time Data Channel)          |
```

### 3.1 Signaling Flow Descriptions

1.  **NAS Intent Broadcast:** UE-A passes a highly compressed Non-Access Stratum (NAS) Intent message to the network containing pre-calculated network slice telemetry requests optimized for semantic communication profile sets.
2.  **Service Request & Resource Reservation:** The AMF handles authorization and prompts the SMF to spin up a specialized high-priority voice slice profile mapping directly to the active User Plane Function (UPF).
3.  **Dynamic QoS Allocation:** The 6G Core issues dedicated resource rules configuring the gNB scheduler loop with ultra-precise scheduling configurations (QoS Flow ID 20, optimized for low latency jitter boundaries).
4.  **SIP Session Handshake:** UE-A launches an extended SIP INVITE request through the established PDU tunnel to the P-CSCF. The Session Description Protocol (SDP) fields state negotiation vectors for custom generative audio parameters instead of fixed bandwidth numbers.
5.  **Session Acceptance & Synchronization:** The network routes the session downstream to UE-B, triggering verification handshakes. Upon confirmation, a 200 OK signal locks the target configuration arrays and releases conversational flow states over the active user-plane connection.

---

## 4. 3GPP Standardization Framework

The relevant 3GPP work spans service requirements (SA1), system architecture (SA2), multimedia and codecs (CT/SA4), security (SA3), and radio access (RAN). The existence of a related 5G specification or a research proposal does not make it a 6G requirement. Do not treat the earlier draft's labels, latency figures, or protocol extensions as 3GPP-defined 6G features.

Useful current anchors are **TR 22.870** for 6G use cases and service requirements, **TS 22.173** for the established multimedia telephony service, **TS 23.228** for the established IMS architecture, and the 3GPP **Release 21 timeline** for the 6G normative-work milestones. These references have different scope: TR 22.870 is a study report, while TS 22.173 and TS 23.228 describe existing service and IMS baselines, not a finished 6G voice specification.

---

## 5. Research Directions

Research is exploring AI-enabled voice coding, efficient voice over constrained NTN links, and more programmable RAN resource control. These are research directions and prototypes—not evidence that 6G will use a semantic-token voice format, put generative models in the UPF, or require an AI-controlled IMS. Results for one codec, channel, or simulation should not be generalized into an end-to-end 6G voice guarantee.

## 6G Voice: 3GPP Standardization Perspective

*Addendum (7 November 2026). This section supplements the draft above; it does not revise the preceding material.*

### What 3GPP has standardized so far

3GPP's Release 20 6G work includes SA1's study of use cases and service requirements, documented in [TR 22.870, *Study on 6G Use Cases and Service Requirements*](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=4374). A TR is a study report: it records study results and is not, by itself, a normative specification of an implementable 6G voice system. As of this addendum, 3GPP has not published a normative specification defining a distinct 6G voice-call architecture, call procedures, or 6G-specific voice protocol stack.

The established 3GPP reference for IP multimedia telephony is IMS: [TS 22.173](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=620) specifies multimedia telephony service requirements, and [TS 23.228](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=821) specifies the IMS architecture. These are useful baselines for discussing future voice service, but they do not, on their own, specify how a future 6G radio or core implements voice. A 6G IMS evolution or alternative would need to be established through approved 3GPP work and specifications, not inferred from a study report.

### Standardization path and voice topics

3GPP describes Release 20 as the 6G study phase and Release 21 as the start of normative 6G specification work. The [3GPP Release 21 timeline](https://www.3gpp.org/news-events/3gpp-news/rel21-timeline) gives the current stage milestones; these are program targets, not a guarantee that any particular voice feature will be standardized. SA1 develops service requirements; architecture, security, media, and radio specifications are developed by the relevant 3GPP groups as work items are approved.

For voice-call standardization, the questions to track include service requirements and call continuity; IMS/core and access architecture; media and codec negotiation; QoS and latency treatment; mobility and interworking; emergency and priority communications; and security, privacy, and interoperability. These are topics for standards work, not claims that a particular 6G solution or performance target has already been adopted.

### How to read the proposed architecture above

The architecture, signaling flow, and protocol labels in this document are design proposals, not an approved 3GPP 6G specification. In particular, names such as “NAS Intent,” “QoS Flow ID 20” as a standardized voice profile, “semantic/neural codec” SDP extensions, and a “MoE” in the UPF should not be read as 3GPP-defined 6G procedures or interfaces. Treat them as concepts requiring formal specification and interoperability work unless and until the relevant 3GPP specifications define them.

## IMS, 6G Core, and SA RAN: Design Responsibilities

A useful way to design a 6G voice call is to keep **service control**, **packet-session policy**, and **radio delivery** distinct, then define their interfaces and failure handling. IMS is not the radio scheduler, and the 6G Core is not the SIP call server. A service can use IMS over an IP connection delivered by the mobile core and radio access.

| Domain | Role in a voice call | Design boundary |
|---|---|---|
| **IMS / service layer** | Authenticates and registers the subscriber for IMS services; routes SIP sessions; applies telephony service logic; negotiates compatible media; and supports supplementary services and interworking with other IMS networks. | Owns call/session semantics, not radio scheduling or user-packet forwarding. Any IMS evolution for 6G, including AI-assisted features, needs defined service behavior, security, and interoperable signaling. |
| **6G Core** | Provides access and mobility management, establishes and manages the IP session, applies subscription and operator policy, selects user-plane connectivity, and coordinates QoS treatment and service exposure with the access network. It can support charging, lawful/regulatory functions, and continuity according to the eventual system design. | Carries and governs connectivity for IMS and media; it does not replace SIP service control. Exact 6G functions, interfaces, QoS identifiers, and IMS-to-core policy procedures are not yet specified. |
| **6G SA RAN** | Connects directly to the 6G core (without relying on an LTE anchor), establishes radio bearers, maps authorized QoS treatment to radio resources, schedules uplink/downlink packets, adapts to channel conditions, and handles radio mobility and link recovery. | Delivers packets under radio constraints; it does not decide who to call or provide telephony service logic. “SA” describes the core connection model, not a promise of zero latency or a dedicated voice slice. |
| **UE and media endpoints** | Run the IMS client, negotiate and encode/decode media, protect media as applicable, and report radio/media conditions through supported mechanisms. | Codec/model capability and energy use are endpoint constraints; both ends need interoperable media options and a fallback path. |

### Illustrative call lifecycle (not a 6G procedure)

1. The UE obtains access and an IP session through the SA network. The core applies subscriber and operator policy; the RAN provides the radio connection.
2. The UE registers with IMS and sends a SIP session request over IP. IMS performs service routing and negotiates the mutually supported media parameters with the far end.
3. If the service needs dedicated or differentiated packet treatment, IMS/service policy can request it through the core's policy mechanisms. The core authorizes and provisions the packet treatment, and the RAN maps it to available radio resources. Exact 6G procedures remain to be defined.
4. Voice media travels between endpoints through the user plane; IMS remains responsible for session control. Mobility, congestion, loss, or a change of access may trigger policy updates, media adaptation, or session continuity behavior.
5. On release, IMS clears the session and the network releases resources when no longer needed.

SIP signaling and the media path are separate logical functions even when they share IP connectivity. IMS Data Channels, where supported, are an optional rich-communication capability and are not the voice codec or a mandatory voice-media layer. Likewise, a PDU/IP session or a slice is not itself a voice service: the service, policy, QoS handling, and end-to-end media behavior must work together.

### What 6G SA RAN changes for the voice designer

SA removes dependence on an LTE anchor for the access-to-core path; it does **not** make voice intrinsically reliable or remove radio effects. The RAN still faces fading, interference, blockage, handover gaps, uplink power limits, and competition for spectrum. Those effects directly shape conversational quality through packet loss, jitter, delay, and interruption.

For a voice-service design, the key RAN-to-core questions are:

- **QoS mapping and admission:** How does authorized service policy become radio scheduling priority and an appropriate bearer/flow treatment? What happens when radio resources are scarce—admission control, degradation, or pre-emption—and how are those decisions coordinated end to end?
- **Mobility and continuity:** How are media packets protected through handover, beam changes, cell-edge operation, and terrestrial/NTN transitions? Call continuity must be tested across access changes, not inferred from a fast scheduler.
- **Coverage and power:** Voice quality and availability at the cell edge, indoors, and on uplink-limited devices matter as much as peak data rate. High-frequency or very wideband radio is not a prerequisite for good voice.
- **Scheduling and resilience:** Low-latency scheduling can help, but queueing in the UE, RAN, transport, core, and far-end path all contribute. A service target needs an end-to-end budget and measurable recovery behavior, not a single radio latency number.
- **AI-assisted control:** RAN automation/xApps may help optimize resources against an SLA, but control-loop delay, data quality, policy constraints, stability, explainability, and safe fallback matter. AI should augment—not replace—deterministic service policy and radio safety controls.
- **NTN:** Satellite access extends reach but has distinct propagation delay, Doppler, and intermittent-link constraints. Codec efficiency and adaptation help with limited capacity; they cannot eliminate propagation delay. Interactive voice must be evaluated separately from delay-tolerant messaging.

### Current research-informed design view (7 November 2026)

The most defensible near-term design is **evolutionary and service-led**: preserve interoperable IMS telephony and media negotiation as the baseline, and let a future 6G access/core improve coverage, continuity, policy coordination, and automation without assuming a new “native 6G voice” protocol. Research is valuable for selecting candidate enhancements, but it does not establish a 3GPP architecture or deployment requirement.

For designers, prioritize conversational quality and operability—mouth-to-ear delay, jitter, loss concealment, intelligibility, call setup success, handover interruption, emergency calling, security, and interoperability—over headline radio throughput or an isolated latency result. AI-enhanced codecs and translation may be optional negotiated features; the endpoints need a conventional interoperable fallback. Any learned model should be assessed for compute and battery cost, privacy, security, robustness to noise and accents, and predictable behavior when inference is unavailable. Semantic representations are especially application-dependent and are not a drop-in replacement for intelligible human speech.

Recent work illustrates distinct, limited research tracks:

- **LANA, “Lightweight AI-driven High-Fidelity Voice Services over 6G Narrowband Non-Terrestrial Networks,”** explores efficient AI-driven voice for narrowband NTN conditions. It is relevant to constrained-link codec research, not proof of a generally adopted mobile-telephony codec or a way to overcome satellite propagation delay. [Research record](https://researchportal.hkust.edu.hk/en/publications/lana-lightweight-ai-driven-high-fidelity-voice-services-over-6g-n/) · [IEEE Xplore](https://ieeexplore.ieee.org/document/11152968)
- **“RSLAQ: A Robust SLA-driven 6G O-RAN QoS xApp Using Deep Reinforcement Learning”** studies learning-based RAN QoS/resource control. It informs the automation discussion, but an O-RAN QoS experiment is not a voice-call standard or evidence of guaranteed end-to-end voice quality. [arXiv:2504.09187](https://arxiv.org/abs/2504.09187)
- **“Flexible and High-Performance Radio Access Networks for Upcoming Sixth-Generation (6G) Systems”** surveys/proposes future RAN design directions. It is useful for understanding radio architecture trade-offs, while voice-specific service and interoperability requirements remain separate questions. [arXiv:2509.24417](https://arxiv.org/abs/2509.24417)

Taken together, these papers support a research agenda—adaptive codecs, constrained-link operation, and policy-aware RAN control—not a settled “AI-native voice stack.” The practical architecture should keep the service layer, core policy, RAN execution, and media endpoints independently testable, with explicit fallback and continuity behavior.

### 3GPP references

- [3GPP: 6G Use Cases and Service Requirements (SA1)](https://www.3gpp.org/technologies/sa1-use-cases-6g)
- [3GPP TR 22.870 specification record](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=4374)
- [3GPP TS 22.173 specification record](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=620)
- [3GPP TS 23.228 specification record](https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=821)
- [3GPP: Release 21 timeline](https://www.3gpp.org/news-events/3gpp-news/rel21-timeline)
