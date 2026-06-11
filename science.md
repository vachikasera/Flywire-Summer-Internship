Biological significance of the shared circuit

The largest shared structure across MAOL, FAFB, and BANC is a flow-consistent induced circuit obtained via structural role alignment in directed connectomes. Instead of requiring exact subgraph isomorphism (which collapses under cross-dataset variability), neurons were grouped by a flow signature capturing first- and second-order directed connectivity statistics. The intersection of these structural roles across datasets yields a conserved weakly connected circuit spanning all three connectomes. This aligns with prior work (Tschopp et al. (2024)) showing that Drosophila connectomes exhibit strong modular flow structure and hierarchical organization rather than globally rigid motifs, with information propagating through distributed subnetworks rather than isolated units.

Structural interpretation

The recovered circuit is best interpreted as a distributed propagation backbone rather than a discrete motif (e.g., clique or star).

Each participating neuron class exhibits:
- high outward reach (signal broadcasting / divergence)
- non-trivial convergence (integration of upstream inputs)
- persistence of second-order reachability (multi-hop propagation stability)

These properties are consistent with known organization of fly sensory systems, where computation is distributed across layered feedforward and recurrent pathways rather than localized single-node hubs. Recent connectome-constrained modeling work similarly demonstrates that structural connectivity alone can strongly constrain functional dynamics in Drosophila visual circuits (Grindrod et al. (2024)).

Functional relevance in visual and central pathways

Although neuron identity is not preserved across datasets, many matched flow-role classes align with known properties of optic-lobe and early sensory processing circuits, including:
- columnar-to-wide-field signal integration
- lateral inhibition shaping directional selectivity
- feedforward divergence in motion processing streams

Experimental studies of the optic lobe show that motion processing relies on parallel subnetworks with structured inhibitory and excitatory interactions, rather than single-path routing (Pokusaeva 2023). This supports the interpretation that the recovered circuit corresponds to a conserved computation layer for signal routing and normalization, rather than a single anatomically fixed microcircuit.

Cross-dataset invariance

A key observation is that these structural roles persist across:
- MAOL (dense optic-lobe focused reconstruction)
- FAFB (whole-brain female connectome)
- BANC (central nervous system reconstruction)

Despite differences in scale, reconstruction method, and segmentation pipelines, the same flow-role classes remain detectable. This is consistent with broader findings that Drosophila connectomes exhibit strong modularity and stable functional clustering across datasets, even when synaptic weights vary substantially (Lappalainen et al. (2024)).

Biological interpretation

The recovered structure is best understood as a conserved flow motif class, rather than a specific circuit.

Instead of encoding a single fixed wiring diagram, it likely reflects a repeated organizational principle: neural computation in Drosophila is implemented through distributed, directionally biased flow networks with conserved propagation roles.

This interpretation is supported by both experimental and modeling literature showing that:
- early visual processing can be predicted from connectivity structure alone
- connectomes decompose into semi-independent functional modules with directional information flow
- circuits are more reliably described by cell-type interactions than individual neuron identity

Limitations

While the circuit is structurally consistent across datasets, it should not be interpreted as a uniquely identified biological “unit”:
- correspondence is defined in role space, not neuron identity space
- increasing signature specificity reduces recoverable circuit size rapidly
- decreasing constraints increases size but reduces biological specificity

Thus, the result represents a tradeoff between biological fidelity and structural recoverability.

Conclusion

The main result is that cross-connectome alignment is more stable when framed as a flow-role matching problem rather than a strict graph isomorphism problem. This suggests that conserved biological signal processing is better captured by invariants of information flow structure, rather than exact synaptic wiring patterns. The recovered circuit therefore represents a robust computational scaffold in the Drosophila brain, consistent with modern views of the connectome as a modular, flow-organized system rather than a rigid wiring diagram.

Key references

FlyWire Codex connectome datasets (MAOL v1.1, FAFB v783, BANC v626)
https://codex.flywire.ai

Lappalainen et al. (2024). Connectome-constrained networks predict neural activity in Drosophila visual processing. Nature.

Tschopp et al. (2018). Connectome-based models of the Drosophila visual system. arXiv.

Grindrod et al. (2024). Modularity, hierarchical flows and symmetry of the Drosophila connectome. arXiv.

Pokusaeva (ISTA thesis). Optic flow-based navigation circuits in Drosophila.
