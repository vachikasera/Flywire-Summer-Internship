# Flywire-Summer-Internship Connectome Challenge

## Problem Setup
The goal of this project was to identify the largest set of neurons that appears consistently across three connectome datasets, such that the induced directed subgraphs over those neurons are structurally equivalent and weakly connected.

A key difficulty is that neuron identifiers are not shared across datasets, meaning this is fundamentally a problem of structural alignment under unknown correspondence. There is no ground-truth mapping file; any correspondence must be inferred purely from graph topology.

## Datasets Used
- MAOL (male optic lobe)
- FAFB (female brain EM reconstruction)
- BANC (female central nervous system)

All three overlap in the optic lobe, which mattered once T1 turned out to be the dominant hub in the shared role class. T1 is an optic-lobe intrinsic neuron, so for the cross-dataset match to mean anything biologically, all three datasets needed to actually contain optic lobe tissue in the first place. MCNS, by contrast, spans the whole CNS and would have diluted the comparison.

## Trial-and-Error History

### Attempt 1 - Direct ID Correspondence (invalid assumption)

The first instinct was to check for overlapping neuron IDs across datasets, on the assumption that some shared numbering convention might exist. This failed immediately: IDs are assigned independently per reconstruction pipeline and carry no cross-dataset meaning. A spot-check on Codex confirmed that a single ID value could refer to three unrelated neurons across MAOL, FAFB, and BANC. This confirmed that the problem must be solved purely through graph structure-- node identity is uninformative.

### Attempt 2 - Strict Structural Matching (over-constrained)

The second approach enforced near-exact structural equivalence using local fingerprints: degree sequences, triangle counts, and higher-order neighborhood structure (2-hop neighbor degree profiles).

This was too strict:
- In the most rigorous version, the constraint search returned 0 valid circuits. No node correspondence satisfied exact induced-subgraph isomorphism once 2-hop context was required.
- In relaxed versions (degree-pair fingerprints only, retained in the repository under the "old" tab), the method produced 3-7 node subgraphs that were verifiably isomorphic but biologically trivial in size.

Root cause: MAOL is substantially denser than FAFB (roughly 5x higher average degree), so even genuinely homologous neurons have very different absolute degree values once embedded in their respective connectomes. Exact or near-exact isomorphism is far too restrictive a model for cross-individual, cross-reconstruction biological data. Even bona fide conserved circuits do not preserve exact adjacency at the single-neuron level across animals!

### Attempt 3 - Star-Based Structural Motifs

To get a guaranteed non-trivial result, the next approach searched for directed stars: a single hub neuron projecting to N leaf neurons, with no edges between leaves and no back-edges to the hub. Any two clean stars of the same size N are trivially isomorphic, so this structure is guaranteed identical across datasets by construction.

This produced larger, more stable results, but the structure is biologically artificial-- real neural circuits are essentially never pure stars. A star match demonstrates that some large isomorphic substructure exists, but it doesn't represent a recognizable circuit motif.

### Attempt 4 (Final) - Flow-Based Structural Role Alignment

At this point, I stepped back and reconsidered the problem from a more biological perspective. In my CS3 class, we were taught a useful principle when designing graph algorithms: when strict algorithmic constraints fail, it often helps to relax the model toward naturally occurring structures in the system being modeled.

Since connectomics is fundamentally about biological wiring, I began thinking about how real neural systems organize information. Neural circuits are not star-like. Instead, they resemble:
- flow networks (signal propagation)
- branching trees (divergent processing)
- locally recurrent pathways (feedback loops)

At this point the model was relaxed from symmetry (exact isomorphism, star shape) to flow-consistent structural roles. This framing was more in line with how connectomics literature describes neuron function: by position in a signal-flow hierarchy (input/integration/output layers) rather than by exact wiring diagrams.

#### Flow Signature
Each neuron `n` is assigned a 3-tuple:
- outgoing connectivity (signal propagation strength)
- incoming connectivity (signal integration)
- second-order propagation through neighbors
where `second_order_outflow(n)` sums the out-degrees of `n`'s first 10 out-neighbors. This captures not just how many downstream targets a neuron has, but how "amplifying" those targets are: a coarse proxy for a neuron's position in a feedforward processing hierarchy (e.g., early visual relay vs. late integration).

