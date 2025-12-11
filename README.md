<img width="875" height="598" alt="image" src="https://github.com/user-attachments/assets/08c97e97-b0f4-4a94-8038-c744e7145640" />
no skip
<img width="909" height="596" alt="image" src="https://github.com/user-attachments/assets/e0947788-4dc8-4d63-be09-30e9cc6402bc" />
deep only
<img width="897" height="603" alt="image" src="https://github.com/user-attachments/assets/e87b4604-6c14-4c08-902b-2b05b007cc60" />
shallow only
<img width="839" height="594" alt="image" src="https://github.com/user-attachments/assets/225ecb7f-f6b1-4df2-b6eb-3569cf9eb053" />
complete unet, all skips


<img width="2270" height="404" alt="image" src="https://github.com/user-attachments/assets/712c79b2-f6a4-470e-aa3f-ee8e27d56214" />
The Illustrated U-Net: Deconstructing Biomedical Segmentation
A visual investigation into the "Black Box" of Medical AI. This project deconstructs the famous U-Net architecture to understand exactly how it localizes biological structures. By systematically severing the network's skip connections, we isolate the specific contributions of "Deep" (Semantic) vs. "Shallow" (Spatial) features.
<img width="743" height="147" alt="image" src="https://github.com/user-attachments/assets/9802ca48-2dfc-4b31-b482-c26e314db6ea" />

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




dataset2
+----------------------+--------+--------+-----------+--------+
| Model Variant        |  Dice  |  IoU   | Precision | Recall |
+----------------------+--------+--------+-----------+--------+
| No Skips (Baseline)  | 0.7144 | 0.5557 |  0.6420   | 0.8051 |
| Deep Skips Only      | 0.8194 | 0.6941 |  0.7855   | 0.8562 |
| Shallow Skips Only   | 0.8317 | 0.7119 |  0.8291   | 0.8344 |
| Full U-Net           | 0.8339 | 0.7151 |  0.8350   | 0.8328 |
+----------------------+--------+--------+-----------+--------+
<img width="1457" height="396" alt="image" src="https://github.com/user-attachments/assets/6e6c4dd4-c7aa-4498-adbe-076e05fac90a" />

<img width="2275" height="390" alt="Screenshot 2025-12-05 030804" src="https://github.com/user-attachments/assets/ff6dced9-9c4a-49f2-8077-2346e461a9d5" />
As hypothesized in our proposal, the No-Skips Baseline suffered a significant performance collapse (Dice: 0.7144), confirming the "Blurry Boundary" problem: without a direct path from the encoder, the bottleneck irreversibly destroys the fine-grained spatial information needed to define object edges. Conversely, the Full U-Net achieved the highest performance (Dice: 0.8339), validating the effectiveness of fusing multi-scale features to solve the "What vs. Where" problem. The Dominance of Shallow Features The most significant and surprising finding of this study is the performance of the Shallow Skips Only variant. With a Dice score of 0.8317, it performed within 0.3% of the full model, significantly outperforming the Deep Skips Only variant (0.8194).
This result offers a critical insight into the mechanics of the U-Net for biomedical tasks:

Spatial Precision > Semantic Depth: Nuclei segmentation is a semantically simple task (binary classification) but spatially complex (separating touching instances). While the deep layers capture abstract semantic context, they are heavily downsampled.
The "Edge Detector" Function: The "Shallow" connections bypass the bottleneck entirely, transferring high-resolution edge information directly to the decoder. Our results suggest that for tasks requiring high boundary precision, the recovery of these high-frequency spatial features is the primary driver of model performance, rather than the deep semantic context alone.


