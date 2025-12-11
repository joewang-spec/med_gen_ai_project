<img width="2270" height="404" alt="image" src="https://github.com/user-attachments/assets/712c79b2-f6a4-470e-aa3f-ee8e27d56214" />
The Illustrated U-Net: Deconstructing Biomedical Segmentation
A visual investigation into the "Black Box" of Medical AI. This project deconstructs the famous U-Net architecture to understand exactly how it localizes biological structures. By systematically severing the network's skip connections, we isolate the specific contributions of "Deep" (Semantic) vs. "Shallow" (Spatial) features.

The Visual Deconstruction
The core of this project is not just achieving a high score, but visualizing why the model succeeds or fails. We analyzed the breakdown of the architecture using the Kvasir-SEG (Polyp) dataset.

1. The "Tunnel Vision" Failure (No Skips)
Observation: The model produces small, central blobs.

Explanation: Without skip connections, the encoder compresses the image into a tiny bottleneck. Spatial resolution is lost. The model knows roughly where the polyp is (center), but has lost the fine-grained coordinate data to define the edges.

2. The "Texture Blind" Failure (Shallow Skips Only)
Observation: Sharp edges, but misses the bulk of the mass.

Explanation: The shallow layers act as edge detectors. They see high-frequency changes (lines), but they lack the Semantic Receptive Field to understand that the "lumpy texture" at the top is also part of the polyp. It sees the boundary, but misses the object.

3. The "Latent Identity" Success (Deep Skips Only)
Observation: Robust coverage of the entire polyp mass.

Explanation: This variant outperformed even the Full U-Net. Polyp detection is a Texture Task. The deep layers encode the "concept" of abnormal tissue. By prioritizing these deep features, the model acts like a semantic generator, filling in the mask based on tissue identity rather than just edges.
