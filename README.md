# softmodem 2400 & V.34-like protocol and WebSerial
<img width="1159" height="902" alt="image" src="https://github.com/user-attachments/assets/33436736-25e8-4cfc-a058-8de0b43471b2" />

<img width="1204" height="871" alt="image" src="https://github.com/user-attachments/assets/10cbdc06-df5f-4189-8f18-67a6e9b0dfbc" />

<img width="770" height="841" alt="image" src="https://github.com/user-attachments/assets/280b4066-8189-4d3c-a392-2db838df27bc" />


# SoftModem — V.BoOm High-Speed Software Modem

> **A real-time, browser-based software modem pushing audio communications far beyond classic dial-up.**

**V.BoOm** is an experimental high-speed software modem designed to explore how far modern DSP techniques can push data transmission through ordinary audio hardware.

Built entirely in **JavaScript/Web Audio**, the project combines adaptive QAM, adaptive equalization, carrier and timing recovery, trellis coding, error correction, link-layer framing, and real-time audio processing into a complete modem stack.

It is heavily inspired by the engineering ideas behind classic high-speed modems such as **V.32/V.34**, while remaining an independent experimental protocol rather than claiming V.34 compatibility.

---

## 🚀 Why V.BoOm?

Classic dial-up modems were engineering masterpieces.

V.BoOm takes the same fundamental idea — **turning an audio channel into a data channel** — and brings it into a modern browser.

Instead of simply generating tones, V.BoOm implements an actual adaptive communication system:

```text
                    V.BoOm MODEM

        ┌──────────────────────────────────┐
        │          APPLICATION DATA        │
        └────────────────┬─────────────────┘
                         ↓
                  V.42 / LAPM Layer
                         ↓
                Framing / CRC / ARQ
                         ↓
                 Trellis Coding
                         ↓
              Adaptive QAM Mapper
                         ↓
                 RRC Pulse Shaping
                         ↓
                 Audio Modulation
                         ↓
                ╔═══════════════╗
                ║  AUDIO CHANNEL ║
                ║  Noise / Echo  ║
                ║  Distortion    ║
                ╚═══════╤═══════╝
                         ↓
                 AGC / Filtering
                         ↓
              Adaptive FFE Equalizer
                         ↓
              Carrier Recovery
                         ↓
               Timing Recovery
                         ↓
                  QAM Demodulator
                         ↓
                 Viterbi Decoder
                         ↓
                 CRC / ARQ / LAPM
                         ↓
                    APPLICATION
```

This is not just a tone generator.

**It is a complete experimental modem stack.**

---

# ⚡ Key Features

## High-Speed V.BoOm Mode

V.BoOm provides an adaptive QAM-based modulation system with multiple constellation sizes:

| Modulation   | Bits / Symbol | Approx. Gross Rate @ 4800 baud |
| ------------ | ------------- | ------------------------------ |
| QPSK / 4-QAM | 2             | 9.6 kbps                       |
| 16-QAM       | 4             | 19.2 kbps                      |
| 64-QAM       | 6             | 28.8 kbps                      |
| 256-QAM      | 8             | 38.4 kbps                      |
| 1024-QAM     | 10            | **48 kbps**                    |

The modem can dynamically select the constellation according to channel conditions.

A clean channel can use a high-order constellation.

A noisy channel can fall back to a more robust modulation.

```text
GOOD CHANNEL
1024-QAM
   ↓
256-QAM
   ↓
64-QAM
   ↓
16-QAM
   ↓
4-QAM
NOISY CHANNEL
```

The result is a modem that can trade **speed for robustness** instead of simply failing when the channel gets worse.

> **48 kbps is a theoretical gross V.BoOm rate, not a claim of V.34 compatibility or guaranteed application throughput.**

---

# 🧠 Adaptive DSP

The receiver is designed around real communication-channel problems rather than an ideal mathematical channel.

### Included DSP techniques

* Automatic Gain Control (AGC)
* Root Raised Cosine filtering
* Complex Feed-Forward Equalizer (FFE)
* Adaptive LMS equalization
* Carrier recovery
* Timing recovery
* Gardner-style timing error detection
* QAM demodulation
* EVM estimation
* SNR estimation
* Adaptive constellation selection
* Retraining
* Carrier-loss detection

