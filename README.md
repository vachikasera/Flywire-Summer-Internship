# Flywire-Summer-Internship

Largest shared induced circuit across three Drosophila connectomes
Technical report

Problem setup:
The goal of this project was to identify the largest set of neurons that appears consistently across three connectome datasets (MAOL, FAFB, and BANC), such that the induced directed subgraphs over those neurons are structurally equivalent and weakly connected.
*A key difficulty is that neuron identifiers are not shared across datasets, meaning the problem is fundamentally one of structural alignment under unknown correspondence, not direct node matching.

Datasets used:
- MAOL (male optic lobe)
- FAFB (female brain EM reconstruction)
- BANC (female central nervous system)

Early attempts and failure modes:
1. Direct correspondence (invalid assumption)
I initially tried simple matching based on neuron IDs, assuming there might be overlap or partial alignment between datasets. This quickly failed because IDs are completely independent across reconstructions. This made it clear that the problem had to be solved purely through graph structure.

2. Strict structural matching (over-constrained)
Next, I tried enforcing near-exact structural equivalence using local fingerprints (degree, triangle counts, and higher-order neighborhood structure).

This approach produced very small or trivial matches:
- In some cases, the method returned no valid circuits at all (0 nodes) due to overly strict constraints.
- In more relaxed versions (old under Github links), it produced only very small subgraphs (3–7 nodes) that were structurally consistent but far too limited to represent meaningful circuits.

The core issue was that requiring exact or near-exact isomorphism across noisy biological datasets is too restrictive. Even biologically conserved circuits do not preserve exact adjacency at the neuron level across reconstructions.

Immediate idea:
Star-based structure
After these failures, I shifted to a more relaxed structural motif: the directed star.
A star is defined as a single hub neuron projecting to many independent leaf neurons. Any two clean stars of the same size are isomorphic, which makes comparison across datasets straightforward. This approach produced much larger and more stable results, but it was also somewhat artificial from a biological perspective because it enforces a highly centralized structure that does not fully reflect how real neural circuits behave.

Key insight:
Shift toward natural structure
At this point, I stepped back and reconsidered the problem from a more biological perspective. In my CS3 class, we were taught a useful principle when designing graph algorithms: when strict algorithmic constraints fail, it often helps to relax the model toward naturally occurring structures in the system being modeled.

Since connectomics is fundamentally about biological wiring, I began thinking about how real neural systems organize information.

Neural circuits are not star-like. Instead, they resemble:
- flow networks (signal propagation)
- branching trees (divergent processing)
- locally recurrent pathways (feedback loops)
This suggested that the correct abstraction is not symmetry, but flow-consistent structure.

The Final Approach: flow-based structural alignment
Instead of matching neurons directly or enforcing strict isomorphism, I modeled each connectome as a directed flow system.

Each neuron was assigned a flow signature, capturing:
- outgoing connectivity (signal propagation strength)
- incoming connectivity (signal integration)
- second-order propagation through neighbors
This creates a structural representation of each neuron’s role in information flow, rather than its exact wiring pattern.

Circuit construction
The final pipeline works as follows:
- Compute flow signatures for all neurons in MAOL, FAFB, and BANC.
- Group neurons within each dataset by identical structural signatures.
- Identify signature classes that appear in all three datasets.
- Treat these shared classes as conserved “structural roles.”
- Merge all neurons belonging to shared roles into a unified circuit representation.
- Extract the largest weakly connected component from this merged structure.
This produces a circuit based on role equivalence rather than exact structural identity, which is more robust to biological variability.

Compared to earlier attempts:
- Unlike strict isomorphism methods, it does not collapse to trivial solutions (3–7 nodes).
- Unlike star-based models, it does not impose unrealistic centralization.
- Unlike local fingerprinting, it preserves enough structural context to support circuit-level assembly.

Most importantly, it aligns better with the biological intuition that neural circuits are defined by function and flow, not exact wiring replication across individuals.

Assumptions:
- Graphs are directed and unweighted
- Neuron identities are not comparable across datasets
- Small structural variation across datasets is expected and not penalized
- Functional role equivalence is sufficient for defining correspondence

Output
The final output is stored in: network.csv
with:
Column 1: MAOL neuron IDs
Column 2: FAFB neuron IDs
Column 3: BANC neuron IDs

Each row corresponds to neurons grouped by shared structural flow role across datasets.

Reproducibility
- Load MAOL, FAFB, and BANC edge lists
- Construct directed graphs
- Compute flow signatures for each node
- Group nodes by structural role within each dataset
- Intersect shared role classes across datasets
- Export aligned neuron mappings to network.csv

Final reflection
The most important realization in this project was that the failure of strict graph matching methods is a modeling issue. Biological neural systems are not perfectly identical across datasets, so methods that require exact structural equivalence naturally break down. Relaxing the problem to focus on flow-consistent structural roles allowed much larger and more biologically meaningful circuits to emerge.