#### Pipeline
1. Load MAOL, FAFB, and BANC edge lists as directed graphs (`networkx.DiGraph`).
2. Compute the flow signature for every neuron in each dataset.
3. Group neurons within each dataset by identical signature value, producing per-dataset **signature classes**.
4. Intersect signature classes across all three datasets — a class is "shared" if at least one neuron in each dataset carries that exact `(out, in, out2)` triple. This yielded **90 shared structural roles**.
5. For each shared role, pool all neurons carrying that signature in each dataset into parallel lists.
6. Export the pooled, position-aligned lists to `network.csv` (3 columns: MAOL / FAFB / BANC; 647 rows, bounded by the smallest pooled list, MAOL).

## Assumptions

- Graphs are directed and unweighted (synapse-count weights discarded).
- Neuron identities (IDs) are not comparable across datasets.
- Small structural variation across datasets is expected and not penalized - exact adjacency is not required at the single-neuron level.
- Functional/flow-role equivalence is an acceptable relaxation of strict structural correspondence for the purposes of identifying a candidate conserved circuit.

## Important Caveat: Role-Equivalence vs. Verified Isomorphism
`network.csv` should be read as a candidate correspondence / role-equivalence map, not as a verified isomorphic circuit in the strict sense defined by the challenge.

Concretely:

- Two neurons are placed in the same row if they share a flow signature *within their own dataset* and occupy the same position in the pooled, signature-ordered list across datasets. This guarantees role similarity (similar local in/out/second-order flow), but it does not by itself guarantee that the induced directed subgraph over the 647 MAOL neurons is isomorphic to the induced subgraphs over the corresponding FAFB and BANC neurons.
- In other words: the rows are aligned by degree-flow class membership, not by an edge-by-edge correspondence check between the three induced subgraphs.

This is a deliberate relaxation, consistent with the trial-and-error progression above (Attempts 2–3 showed that strict isomorphism collapses to either 0 or single-digit node counts). However, it means `network.csv` represents a hypothesis about conserved circuit membership rather than a proof of structural identity.

### Future Work (Verification Step)
To close this gap, a follow-up pass should:

1. Take the 647-neuron candidate set per dataset.
2. Compute the induced directed subgraph for each dataset's candidate set.
3. Run a graph-isomorphism / maximum-common-subgraph check (like a `networkx.algorithms.isomorphism.DiGraphMatcher`, or a VF2-based search) between the three induced subgraphs.
4. Report the size of the largest verified-isomorphic subset, and use that as the final answer to the strict version of the challenge with the 647-neuron role-equivalence map serving as the candidate pool / search space for that verification, rather than the final answer itself.

## Reproducibility

```bash
pip install pandas networkx matplotlib
```

1. Download the three edge-list CSVs (MAOL, FAFB, BANC) and update the `files` dict paths at the top of the notebook/script.
2. Run cells in order:
   - **Load graphs**: builds `networkx.DiGraph` objects from each edge list.
   - **Compute signatures**: applies `signature(G, n)` to every node.
   - **Build signature maps**: groups nodes by signature within each dataset.
   - **Intersect shared signatures**: finds the 90 signature classes present in all three datasets.
   - **Pool and export**: builds `network.csv` (647 rows × 3 columns).
3. Visualization cells (optional) produce:
   - `shared_roles.png` - stacked bar chart of the 15 largest shared structural roles by total neuron count.
   - `flow_signature_space.png` - scatter plot of shared signatures in (out-degree, in-degree) space, sized by second-order outflow.
   - `flow_hierarchy.png` - bar chart bucketing shared roles into Local / Intermediate / Global flow tiers by second-order outflow magnitude (`<100`, `100–1000`, `>=1000`).
   - `role_network.png` - graph of shared signature classes connected when their (out-degree, in-degree) values are within Manhattan distance 10, illustrating clustering of related structural roles.

## Final Reflection

The central lesson of this project was that **the failure of strict graph matching is a modeling problem, not just an algorithmic one**. Connectomes from different individuals (and different sexes/sequencing efforts, in this case) are not expected to be exactly isomorphic at the level of individual synaptic adjacency, as biological variability, reconstruction error, and individual differences all break exact structural correspondence.

Relaxing the correspondence criterion from exact isomorphism to flow-role equivalence allowed a much larger (647-neuron) candidate set to emerge, consistent with the idea (common in computational neuroscience) that conserved circuits are better characterized by their functional position in an information-flow hierarchy than by exact wiring-diagram identity. The tradeoff, made explicit above, is that this candidate set requires a downstream verification step before it can be called a strictly isomorphic circuit.
