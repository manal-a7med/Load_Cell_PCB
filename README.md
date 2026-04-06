# Load_Cell_PCB
HX711 Load Cell PCB Design using KiCAD 10.0


Manal Ahmed Matheen
ELECTRONICS ASSIGNMENT REPORT
Automated Food Dispensing System — Load Cell Measurement Subsystem

Task	Design Problem Statement
Domain	Embedded Systems / PCB Design
Status	Internship Technical Submission
Date	31 March 2026

Task 1 — Load Cell Selection and Key Specifications

The load cell is the primary transducer in this food dispensing system. It converts the mechanical force (weight of dispensed food) into an electrical signal. Choosing the right load cell is critical for accuracy, durability, and noise immunity in an industrial or semi-industrial environment.

1.1 Recommended Load Cell — Recommendation

✔ Selected	HX711-compatible Single-Point Load Cell (e.g., TAL220B / CZL635 / Bosche H40A) — Strain-gauge type, single-point platform cell

Reason	Strain-gauge load cells offer excellent linearity, low hysteresis, and are widely compatible with 24-bit ADC front-ends. Single-point cells are ideal for platform-style food-pan weighing.

1.2 Key Specifications to Consider

A. Load Capacity
•	Select rated capacity based on maximum expected food load plus a 20–30% safety margin.
•	For a food dispenser pan: 1 kg to 5 kg rated capacity is typical (e.g., 5 kg full-scale).
•	Overload protection rating should be ≥150% of rated capacity to survive mechanical disturbances.

B. Accuracy & Linearity
Specification targets for a reliable food-dispensing system:

Specification	Typical Requirement	Recommended Value
Non-linearity	< ±0.05% FS	< ±0.02% FS
Hysteresis	< ±0.05% FS	< ±0.02% FS
Repeatability	< ±0.05% FS	< ±0.01% FS
Temperature Effect (Zero)	< ±0.03% FS/°C	< ±0.02% FS/°C
Creep (30 min)	< ±0.03% FS	< ±0.02% FS

C. Sensitivity & Output
•	Standard bridge sensitivity: 1–3 mV/V (e.g., 2 mV/V at full rated load).
•	Bridge excitation voltage: 5V or 10V (match to ADC front-end supply).
•	Higher sensitivity = better SNR before amplification.

D. Resistance to Mechanical Disturbances
•	IP Rating: Minimum IP54 for food/dust environments; IP65 preferred.
•	Material: Aluminium alloy body (anodised) for food-grade applications; stainless steel for wet wash-down.
•	Mechanical shock rating: ≥10g or higher to withstand dispensing impacts.
•	Anti-vibration: Use rubber mounting isolators under the cell to decouple conveyor/motor vibration.

E. Electrical Noise Immunity
•	Use a 4-wire (Wheatstone bridge) or 6-wire load cell for remote sense compensation.
•	Shield the signal cable and connect the shield to a single-point earth at the MCU side.
•	Digital filtering in firmware (moving average / IIR) to suppress 50 Hz mains interference.

1.3 Comparative Summary of Load Cell Types

Type	Pros	Cons	Suitability
Strain Gauge (Single Point)	Low cost, compact, good linearity	Sensitive to off-center loads beyond cell rating	✔ Best for this application
Strain Gauge (Bending Beam)	High accuracy, robust	Larger footprint	Good alternative
Capacitive	No hysteresis, very accurate	Complex electronics, higher cost	Overkill for this use
Piezoelectric	Fast dynamic response	Cannot measure static weight accurately	✘ Not suitable

 
Task 2 — ADC Selection for Load Cell Signal Acquisition

Load cell output signals are extremely small (millivolt-range differential signals). A high-resolution, low-noise ADC with an integrated instrumentation amplifier front-end is required. The ADC communicates directly with the MCU.

2.1 Recommended ADC — HX711

✔ Selected	HX711 — 24-bit Sigma-Delta ADC with integrated PGA (Programmable Gain Amplifier), purpose-built for load cell / Wheatstone bridge applications

2.2 HX711 Key Specifications

