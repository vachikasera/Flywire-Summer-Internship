## Choice of dataset

I went with MAOL for the writeup. It's the smallest of the three datasets, which made it easiest to spot-check individual neurons against Codex (T1 alone has ~1,800 instances in MCNS and 1,400 in FAFB vs. 892 in MAOL). Sticking to the optic lobe also kept the sample within one functional domain, making it easier to reason about what the shared "role" meant.

## The conclusion

Out of the 647-neuron candidate set, I annotated a sample of 15 MAOL neurons (table below). The result was less "one circuit" and more "one role showing up repeatedly"-- nearly every sampled neuron shares the same local signature: a handful of upstream partners and zero downstream partners within the induced subgraph. The class is dominated by T1, a classic optic-lobe intrinsic interneuron, with a scatter of central-brain neurons (AOTU050, LHAV3e2, CL253, PLP159, PLP001) sitting at an analogous "sink" position one or two steps further into the brain.

| Cell type | Count | Super class | Class / Hemilineage |
|---|---|---|---|
| T1 | 4 | optic | optic_lobe_intrinsic (t1_neuron) |
| Unidentified (NOTPRIMARY) | 6 | central | VLPl2, VLPl4, VLPl&p1, SLPav1 |
| AOTU050 | 1 | central | — |
| LHAV3e2 | 1 | central | SLPav1_medial |
| CL253 | 1 | central | VPNp&v1_posterior |
| PLP159 | 1 | central | VPNp&v1_posterior |
| PLP001 | 1 | central | putative_primary |

## The T1 majority

4/15 sampled neurons are T1, flagged `optic_lobe_intrinsic / t1_neuron` with a predicted neurotransmitter of glutamate. T1 cells sit in the lamina-medulla circuitry, conditioning photoreceptor signal before it reaches the medulla's motion-processing columns. Each T1 instance here has 17-23 upstream connections and 0 downstream-- a convergence point collecting from many partners whose onward output isn't captured in this subgraph.

What's interesting: the T1 neurons have an order of magnitude more upstream partners (17-23) than the unidentified central neurons (1-4), yet land in the same role class. Once a neuron has zero out-degree in-subgraph, the second-order-outflow term collapses toward zero for all of them, so they get grouped together despite very different absolute connectivity.

## The central-brain stragglers

The other 11 are mostly unidentified (`Prelim Roughly traced`) but carry hemilineage tags pointing to VLP, SLP, and AOTU-- regions downstream of the optic lobe in visual processing. A few have real cell-type calls: AOTU050 (anterior optic tubercle pathway), LHAV3e2 (lateral horn), and CL253 / PLP159 (both `VPNp&v1_posterior`), each with 1–3 upstream / 0 downstream. My read is that these are likely downstream recipients of the same visual stream T1 is conditioning, which are not directly wired to the T1 instances here, but occupying the same "few-in, zero-out" niche one or two synapses further along.

## Visualizations

Structural Role Abundance and Shared Flow-Signature Space show where T1's signature (high in-degree, zero out-degree, low second-order outflow) sits relative to the other 89 shared role classes-- it's a clear outlier in scale but not in shape. Flow Hierarchy of Conserved Neuron Classes buckets roles into local/intermediate/global tiers; T1 falls solidly in the "local
sink" tier alongside the central-brain stragglers, supporting the shared-role interpretation above. Relationship Network of Shared Structural Roles and Shared Circuit (Aligned Cross-Dataset Matching) show how this role class connects to neighboring roles in (out-degree, in-degree) space. T1's cluster sits adjacent to several of the central VLP/SLP roles, which is
consistent with them occupying a similar topological position one step apart.

<img width="1377" height="662" alt="Structural Role Abundance" src="https://github.com/user-attachments/assets/87f5d378-97f6-46a6-8755-9bb0d824f5de" />
<img width="867" height="723" alt="Shared Flow-Signature Space" src="https://github.com/user-attachments/assets/0f9e0f82-452a-4336-8592-347c9b75e78f" />
<img width="666" height="552" alt="Flow Hierarchy of Conserved Neuron Classes" src="https://github.com/user-attachments/assets/d03d2aef-fb11-43d8-a74c-c01f5003ad07" />

## What I'd check next

This role class was built from local flow signatures, not a verified induced-subgraph isomorphism. The T1 dominance is a real, interesting signal, which suggests this corner of the candidate set captures a genuine "optic lobe sink" motif preserved across MAOL/FAFB/BANC, but confirming the 647 neurons form an isomorphic wiring diagram (not just a shared role) needs the VF2-based verification pass described in the README's Future Work section.

## Citations

- Davis, F.P. et al. (2020). *A genetic, genomic, and computational resource for exploring neural circuit function.* eLife. - source of the glutamate
  prediction for T1 in Codex.
- Takemura, S. et al. (2013). *A visual motion detection circuit suggested by Drosophila connectomics.* Nature. - characterizes T1 as a lamina output neuron feeding medulla motion-processing circuitry.