The adaptive equalizer is particularly important.

Real audio channels are not flat, clean transmission lines.

They introduce:

* frequency-dependent attenuation
* phase distortion
* echoes
* filtering
* clock mismatch
* noise
* nonlinear behavior

V.BoOm attempts to compensate for these effects in real time.

---

# 📡 Trellis-Coded Modulation

V.BoOm also includes a trellis-coded modulation path inspired by the techniques used by high-speed telephone modems.

The implementation includes:

* multi-state trellis coding
* soft-decision decoding
* Viterbi decoding
* multidimensional symbol processing

This allows the modem to obtain additional coding gain without simply increasing the transmit power.

The V8 implementation uses a 16-state Viterbi decoder and reports approximately **3.7–4.1 dB of coding gain in its experimental measurements**.

This is one of the areas where V.BoOm goes well beyond a conventional "QAM demo".

---

# 🔄 Adaptive Data Rate

The modem continuously evaluates channel quality and can change modulation according to the current conditions.

Conceptually:

```text
                 CHANNEL QUALITY
                        │
            ┌───────────┴───────────┐
            ↓                       ↓
          HIGH                    LOW
            │                       │
            ↓                       ↓
       1024-QAM                  4-QAM
            │                       │
            ↓                       ↓
       HIGH SPEED              HIGH ROBUSTNESS
```

This is fundamental to practical high-speed modem design.

A modem should not insist on maximum speed when the channel cannot support it.

Instead:

> **Use the fastest constellation the channel can reliably handle.**

V.BoOm can also trigger retraining when channel conditions deteriorate significantly.

---

# 🔗 V.42 / LAPM

V.BoOm is not only a physical-layer experiment.

The project also includes an experimental V.42/LAPM-style link layer featuring concepts such as:

* HDLC-style framing
* CRC-16
* bit stuffing
* acknowledgements
* retransmission
* receive-ready / reject handling
* sequence numbers
* sliding-window style operation
* connection establishment
* link timers

This provides the foundation for reliable data transmission over an imperfect audio channel.

In other words:

```text
Audio errors
     ↓
Physical layer detects corruption
     ↓
CRC detects bad frames
     ↓
LAPM requests retransmission
     ↓
Correct data reaches application
```

The goal is not merely to transmit data.

The goal is to **transmit data reliably**.

---

# ☎️ Classic Modem Modes

V.BoOm also contains implementations of classic low-speed modem techniques.

### V.21

**300 bit/s FSK**

Useful for experimentation with traditional Bell/ITU-style low-speed modem signaling.

### V.22

**1200 bit/s DPSK**

A classic 1200-baud modem mode.

### V.22bis

**2400 bit/s 16-QAM**

A classic high-speed dial-up milestone.

These modes provide a useful progression:

```text
V.21
  ↓
V.22
  ↓
V.22bis
  ↓
V.BoOm
```

From classic FSK all the way to experimental high-order QAM.

---

# 🎧 Real-Time Audio

V.BoOm is designed to work with real audio devices through the browser.

The project uses modern Web Audio APIs and supports real-time audio processing.

This means the modem can be connected to:

* microphone input
* speakers
* audio interfaces
* virtual audio cables
* external communication hardware
* WebSerial-connected equipment

The objective is simple:

> **If you can get audio in and audio out, you have a potential modem channel.**

---

# 🔌 WebSerial

The V8 interface includes **WebSerial support**, allowing the modem to expose a serial-style data interface directly from a compatible browser.

This makes the project much more than an audio visualizer.

It opens the door to experiments such as:

```text
Computer
   │
   │ Serial
   ↓
V.BoOm
   │
   │ Audio
   ↓
Audio Channel
   │
   ↓
V.BoOm
   │
   │ Serial
   ↓
Computer
```

This architecture makes it possible to experiment with actual applications rather than simply transmitting predefined test patterns.

---

# 📈 Built-In Channel Simulation

V.BoOm includes a simulated communication channel for development and testing.

The simulator can model effects such as:

* additive noise
* echo
* attenuation
* bandwidth limitations
* channel distortion
* audio processing effects

This is extremely useful because DSP development becomes much easier when the same channel can be reproduced repeatedly.

