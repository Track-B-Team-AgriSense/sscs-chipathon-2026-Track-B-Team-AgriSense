## FAQs

<details>
<summary>🌾1. Why agriculture? Why agricultural drones?</summary>

Agriculture is one of the few domains where small improvements in monitoring and decision-making directly translate to massive economic and environmental impact:
  
  a. Farms are large (often hundreds of hectares). Manual crop inspection is slow, labour‑intensive, and inconsistent.
  
  b. Crop stress is time‑critical: Nutrient deficiency, water stress, pest infestation, and disease need to be caught early. A delay of a few days can mean the difference between a minor treatment and a total crop loss.
  
  c. Data is spatial: Plant health varies across a field. A ground‑based sensor can only measure one point. A drone can cover the entire field quickly and repeatedly.

These factors make agriculture a perfect target for automated, high‑coverage, low‑cost sensing — exactly what a small, power‑efficient edge‑AI chip enables.


Why Drones Are the Ideal Mobile Sensor Carrier:

a. High spatial resolution: Flies low (5–20 m) to achieve centimeter-level optical resolution for detailed imaging [compared to satellite's meter-level]

b. High temporal frequency: Can survey a field in minutes and redeploy on-demand, multiple times per day (vs. satellites: days to weeks)

c. Low cost per flight: Much more affordable than ground robots (high hardware cost) or deploying many fixed sensors

d. Full autonomy: Supports GPS waypoint navigation for autonomous operation (unlike ground robots with limited autonomy)

e. Close-range sensing capability: Low altitude enables close-range gas and humidity sensing that satellites and ground robots cannot achieve

f. Mobile flexibility: Unlike fixed sensors (point-only coverage), drones can survey entire fields and move to any location whenever needed

g. Best balance of all factors: Combines high resolution, high frequency, low cost, and autonomy — something no other platform offers alone

Why agricultural drones (not just general‑purpose): 
Agricultural drones already exist (e.g., DJI Agras for spraying), but they focus on actuation (spraying, spreading). The missing piece is smart sensing that can direct those actions precisely. Your chip fills that gap: it turns a basic sprayer drone into an autonomous scout that can identify where and when to act — reducing chemical use, saving water, and improving yield.

</details>

<details>
<summary>📊Why event‑driven sensing with local edge AI? Why not send all data for detailed analysis?</summary>
What happens if you send all raw data? Imagine the drone streams four sensor channels continuously at a modest rate (1 kSps each, 12‑bit). Data rate: 4 sensors × 1 kSps × 12 bits = 48 kbps raw sensor data alone.
Add packaging, timestamps, metadata: appx 60–100 kbps.

To transmit this reliably from a moving drone to a ground station over tens or hundreds of metres, you need a radio like LoRa (~5 kbps max), WiFi (~1–10 Mbps but power‑hungry), or 4G/LTE (power‑hungry, requires cellular coverage).
Power cost of continuous radio transmission: A typical LoRa module consumes ~100 mW during TX.
WiFi module: 200–500 mW.

Even if you buffer and send in bursts, the radio is active a significant fraction of the time.
Meanwhile, our sensor payload’s power budget is a tiny fraction of that. For a drone that needs to fly for 30–60 min, every millijoule counts. Continuous streaming would dominate the energy budget, forcing a larger battery, which increases weight, which requires larger motors, which requires even more battery — a negative spiral.

The event‑driven alternative
Our chip does the opposite:

It sleeps at <100 nA, with only a low‑power comparator watching one sensor channel (e.g., humidity or a specific gas threshold).

When a significant change is detected, it wakes up, samples all four sensors, runs the TinyML model, and determines: “Is this crop stress (yes/no) and what type?”

Only the classification result (a few bytes: stress class, confidence, maybe a compressed sensor snapshot) is sent over SPI to the flight controller, which can then relay it via LoRa (very short packet, low energy) or store it for later download.

The result: the radio is off 99% of the time, and the entire payload power is under 1 mW active, negligible in sleep. This makes the system viable on a small battery.

But can you still get detailed crop health data?
Yes! Event‑driven does not mean you lose information. It means you change when you send it:
On‑demand streaming: The flight controller can command the chip over SPI: “Stream raw data from the optical sensor now.” The chip enters a higher‑power streaming mode only when needed (e.g., when a farmer wants to inspect a specific area in detail).
Local logging: In a real product, the chip could store longer segments in off‑chip flash (the same SPI flash that holds model weights) and offload them after the drone lands. This gives you rich time‑series data without in‑flight transmission energy.
Smart compression: The TinyML model itself extracts features; you could send those feature vectors (much smaller than raw data) to the ground station for more sophisticated analysis, combining the best of both worlds.
The chip acts as an intelligent filter, ensuring that every transmitted byte has high value.

</details>

<details>
<summary>➡️How does the event‑driven flow use fusion?</summary>
The sequence is:

  1. Deep sleep – Only a single‑channel wake‑up comparator monitors one sensor (e.g., humidity, or a broadband gas signal) with a programmable threshold. This is NOT fusion — it’s a cheap, always‑on trigger.

2. Wake event – If the threshold is crossed, the digital core powers up.

3. Sensor burst – The AFE sequentially reads all four sensors (temp, humidity, gas, optical) into the 2 KB SRAM buffer.

4. Inference – The TinyML accelerator runs the 1D‑CNN on the 4‑channel input vector and classifies the crop stress type/severity.

5. Alert (if needed) – The classification result is sent via SPI to the drone controller, which can then decide to send a radio packet, change flight path, or trigger a photo/video capture.

So fusion happens in step 4 — the model sees the full multi‑modal picture before making a decision. The wake‑up trigger is just a gatekeeper to avoid running the digital core continuously.
</details>