Parameter	Value
Resolution	24-bit (≈16 million counts)
Input Channels	2 differential inputs (A and B)
Programmable Gain (Ch A)	128× or 64×
Programmable Gain (Ch B)	32×
Data Rate	10 SPS or 80 SPS (selectable via RATE pin)
Supply Voltage (AVDD)	2.6V – 5.5V
Supply Voltage (DVDD)	2.6V – 5.5V
Noise RMS (Ch A, Gain 128)	50 nV RMS typical
Interface	2-wire serial (PD_SCK / DOUT) — not standard SPI/I2C
Temperature Range	−40°C to +85°C
Package	SOP-16

2.3 Why HX711 Over General-Purpose ADCs
•	Integrated PGA eliminates need for an external instrumentation amplifier (reduces BOM and board space).
•	24-bit resolution provides 0.3 μV/count sensitivity at 5V excitation with gain 128 — sufficient for detecting sub-gram weight changes.
•	Differential input rejects common-mode noise from long cable runs.
•	Simple 2-wire bit-bang interface works with any MCU without SPI/I2C peripheral.
•	Low cost (~$1–2 per unit in volume), widely available, extensive community support.

2.4 Alternative High-Performance ADC — ADS1232 (Texas Instruments)

Alternative	ADS1232 — 24-bit Sigma-Delta ADC with integrated PGA, for applications requiring higher accuracy and temperature performance

Feature	HX711	ADS1232
Resolution	24-bit	24-bit
Data Rate	10 / 80 SPS	10 / 40 / 640 / 2000 SPS
Input Channels	2 differential	2 differential
Max Gain	128×	128×
Noise @ 10 SPS	~50 nV RMS	~17 nV RMS
Price (Approx.)	$1–2	$5–8
Interface	Custom 2-wire	SPI-compatible
Best For	Cost-sensitive, basic weighing	High-precision industrial weighing

For this internship system, the HX711 is the recommended choice due to its cost-effectiveness, simplicity, and adequate resolution for food dispensing applications.

 
Task 3 — Connectors and Cables Selection

Proper connector and cable selection is essential to maintain signal integrity for the millivolt-level load cell signals, ensure mechanical robustness, and provide EMI immunity.

3.1 Load Cell Output Cable (Load Cell → HX711/ADC)

Cable Specification

✔ Recommended	4-core shielded twisted pair (STP) cable — 24 AWG minimum, overall braid shield + foil shield, PVC or PUR jacket

Parameter	Specification
Cable Type	4-core shielded twisted pair (STP)
Conductor Gauge	24 AWG (0.205 mm²) minimum
Shielding	Braid + foil (double shield) for best EMI rejection
Jacket Material	PVC (dry environments) / PUR (wash-down / flexible routing)
Max Cable Length	≤ 3 m without additional buffering (use 6-wire for longer runs)
Color Coding (Standard)	Red=EXC+, Black=EXC−, Green=SIG+, White=SIG−, (Yellow=SEN+, Blue=SEN− for 6-wire)
Temperature Range	−20°C to +80°C

Connector at Load Cell End
•	M8 4-pin circular connector (IP65/IP67 rated) — push-pull or screw-lock.
•	Manufacturer examples: Binder Series 763, Amphenol M8, Phoenix Contact M8.
•	Use nickel-plated brass shell for corrosion resistance in food environments.

Connector at PCB/MCU End
•	Molex Micro-Fit 3.0 (2×2 or 1×4) — locking, 3A rated, vibration resistant.
•	Alternatively: JST-XH 2.54mm pitch 4-pin — lower cost, adequate for PCB prototyping.
•	For industrial boards: Phoenix Contact MSTB 2.5mm pitch screw terminal block.

3.2 HX711 → MCU Digital Interface Connections

Signal	Pin on HX711	Connector Type	Notes
PD_SCK (Clock)	Pin 11	Standard PCB header 2.54mm	Push-pull output from MCU GPIO
DOUT (Data Out)	Pin 12	Standard PCB header 2.54mm	Open-drain; add 10kΩ pull-up
VCC (DVDD)	Pin 14/15	Power header or onboard trace	3.3V or 5V from MCU regulator
GND	Pin 16	Common PCB ground	Single-point ground near ADC
AVDD (Analog)	Pin 1	Separate filtered supply	100nF + 10μF decoupling to AGND

