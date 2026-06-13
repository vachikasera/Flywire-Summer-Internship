# Conserved Flow-Role Circuit in MAOL: T1-Dominated Optic-Lobe Class with Sparse Central Co-Members

## Choice of Dataset

I went with MAOL for the biological writeup, mainly because it's the smallest and most tractable of the three datasets, which made it easier to spot-check individual neurons against Codex without drowning in instance counts (T1 alone has nearly 1,800 instances in MCNS and 1,400 in FAFB, vs. 892 in MAOL). Sticking to the male optic lobe also kept the annotated sample within a single functional domain (vision), which made it easier to reason about what the shared "role" actually meant biologically.

**Composition of the sample (n=15):**

| Cell type | Count | Super class | Class |
|---|---|---|---|
| T1 | 4 | optic | optic_lobe_intrinsic (t1_neuron) |
| Unidentified (NOTPRIMARY) | 6 | central | various hemilineages (VLPl2, VLPl4, VLPl&p1, SLPav1) |
| AOTU050 | 1 | central | — |
| LHAV3e2 | 1 | central | SLPav1_medial |
| CL253 | 1 | central | VPNp&v1_posterior |
| PLP159 | 1 | central | VPNp&v1_posterior |
| PLP001 | 1 | central | putative_primary |

The dominant identified type is **T1**, an optic-lobe intrinsic interneuron with ~17–23 upstream partners and 0 downstream partners in this induced subgraph, which is consistently a "sink" within the matched circuit, despite being a major relay neuron globally (892 instances in MAOL alone, 1,400 in FAFB, 1,777 in MCNS).

## Structural Observation

Within this role class, nearly every sampled neuron shares the signature "few upstream connections, zero downstream connections" (most commonly 1-4 upstream, 0 downstream). The T1 instances are outliers in scale (17–23
upstream) but match the same zero-out-degree pattern. This means the flow
signature is grouping neurons primarily by **terminal/integrator position**
in their local subnetwork — consistent with the "second-order outflow"
metric being low or zero for nodes with no successors, which dominates the
signature regardless of absolute in-degree.

## Interpretation / Hypothesis

T1 neurons are well-characterized optic-lobe intrinsic interneurons of the
fly visual system, situated in the lamina/medulla and implicated in
contrast and motion-related signal conditioning upstream of the medulla's
direction-selective (T4/T5) pathways T1 neurons receive synaptic input primarily from photoreceptor terminals R1-R6 and lamina monopolar cells, supplying inhibitory input to motion-detecting neurons. Their predicted neurotransmitter
in this dataset is **glutamate** (Davis et al. 2020 reference), consistent
with reports of T1 as a primarily inhibitory/modulatory interneuron in the
lamina-medulla circuit.

**Hypothesis:** The recurrence of T1 as the dominant *conserved terminal node*
across MAOL/FAFB/BANC under this flow-signature alignment suggests that T1's
**topological role as a high-convergence, zero-further-output integrator**
is preserved across sexes and reconstructions — i.e., T1 consistently sits
at the same "depth" in the visual processing hierarchy (receiving from many
upstream partners, passing signal onward via pathways not captured within
this induced subgraph). The co-occurring central-brain neurons (AOTU050,
LHAV3e2, CL253, PLP159), several of which are themselves visual
projection-neuron targets (AOTU050 is part of the anterior optic tubercle
visual pathway), may represent **downstream recipients of the same visual
information stream**, grouped here not because they are wired identically to
T1, but because they occupy an analogous "few-in, zero-out-in-subgraph"
position in the central brain's processing hierarchy.

This supports the README's framing: the 647-neuron set is best understood as
a **conserved flow-role population** (a recurring "sink/integrator" motif
across the visual-to-central pipeline) rather than a single anatomically
contiguous circuit. A verified-isomorphism pass (see README, Future Work)
would be needed to determine which subset, if any, forms a literal shared
wiring diagram.

## Visualizations (to generate)

1. **Network graph**: `nx.draw` of the induced subgraph on the 15 sampled
   MAOL neurons (IDs: 38644, 14463, 25422, 31821, 21886, 26305, 32787, 41488,
   34866, 41342, 23112, 29229, 21340, 42335, 37450) plus their upstream
   partners, colored by `Cell Type` (T1 = blue, central types = orange,
   unidentified = gray).
2. **Codex 3D meshes**: load each root ID at
   `https://codex.flywire.ai/app/cell_details?dataset=optic-lobe&root_id=<ID>`
   — the T1 neurons (38644, 32787, 37450) will show the characteristic
   lamina-to-medulla columnar morphology; central-brain types will show
   projections into VLP/SLP/AOTU neuropils.

## Key Citations

- Davis, F.P. et al. (2020). *A genetic, genomic, and computational resource
  for exploring neural circuit function.* eLife — predicted-neurotransmitter
  reference used by Codex for T1 (glutamate, low confidence ~0.20).
- Takemura, S. et al. (2013/2017). *Visual motion detection circuits in
  Drosophila* — characterizes T1 as a lamina-medulla interneuron upstream of
  T4/T5 direction-selective pathways.
- Shinomiya, K. et al. (2019). *Comparisons between the medulla and lobula
  plate columns* — provides connectivity context for optic-lobe-intrinsic
  cell types including T1.
