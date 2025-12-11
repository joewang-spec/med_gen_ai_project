🏗️ Part 1: The Architecture Guide
To understand why the U-Net works, we must first understand what happens when we break it. We implemented a "Hackable U-Net" that allows us to toggle specific feature pathways on and off.

1. No Skips (The Baseline)
The Bottleneck: Without skip connections, the U-Net acts like a standard Autoencoder. The image is compressed into a tiny latent vector (bottleneck) and then upsampled. All fine-grained spatial data is lost in the compression. <img width="875" alt="No Skip Architecture" src="https://github.com/user-attachments/assets/08c97e97-b0f4-4a94-8038-c744e7145640" />

2. Deep Skips Only
The Semantic Bridge: We activate only the deepest connections. These transfer low-resolution, high-context features (e.g., "this is a polyp") directly from the bottom of the encoder to the decoder. <img width="909" alt="Deep Skips Architecture" src="https://github.com/user-attachments/assets/e0947788-4dc8-4d63-be09-30e9cc6402bc" />

3. Shallow Skips Only
The Edge Detector: We activate only the top-level connections. These transfer high-resolution raw pixel data (edges, textures) directly to the output, bypassing the semantic bottleneck entirely. <img width="897" alt="Shallow Skips Architecture" src="https://github.com/user-attachments/assets/e87b4604-6c14-4c08-902b-2b05b007cc60" />

4. Full U-Net
The Synthesis: The standard architecture. It attempts to fuse the "Where" (Shallow) with the "What" (Deep). <img width="839" alt="Full U-Net Architecture" src="https://github.com/user-attachments/assets/225ecb7f-f6b1-4df2-b6eb-3569cf9eb053" />
🔬 Part 2: Visual Deconstruction (Qualitative Results)
We analyzed the breakdown of the architecture using the Kvasir-SEG (Polyp) dataset. This visual strip demonstrates exactly how the model fails when specific connections are removed.

<img width="2270" alt="Visual Deconstruction Strip" src="https://github.com/user-attachments/assets/712c79b2-f6a4-470e-aa3f-ee8e27d56214" />

🔴 Failure Mode 1: "Tunnel Vision" (No Skips)
Observation: The model produces small, central blobs (see "No Skips" column above).

Explanation: Without skip connections, the encoder compresses the image into a tiny bottleneck. Spatial resolution is lost. The model knows roughly where the polyp is (center), but has lost the fine-grained coordinate data to define the edges.

🟠 Failure Mode 2: "Texture Blind" (Shallow Skips Only)
Observation: Sharp edges, but it misses the bulk of the mass.

Explanation: The shallow layers act as edge detectors. They see high-frequency changes (lines), but they lack the Semantic Receptive Field to understand that the "lumpy texture" at the top of the image is also part of the polyp. It sees the boundary but misses the object.

🟢 Success Mode: "Latent Identity" (Deep Skips Only)
Observation: Robust coverage of the entire polyp mass.

Explanation: Surprisingly, this variant outperformed even the Full U-Net on the Polyp dataset. Polyp detection is a Texture Task. The deep layers encode the "concept" of abnormal tissue. By prioritizing these deep features, the model acts like a semantic generator, filling in the mask based on tissue identity rather than just edges.