3.3 Grounding and Shielding Best Practices
•	Connect cable shield to AGND at the PCB side only (single-point grounding prevents ground loops).
•	Separate analog ground (AGND) from digital ground (DGND); join at a single star point.
•	Route analog signal traces away from digital clock traces and switching power supply traces on the PCB.
•	Add ferrite bead (e.g., Murata BLM18AG, 600Ω @ 100MHz) on AVDD supply line to ADC.

 
Task 4 — Embedded Code for ADC Data Acquisition

The following code is written for the Arduino Uno (ATmega328P) MCU using the Arduino framework. It interfaces with the HX711 24-bit ADC via bit-banging the custom 2-wire serial protocol. The code includes tare (zero offset), scale calibration, moving average filtering, and serial output.

4.1 HX711 Hardware Interface Protocol
The HX711 uses a proprietary clock-gated serial protocol. When DOUT goes LOW, data is ready. The MCU generates 24 rising edges on PD_SCK to clock out 24 data bits MSB-first, followed by 1–3 additional pulses to configure channel and gain for the next conversion.

4.2 Complete Arduino Firmware

/*
 * HX711 Load Cell ADC Interface — Food Dispensing System
 * MCU: Arduino Uno (ATmega328P)
 * Author: Engineering Intern
 * Date: 2024
 */

#include <Arduino.h>

// ─── Pin Definitions ──────────────────────────────────────
#define HX711_DOUT    2    // Data Out pin (INT0 for optional interrupt use)
#define HX711_SCK     3    // Serial Clock pin

// ─── Configuration ────────────────────────────────────────
#define HX711_GAIN    1    // 1 = Ch A Gain 128 (2 pulses extra)
                           // 2 = Ch B Gain 32  (3 pulses extra)
                           // 3 = Ch A Gain 64  (4 pulses extra)
#define SAMPLES       10   // Moving average window size
#define SERIAL_BAUD   115200

// ─── Calibration Constants ────────────────────────────────
long   tareOffset  = 0;        // Set by tare() function
float  scaleGram   = 410.0f;   // Counts per gram (calibrate empirically)

// ─── Function: Read raw 24-bit signed value from HX711 ─────
long hx711_read_raw(void) {
  // Wait until data ready (DOUT goes LOW)
  while (digitalRead(HX711_DOUT) == HIGH);

  long data = 0;

  // Clock out 24 bits, MSB first
  for (uint8_t i = 0; i < 24; i++) {
    digitalWrite(HX711_SCK, HIGH);
    delayMicroseconds(1);
    data = (data << 1) | digitalRead(HX711_DOUT);
    digitalWrite(HX711_SCK, LOW);
    delayMicroseconds(1);
  }

  // Extra pulses to configure gain for next reading
  for (uint8_t i = 0; i < HX711_GAIN; i++) {
    digitalWrite(HX711_SCK, HIGH);
    delayMicroseconds(1);
    digitalWrite(HX711_SCK, LOW);
    delayMicroseconds(1);
  }

  // Sign-extend 24-bit two's complement to 32-bit
  if (data & 0x800000) {
    data |= 0xFF000000;  // Extend sign bit
  }

  return data;
}

// ─── Function: Moving Average Filter ──────────────────────
long hx711_read_average(uint8_t n) {
  long sum = 0;
  for (uint8_t i = 0; i < n; i++) {
    sum += hx711_read_raw();
  }
  return sum / n;
}

// ─── Function: Set tare (zero point) ──────────────────────
void tare(void) {
  Serial.println("Taring... ensure pan is EMPTY.");
  delay(1000);
  tareOffset = hx711_read_average(20);  // Average 20 readings for stable zero
  Serial.print("Tare offset set: ");
  Serial.println(tareOffset);
}

// ─── Function: Get weight in grams ────────────────────────
float get_weight_grams(void) {
  long raw = hx711_read_average(SAMPLES) - tareOffset;
  return (float)raw / scaleGram;
}

// ─── Function: Power down HX711 (sleep mode) ──────────────
void hx711_power_down(void) {
  digitalWrite(HX711_SCK, LOW);
  digitalWrite(HX711_SCK, HIGH);
  delayMicroseconds(65);  // Hold SCK HIGH >60μs to enter power-down
}