Instead of asking:

> "Why did it fail this time?"

you can ask:

> "What happens when I reduce SNR by 3 dB?"

That makes the project useful as a **DSP experimentation platform**, not just a modem.

---

# 📊 Diagnostics

The modem exposes useful measurements during operation, including concepts such as:

* SNR
* EVM
* constellation state
* carrier lock
* timing state
* equalizer behavior
* error conditions
* retraining
* current modulation

The constellation can be observed directly, making it possible to see the modem transition from a clean high-order QAM signal to a lower-order, more robust modulation as channel conditions change.

---

# 🧪 Experimental Nature

V.BoOm is intentionally experimental.

It is inspired by established modem engineering, but it is **not an implementation of ITU-T V.34**.

The protocol, training sequence, modulation selection, signaling, and implementation details are specific to this project.

Think of it as:

> **"What if we rebuilt the ideas behind high-speed dial-up modems from scratch using modern browser DSP?"**

rather than:

> "This is a V.34 modem."

That distinction is important.

---

# 🆚 V.BoOm vs Classic V.34

| Feature                           | V.BoOm | V.34 |
| --------------------------------- | -----: | ---: |
| Adaptive QAM                      |      ✅ |    ✅ |
| Adaptive rate                     |      ✅ |    ✅ |
| Adaptive equalization             |      ✅ |    ✅ |
| Carrier recovery                  |      ✅ |    ✅ |
| Timing recovery                   |      ✅ |    ✅ |
| Trellis coding                    |      ✅ |    ✅ |
| Viterbi decoding                  |      ✅ |    ✅ |
| Error correction / ARQ            |      ✅ |    ✅ |
| Real-time audio                   |      ✅ |    ✅ |
| Browser-native                    |      ✅ |    ❌ |
| WebSerial                         |      ✅ |    ❌ |
| V.34 compatible                   |      ❌ |    ✅ |
| V.34 standardized training        |      ❌ |    ✅ |
| Experimental 48 kbps mode         |      ✅ |    ❌ |
| Open-source experimental protocol |      ✅ |    ❌ |

V.BoOm should therefore be viewed as a **V.34-inspired experimental modem**, not a replacement or drop-in compatible V.34 implementation.

---

# 🏗️ Project Architecture

The project is intentionally self-contained and can run directly in a modern browser.

The V8 implementation combines:

```text
JavaScript
├── Modem State Machine
├── TX DSP
│   ├── Scrambling
│   ├── Trellis Encoding
│   ├── QAM Mapping
│   └── RRC Filtering
│
├── RX DSP
│   ├── AGC
│   ├── FFE
│   ├── Carrier Recovery
│   ├── Timing Recovery
│   ├── QAM Detection
│   └── Viterbi Decoder
│
├── Link Layer
│   ├── Framing
│   ├── CRC
│   ├── LAPM
│   └── Retransmission
│
├── Channel Simulator
│   ├── Noise
│   ├── Echo
│   └── Distortion
│
├── Web Audio
└── WebSerial
```

The fact that this entire communication chain can run in a browser is one of the main goals of the project.

---

# 🖥️ Running the Project

Clone the repository:

```bash
git clone https://github.com/kaboom748/softmodem.git
cd softmodem
```

Then open the desired HTML modem implementation in a modern browser.

For the V8 experimental high-speed modem:

```text
vBoOomV8-WEBSERIAL.html
```

For the best results, use a Chromium-based browser with Web Audio support.

WebSerial requires a browser that supports the WebSerial API and appropriate permissions.

---

# 🎛️ Recommended Hardware

For real-world experiments, better audio hardware can make a significant difference.

Recommended:

* USB audio interface
* wired headphones/headsets
* good quality sound card
* direct audio connections
* shielded cables
* virtual audio cable for computer-to-computer testing

Avoid Bluetooth when testing modem performance.

Bluetooth audio codecs introduce latency, compression, filtering and packetization that can make modem DSP experiments considerably harder.

---

# 🔬 What Makes This Project Interesting?

Because it combines several areas of engineering into one project:

### Digital Signal Processing

QAM, filtering, equalization, synchronization and carrier recovery.

### Communications Theory

SNR, EVM, constellation design, coding gain and adaptive modulation.

