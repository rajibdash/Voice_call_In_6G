# Voice call In 6G (__*Work in progress*__)
Voice in 6G is now being studied in standardization activity, with IMS confirmed as the continued foundation.

# 6G Standalone (SA) Voice Call Architecture & Design Specification

This document outlines the full end-to-end technical specification, structural design, signaling flows, and 
standardization pathways for native Voice over 6G (Vo6G) within a 6G Standalone (SA) deployment framework.

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

The 6G SA Voice architecture transitions from the rigid network functions of 5G to an **AI-native, Service-Based Architecture (SBA)** 
featuring hyper-distributed network intelligence, semantic communication layers, and seamless terrestrial/non-terrestrial network (NTN) integration.

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

## 3. End-to-End Signaling Sequence (Vo6G SA Session Establishment)

The following sequence details an end-to-end call establishment between UE-A (initiating agent/human) and UE-B within a Standalone 6G topology.

```
 UE-A (6G)          6G gNB            6G AMF/SMF          IMS/P-CSCF          UE-B (6G)
   |                  |                    |                   |                  |
   |-- 1. NAS Intent ->|                    |                   |                  |
   |   (Voice Request)|-- 2. Service Req ->|                   |                  |
   |                  |   & Slice Assign   |                   |                  |
   |                  |<-- 3. PDU Session -|                   |                  |
   |                  |   Modification     |                   |                  |
   |<-- 4. RRC Reconf -|                    |                   |                  |
   |   (QoS-20 Flow)  |                    |                   |                  |
   |                                       |                   |                  |
   |============ 5. SIP INVITE (SDP: Semantic, Neural Codec) =>|                  |
   |                                       |                   |-- 6. SIP INVITE->|
   |                                       |                   |   (P-CSCF Alloc) |
   |                                       |                   |<-- 7. 180 Ring --|
   |<=========== 8. SIP 180 Ringing ===========================|                  |
   |                                       |                   |<-- 9. 200 OK ----|
   |<=========== 10. SIP 200 OK (SDP Negotiated Matrix) =======|                  |
   |                                       |                   |                  |
   |============ 11. SIP ACK =================================>|-- 12. SIP ACK -->|
   |                                       |                   |                  |
   |<======================== 13. NATIVE VOICE SESSION ==========================>|
   |             (Bi-directional Semantic Token / Real-Time Data Channel)          |
```

### 3.1 Signaling Flow Descriptions

1.  **NAS Intent Broadcast:** UE-A passes a highly compressed Non-Access Stratum (NAS) Intent message to the network containing pre-calculated network slice telemetry requests optimized for semantic communication profile sets.
2.  **Service Request & Resource Reservation:** The AMF handles authorization and prompts the SMF to spin up a specialized high-priority voice slice profile mapping directly to the active User Plane Function (UPF).
3.  **Dynamic QoS Allocation:** The 6G Core issues dedicated resource rules configuring the gNB scheduler loop with ultra-precise scheduling configurations (QoS Flow ID 20, optimized for low latency jitter boundaries).
4.  **SIP Session Handshake:** UE-A launches an extended SIP INVITE request through the established PDU tunnel to the P-CSCF. The Session Description Protocol (SDP) fields state negotiation vectors for custom generative audio parameters instead of fixed bandwidth numbers.
5.  **Session Acceptance & Synchronization:** The network routes the session downstream to UE-B, triggering verification handshakes. Upon confirmation, a 200 OK signal locks the target configuration arrays and releases conversational flow states over the active user-plane connection.

---

## 4. 3GPP Standardization Framework & Specification Tracks

System design for 6G voice services aligns structurally with specifications established by the **3GPP Technical Specification Group (TSG) Service and System Aspects (SA)** and **Radio Access Network (RAN)**.

### 4.1 Key Spec Target Matrices
*   **3GPP TR 23.801:** *Study on Architecture Aspects for the 6G System (FS_6G_ARC)*. This defines foundational interfaces ensuring interoperability between external packet agents and the Core SBA framework.
*   **3GPP TS 22.261:** *Service requirements for the 6G System*. Dictates latency constraints (< 1ms jitter boundaries) and availability profiles for tactile real-time conversational media arrays.
*   **3GPP TR 26.802:** *Study on Codecs and Media Processing for 6G*. Governs deployment policies regarding the integration of generative AI structural layers inside user-plane pipelines.

---

## 5. Academic Research & Industry Implementation Insights

To ensure production stability, the architectural models map directly to foundational pillars established by active 6G consortia:

1.  **Semantic Communications Breakthroughs:** Based on paradigms popularized by *Zhang et al.* (IEEE Communications Surveys & Tutorials, 2025), which demonstrate up to **93.5% bandwidth overhead mitigation** by using feature token arrays instead of structural PCM/waveform configurations.
2.  **Agent-to-Agent Mesh Telemetry:** Adapts structural logic from the *Hexa-X-II Project (Deliverable D1.2)* on native AI engine exposure within foundational radio frameworks, enabling real-time context-switching during active voice calls without dropping active data structures.