// ─── Function: Wake up HX711 ──────────────────────────────
void hx711_power_up(void) {
  digitalWrite(HX711_SCK, LOW);
  delay(500);  // Allow ADC to settle after wake
}

// ─── Setup ────────────────────────────────────────────────
void setup() {
  Serial.begin(SERIAL_BAUD);
  pinMode(HX711_DOUT, INPUT);
  pinMode(HX711_SCK, OUTPUT);
  digitalWrite(HX711_SCK, LOW);

  Serial.println("===================================");
  Serial.println(" Food Dispenser Load Cell System  ");
  Serial.println("===================================");
  delay(500);

  tare();  // Zero the scale on startup

  Serial.println("System ready. Monitoring weight...");
  Serial.println("-----------------------------------");
}

// ─── Main Loop ────────────────────────────────────────────
void loop() {
  float weight = get_weight_grams();

  Serial.print("Weight: ");
  Serial.print(weight, 2);
  Serial.println(" g");

  // Optional: threshold alarm for target dispensing weight
  const float TARGET_GRAMS = 200.0f;
  const float TOLERANCE    = 2.0f;

  if (weight >= (TARGET_GRAMS - TOLERANCE) &&
      weight <= (TARGET_GRAMS + TOLERANCE)) {
    Serial.println(">>> TARGET WEIGHT REACHED — Stop dispensing!");
  }

  delay(200);  // 5 Hz update rate (HX711 at 80 SPS with 10-sample average)
}

4.3 Calibration Procedure
1.	Remove all weight from the pan and power on the system.
2.	The tare() function runs automatically at startup — establishes zero reference.
3.	Place a known calibration weight (e.g., exactly 200 g certified weight) on the pan.
4.	Read the raw value via Serial Monitor.
5.	Calculate: scaleGram = raw_reading / known_weight_in_grams
6.	Update the scaleGram constant in firmware and reflash.
7.	Verify accuracy with additional reference weights across the measurement range.

4.4 Key Firmware Design Features
Feature	Implementation	Purpose
Moving Average Filter	hx711_read_average(N)	Reduce noise from vibration & EMI
Sign Extension	Two's complement 24→32 bit	Correct negative weight readings
Tare / Zero Offset	tareOffset subtraction	Compensate for pan weight
Scale Factor	Float division by scaleGram	Convert raw counts to grams
Power Management	hx711_power_down/up()	Reduce current between measurements
Target Detection	Threshold ± tolerance check	Trigger dispenser stop signal

 
Task 5 — PCB Design: Schematics and Board Files

The PCB integrates the HX711 ADC, power regulation, EMI filtering, MCU interface connector, and load cell connector. The design follows mixed-signal PCB best practices to ensure clean analog measurements.

5.1 PCB Design Tool Recommendation

✔ Recommended Tool	KiCad 7.0 (Open-source, professional-grade EDA) — Full schematic + PCB layout with Gerber export for manufacturing

5.2 Block-Level System Schematic Description

The schematic is organized into the following functional blocks:
•	Block 1 — Power Supply: 5V input (USB or barrel jack) → AMS1117-3.3 LDO regulator → 3.3V rail for MCU digital section. Separate ferrite-bead-isolated AVDD (5V analog) for HX711 reference input.
•	Block 2 — Load Cell Interface: M8 4-pin connector → 100nF decoupling capacitors on EXC+/EXC− → HX711 INNA/INNB inputs. Shield connected to AGND via 1MΩ resistor.
•	Block 3 — HX711 ADC: SOP-16 IC, AVDD decoupled with 10μF tantalum + 100nF MLCC. DVDD separate from AVDD with ferrite bead isolation. RATE pin tied to GND (10 SPS, lower noise mode).
•	Block 4 — MCU Interface: 4-pin 2.54mm header for DOUT, PD_SCK, VCC, GND to Arduino Uno or custom MCU board. 10kΩ pull-up on DOUT line.
•	Block 5 — Protection & Filtering: TVS diode (PRTR5V0U2X) on load cell signal lines. Common-mode choke on EXC supply lines.

5.3 Component Bill of Materials (BOM)