### Embedded/Real-Time Thinking

Continuous processing of an audio stream under timing constraints.

### Networking

Framing, CRC, retransmission and reliable delivery.

### Web Technology

Web Audio, AudioWorklet-style processing and WebSerial.

### Retrocomputing

The entire project is inspired by the golden age of dial-up modem engineering.

---

# 🏆 From Dial-Up to the Browser

The history of modems is a fascinating progression:

```text
300 bps
  ↓
1200 bps
  ↓
2400 bps
  ↓
9600 bps
  ↓
14.4 kbps
  ↓
28.8 kbps
  ↓
33.6 kbps
  ↓
56 kbps
  ↓
        V.BoOm
   experimental browser DSP
```

V.BoOm is an attempt to revisit that engineering challenge with modern tools.

Instead of dedicated modem silicon, expensive DSP hardware, or a proprietary embedded system:

**the modem lives in JavaScript.**

And instead of a telephone line:

**the channel can be anything that can carry audio.**

---

# ⚠️ Limitations

This project is experimental and should not be confused with a production telecommunications modem.

In particular:

* V.BoOm is **not V.34 compatible**.
* The advertised 48 kbps figure is a **gross experimental modulation rate**.
* Actual throughput depends on modulation, coding, channel quality and protocol overhead.
* 1024-QAM requires a very clean channel and high SNR.
* Consumer microphones/speakers can significantly affect performance.
* Browser scheduling and audio hardware can introduce timing constraints.
* WebSerial availability depends on the browser and operating system.
* Real telephone networks may impose additional filtering, echo and codec limitations.

The project is best considered a **research, experimentation and educational platform**.

---

# 🛠️ Roadmap / Ideas

Potential future improvements include:

* [ ] More robust automatic training
* [ ] Improved carrier recovery
* [ ] Improved timing recovery
* [ ] More sophisticated adaptive equalization
* [ ] Better channel estimation
* [ ] More advanced constellation selection
* [ ] Improved soft-decision decoding
* [ ] More efficient framing
* [ ] Throughput benchmarking
* [ ] Automated BER testing
* [ ] Automated SNR sweeps
* [ ] Real telephone-line experiments
* [ ] Multi-channel experiments
* [ ] Additional modem standards
* [ ] Improved mobile/browser support
* [ ] Better interoperability tools
* [ ] Hardware DSP acceleration

---

# ❤️ Why Build This?

Because **modems are cool**.

There is something deeply satisfying about taking:

```text
BITS
 ↓
MATH
 ↓
SIGNAL
 ↓
AUDIO
 ↓
NOISE
 ↓
MATH
 ↓
BITS
```

and actually making it work.

V.BoOm is an exploration of that entire process.

It combines old-school telecommunications engineering with modern browser technology and tries to answer a simple question:

> **How much data can we really push through an ordinary audio channel using nothing but software?**

---

# 📜 Disclaimer

V.BoOm is an independent experimental project.

It is **not affiliated with or endorsed by the ITU-T** and should not be considered a compliant implementation of V.21, V.22, V.22bis, V.34 or V.42 unless explicitly stated otherwise for a specific component.

Classic modem terminology is used to describe engineering concepts and historical inspiration.

---

# 🤝 Contributing

Contributions, experiments, benchmarks and DSP improvements are welcome.

Interesting contributions include:

* better synchronization algorithms
* improved equalizers
* improved channel models
* BER measurements
* real hardware testing
* performance optimization
* new modulation schemes
* documentation
* interoperability experiments

If you have an idea that sounds completely unreasonable:

**try it.**

That's exactly what this project is for.

---

# ⭐ Final Words

V.BoOm is not trying to pretend that a few lines of JavaScript magically replace decades of telecommunications standards.

It is doing something more interesting:

**rebuilding the ideas.**

From FSK to QAM.

From simple tone detection to adaptive equalization.

From a few hundred bits per second to experimental tens-of-kilobits-per-second transmission.

From dedicated modem hardware to a browser tab.

**This is software-defined modem engineering — running in your browser.**

---

## 🔥 V.BoOm

**Hear the bits.**

**Shape the signal.**

**Fight the noise.**

**Recover the data.**

**Make the modem scream.** 📡