Ref.	Component	Value / Part No.	Package	Quantity
U1	ADC	HX711	SOP-16	1
U2	LDO Regulator	AMS1117-3.3	SOT-223	1
J1	Load Cell Connector	M8 4-pin female PCB	M8 THT	1
J2	MCU Header	Pin Header 1×4	2.54mm THT	1
J3	Power Input	Micro-USB / Barrel Jack	THT	1
C1, C2	AVDD Decoupling	10μF Tantalum	SMD 1206	2
C3–C6	Bypass Caps	100nF MLCC X7R	SMD 0402	4
R1	DOUT Pull-up	10kΩ	SMD 0402	1
R2	Shield Bleed	1MΩ	SMD 0402	1
FB1	Ferrite Bead	600Ω@100MHz (Murata BLM18AG)	SMD 0402	1
D1	ESD Protection	PRTR5V0U2X TVS	SOT-363	1
				

5.4 PCB Layout Guidelines

Layer Stack-up (2-layer board)
•	Top Layer (Signal): Component placement, analog signal routing, digital routing.
•	Bottom Layer (Ground Plane): Solid copper pour for GND — reduces impedance and acts as EMI shield.

Critical Routing Rules
Rule	Value / Method
Analog signal trace width	0.2 mm minimum (keep short, direct)
Digital signal trace width	0.2 mm
Power trace width	0.8 mm minimum (1.0 mm for 5V input)
Analog/Digital separation	Physical split in ground plane, joined at single star point
HX711 AVDD decoupling placement	< 3 mm from AVDD pin — C1/C2 placed first in routing
Load cell signal routing	Differential pair, matched length, away from clock traces
Trace clearance (EXC to SIG)	≥ 0.2 mm to prevent cross-talk
Silkscreen	Label all connectors, test points, and polarity markers

5.5 PCB Manufacturing Specifications

Specification	Value
Board Size	60 mm × 40 mm initially, but changed later.
Layer Count	2 (signal + ground plane)
Board Material	FR4 1.6 mm
Copper Weight	1 oz (35 μm)
Surface Finish	HASL (Hot Air Solder Level) or ENIG for fine-pitch pads
Solder Mask	Green (or blue for identification)
Silkscreen	White (both sides)
Gerber Files Required	F.Cu, B.Cu, F.Mask, B.Mask, F.SilkS, Edge.Cuts, drill file (.drl)


PLESE NOTE :-
 A barrel jack power input connector (CUI PJ-102AH) is recommended for the production version to provide a dedicated 5V supply input. In this prototype version, 5V power is supplied directly through the J2 MCU connector Pin 3.

5.6 Test Plan for PCB Validation
Visual Inspection: Check all solder joints, no shorts or bridges under magnification.

Power-On Test: Apply 5V, measure 3.3V rail (expect 3.3V ±2%), verify AVDD (expect 5V               ±1%).

HX711 Communication Test: Flash firmware, check Serial Monitor for valid (non-zero) readings.

Noise Floor Test: Short INNA/INNB inputs, record raw values — noise floor should be < ±5 counts.

Load Cell Integration Test: Connect reference load cell, apply known weights, verify accuracy.

Vibration Test: Apply mechanical disturbances, confirm moving-average filter rejects spikes.

Temperature Soak: Run at 10°C and 50°C, verify < ±0.05% FS reading drift.

 
References & Standards

•	HX711 Datasheet — Avia Semiconductor, Rev 1.0. 24-Bit Analog-to-Digital Converter for Weigh Scales.
•	ADS1232 Datasheet — Texas Instruments, SBAS424. 24-Bit Analog-to-Digital Converter.
•	OIML R 60 — International Recommendation for Metrological Regulation of Load Cells.
•	IEC 61000-4-5 — Electromagnetic Compatibility: Surge Immunity Requirements.
•	KiCad EDA Documentation — https://docs.kicad.org, Version 7.0.
•	Analog Devices MT-031 Tutorial — Grounding Data Converters and Solving the Mystery of 'AGND' and 'DGND'.
•	Arduino Reference Documentation — https://www.arduino.cc/reference/en/
•	Phoenix Contact MSTB Connector Series Datasheet — Phoenix Contact GmbH.

— End of Report —
