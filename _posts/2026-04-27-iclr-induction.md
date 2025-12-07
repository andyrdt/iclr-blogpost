---
layout: distill
title: In-context learning of representations can be explained by induction circuits
description: "Park et al., 2025 demonstrate that large language models can learn to trace random walks on graphs presented in context, and observe that token representations reorganize to reflect the underlying graph structure. This has been interpreted as evidence that models 'flexibly manipulate their representations' to reflect in-context semantics, and that this reorganization enables task performance. We offer a simpler mechanistic explanation. We first observe that task performance can be fully explained by induction circuits (Olsson et al., 2022), and show that ablating the attention heads that comprise these circuits substantially degrades performance. As for the geometric structure, we propose that it could result from previous token heads effectively mixing the representations of graph neighbors together. We show that a single round of such 'neighbor mixing' on random embeddings recreates the observed graph correspondence in PCA visualizations. These results suggest that apparent 'representation reorganization' may be a byproduct of the model's induction circuits, rather than a critical strategy useful for in-context learning."
date: 2026-04-27
future: true
htmlwidgets: true
hidden: true

# Mermaid diagrams
mermaid:
  enabled: true
  zoomable: true

# Anonymize when submitting
authors:
  - name: Anonymous

# authors:
#   - name: Albert Einstein
#     url: "https://en.wikipedia.org/wiki/Albert_Einstein"
#     affiliations:
#       name: IAS, Princeton
#   - name: Boris Podolsky
#     url: "https://en.wikipedia.org/wiki/Boris_Podolsky"
#     affiliations:
#       name: IAS, Princeton
#   - name: Nathan Rosen
#     url: "https://en.wikipedia.org/wiki/Nathan_Rosen"
#     affiliations:
#       name: IAS, Princeton

# must be the exact same name as your blogpost
bibliography: 2026-04-27-iclr-induction.bib

# Add a table of contents to your post.
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - please use this format rather than manually creating a markdown table of contents.
toc:
  - name: Recapitulation and reproduction of Park et al., 2025
    subsections:
      - name: The grid tracing task
      - name: Replication and interpretation
  - name: "A simpler explanation: induction circuits"
    subsections:
      - name: Testing the induction hypothesis
      - name: Results
  - name: Previous-token mixing can account for representational structure
    subsections:
      - name: The neighbor-mixing hypothesis
      - name: A toy model of previous-token mixing
      - name: Evidence of neighbor mixing in individual model activations
  - name: Limitations and open questions
  - name: Conclusion

# Below is an example of injecting additional post-specific styles.
# This is used in the 'Layouts' section of this post.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

  /* ============================================================
     RESPONSIVE PLOT SYSTEM
     - Interactive HTML on desktop (>768px)
     - Static PNG fallback on mobile (<=768px)
     ============================================================ */

  /* Container for responsive plots with PNG fallback */
  .plot-container {
    position: relative;
    width: 100%;
    margin-bottom: 1rem;
  }

  /* Standard height plot (450px) */
  .plot-container.plot-standard {
    height: 470px;  /* Slightly more than plot height for padding */
  }

  /* Tall height plot (550px) - for PCA visualizations */
  .plot-container.plot-tall {
    height: 570px;
  }

  /* Grid/multi-panel height (800px) */
  .plot-container.plot-grid {
    height: 820px;
  }

  /* Square plot (550px x 550px) - for PCA visualizations */
  .plot-container.plot-square {
    height: 570px;
    max-width: 570px;
    margin-left: auto;
    margin-right: auto;
  }

  /* Medium width plot (650px) - for ablation curves */
  .plot-container.plot-medium {
    height: 470px;
    max-width: 670px;
    margin-left: auto;
    margin-right: auto;
  }

  /* Hide iframes - always use PNG */
  .plot-container iframe {
    display: none;
  }

  /* PNG image - always shown */
  .plot-container .plot-fallback {
    display: block;
    width: 100%;
    height: auto;
    max-width: 100%;
  }

  /* Reset fixed heights since PNG is auto-sized */
  .plot-container.plot-standard,
  .plot-container.plot-tall,
  .plot-container.plot-grid,
  .plot-container.plot-square,
  .plot-container.plot-medium {
    height: auto;
  }

  /* Container for two plots side-by-side */
  .plot-row {
    display: flex;
    flex-wrap: wrap;
    gap: 16px;
    margin-bottom: 0.5rem;
  }

  /* Each side-by-side plot container */
  .plot-row .plot-container {
    flex: 1 1 400px;
    min-width: 300px;
  }

  /* Custom width ratios for plot rows */
  .plot-row .plot-container.w-65 { flex: 65 1 300px; }
  .plot-row .plot-container.w-60 { flex: 60 1 300px; }
  .plot-row .plot-container.w-55 { flex: 55 1 300px; }
  .plot-row .plot-container.w-50 { flex: 50 1 300px; }
  .plot-row .plot-container.w-45 { flex: 45 1 300px; }
  .plot-row .plot-container.w-40 { flex: 40 1 300px; }
  .plot-row .plot-container.w-35 { flex: 35 1 300px; }

  /* Triple plot row - three plots side by side */
  .plot-row-triple {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
    margin-bottom: 0.5rem;
  }

  .plot-row-triple .plot-container {
    flex: 1 1 280px;
    min-width: 250px;
  }

  .plot-row-triple .plot-container .plot-fallback {
    width: 100%;
    height: auto;
  }

  /* Legacy iframe classes (for backwards compatibility) */
  .plot-frame-half {
    flex: 1 1 300px;
    height: 470px;
    border: 0;
    display: block;
  }

  .plot-frame-full {
    display: block;
    margin: 0 auto 1rem auto;
    width: 100%;
    max-width: 900px;
    height: 470px;
    border: 0;
  }

  @media (max-width: 768px) {
    .plot-frame-half,
    .plot-frame-full {
      display: none;
    }
  }

  /* ============================================================
     SCROLLABLE IMAGE GALLERY (for attention patterns appendix)
     - Always show PNG selector, hide interactive iframe
     ============================================================ */

  /* Container for interactive viewer with PNG fallback gallery */
  .attention-viewer {
    position: relative;
    width: 100%;
    margin-bottom: 1rem;
  }

  /* Hide interactive iframe - always use PNG */
  .attention-viewer .viewer-interactive {
    display: none;
  }

  /* The scrollable PNG gallery (unused now, but kept for reference) */
  .attention-viewer .viewer-gallery {
    display: none;
    width: 100%;
    max-height: 500px;
    overflow-y: auto;
    border: 1px solid #e0e0e0;
    border-radius: 8px;
    padding: 10px;
    background: #fafafa;
  }

  .attention-viewer .viewer-gallery .gallery-item {
    margin-bottom: 20px;
    text-align: center;
  }

  .attention-viewer .viewer-gallery .gallery-item img {
    max-width: 100%;
    height: auto;
    border: 1px solid #ddd;
    border-radius: 4px;
  }

  .attention-viewer .viewer-gallery .gallery-item .gallery-label {
    font-size: 14px;
    font-weight: 600;
    margin-bottom: 8px;
    color: #333;
  }

  /* ============================================================
     PNG SELECTOR - Pure CSS with radio buttons
     ============================================================ */

  .png-selector {
    display: block;
    width: 100%;
    margin-bottom: 1rem;
  }

  /* Hide radio buttons */
  .png-selector input[type="radio"] {
    display: none;
  }

  /* Label buttons in a row */
  .png-selector .selector-buttons {
    display: flex;
    flex-wrap: wrap;
    gap: 4px;
    margin-bottom: 15px;
  }

  .png-selector label {
    padding: 4px 8px;
    font-size: 12px;
    border: 1px solid #ccc;
    background: #f5f5f5;
    cursor: pointer;
    color: #333;
  }

  .png-selector label:hover {
    background: #e0e0e0;
  }

  /* All images hidden by default */
  .png-selector .selector-image {
    display: none;
    text-align: center;
  }

  .png-selector .selector-image img {
    max-width: 400px;
    width: 100%;
    height: auto;
    border: 1px solid #ddd;
  }

  /* Dark mode support */
  @media (prefers-color-scheme: dark) {
    .png-selector label {
      background: #333;
      border-color: #555;
      color: #ddd;
    }
    .png-selector label:hover {
      background: #444;
    }
    .png-selector .selector-image img {
      border-color: #555;
    }
  }

  /* Show image when corresponding radio is checked */
  .png-selector #ind1:checked ~ .selector-images .img-ind1,
  .png-selector #ind2:checked ~ .selector-images .img-ind2,
  .png-selector #ind3:checked ~ .selector-images .img-ind3,
  .png-selector #ind4:checked ~ .selector-images .img-ind4,
  .png-selector #ind5:checked ~ .selector-images .img-ind5,
  .png-selector #ind6:checked ~ .selector-images .img-ind6,
  .png-selector #ind7:checked ~ .selector-images .img-ind7,
  .png-selector #ind8:checked ~ .selector-images .img-ind8,
  .png-selector #ind9:checked ~ .selector-images .img-ind9,
  .png-selector #ind10:checked ~ .selector-images .img-ind10,
  .png-selector #ind11:checked ~ .selector-images .img-ind11,
  .png-selector #ind12:checked ~ .selector-images .img-ind12,
  .png-selector #ind13:checked ~ .selector-images .img-ind13,
  .png-selector #ind14:checked ~ .selector-images .img-ind14,
  .png-selector #ind15:checked ~ .selector-images .img-ind15,
  .png-selector #ind16:checked ~ .selector-images .img-ind16,
  .png-selector #ind17:checked ~ .selector-images .img-ind17,
  .png-selector #ind18:checked ~ .selector-images .img-ind18,
  .png-selector #ind19:checked ~ .selector-images .img-ind19,
  .png-selector #ind20:checked ~ .selector-images .img-ind20,
  .png-selector #ind21:checked ~ .selector-images .img-ind21,
  .png-selector #ind22:checked ~ .selector-images .img-ind22,
  .png-selector #ind23:checked ~ .selector-images .img-ind23,
  .png-selector #ind24:checked ~ .selector-images .img-ind24,
  .png-selector #ind25:checked ~ .selector-images .img-ind25,
  .png-selector #ind26:checked ~ .selector-images .img-ind26,
  .png-selector #ind27:checked ~ .selector-images .img-ind27,
  .png-selector #ind28:checked ~ .selector-images .img-ind28,
  .png-selector #ind29:checked ~ .selector-images .img-ind29,
  .png-selector #ind30:checked ~ .selector-images .img-ind30,
  .png-selector #ind31:checked ~ .selector-images .img-ind31,
  .png-selector #ind32:checked ~ .selector-images .img-ind32,
  .png-selector #prev1:checked ~ .selector-images .img-prev1,
  .png-selector #prev2:checked ~ .selector-images .img-prev2,
  .png-selector #prev3:checked ~ .selector-images .img-prev3,
  .png-selector #prev4:checked ~ .selector-images .img-prev4,
  .png-selector #prev5:checked ~ .selector-images .img-prev5,
  .png-selector #prev6:checked ~ .selector-images .img-prev6,
  .png-selector #prev7:checked ~ .selector-images .img-prev7,
  .png-selector #prev8:checked ~ .selector-images .img-prev8,
  .png-selector #prev9:checked ~ .selector-images .img-prev9,
  .png-selector #prev10:checked ~ .selector-images .img-prev10,
  .png-selector #prev11:checked ~ .selector-images .img-prev11,
  .png-selector #prev12:checked ~ .selector-images .img-prev12,
  .png-selector #prev13:checked ~ .selector-images .img-prev13,
  .png-selector #prev14:checked ~ .selector-images .img-prev14,
  .png-selector #prev15:checked ~ .selector-images .img-prev15,
  .png-selector #prev16:checked ~ .selector-images .img-prev16,
  .png-selector #prev17:checked ~ .selector-images .img-prev17,
  .png-selector #prev18:checked ~ .selector-images .img-prev18,
  .png-selector #prev19:checked ~ .selector-images .img-prev19,
  .png-selector #prev20:checked ~ .selector-images .img-prev20,
  .png-selector #prev21:checked ~ .selector-images .img-prev21,
  .png-selector #prev22:checked ~ .selector-images .img-prev22,
  .png-selector #prev23:checked ~ .selector-images .img-prev23,
  .png-selector #prev24:checked ~ .selector-images .img-prev24,
  .png-selector #prev25:checked ~ .selector-images .img-prev25,
  .png-selector #prev26:checked ~ .selector-images .img-prev26,
  .png-selector #prev27:checked ~ .selector-images .img-prev27,
  .png-selector #prev28:checked ~ .selector-images .img-prev28,
  .png-selector #prev29:checked ~ .selector-images .img-prev29,
  .png-selector #prev30:checked ~ .selector-images .img-prev30,
  .png-selector #prev31:checked ~ .selector-images .img-prev31,
  .png-selector #prev32:checked ~ .selector-images .img-prev32 {
    display: block;
  }

  /* Highlight selected label */
  .png-selector #ind1:checked ~ .selector-buttons label[for="ind1"],
  .png-selector #ind2:checked ~ .selector-buttons label[for="ind2"],
  .png-selector #ind3:checked ~ .selector-buttons label[for="ind3"],
  .png-selector #ind4:checked ~ .selector-buttons label[for="ind4"],
  .png-selector #ind5:checked ~ .selector-buttons label[for="ind5"],
  .png-selector #ind6:checked ~ .selector-buttons label[for="ind6"],
  .png-selector #ind7:checked ~ .selector-buttons label[for="ind7"],
  .png-selector #ind8:checked ~ .selector-buttons label[for="ind8"],
  .png-selector #ind9:checked ~ .selector-buttons label[for="ind9"],
  .png-selector #ind10:checked ~ .selector-buttons label[for="ind10"],
  .png-selector #ind11:checked ~ .selector-buttons label[for="ind11"],
  .png-selector #ind12:checked ~ .selector-buttons label[for="ind12"],
  .png-selector #ind13:checked ~ .selector-buttons label[for="ind13"],
  .png-selector #ind14:checked ~ .selector-buttons label[for="ind14"],
  .png-selector #ind15:checked ~ .selector-buttons label[for="ind15"],
  .png-selector #ind16:checked ~ .selector-buttons label[for="ind16"],
  .png-selector #ind17:checked ~ .selector-buttons label[for="ind17"],
  .png-selector #ind18:checked ~ .selector-buttons label[for="ind18"],
  .png-selector #ind19:checked ~ .selector-buttons label[for="ind19"],
  .png-selector #ind20:checked ~ .selector-buttons label[for="ind20"],
  .png-selector #ind21:checked ~ .selector-buttons label[for="ind21"],
  .png-selector #ind22:checked ~ .selector-buttons label[for="ind22"],
  .png-selector #ind23:checked ~ .selector-buttons label[for="ind23"],
  .png-selector #ind24:checked ~ .selector-buttons label[for="ind24"],
  .png-selector #ind25:checked ~ .selector-buttons label[for="ind25"],
  .png-selector #ind26:checked ~ .selector-buttons label[for="ind26"],
  .png-selector #ind27:checked ~ .selector-buttons label[for="ind27"],
  .png-selector #ind28:checked ~ .selector-buttons label[for="ind28"],
  .png-selector #ind29:checked ~ .selector-buttons label[for="ind29"],
  .png-selector #ind30:checked ~ .selector-buttons label[for="ind30"],
  .png-selector #ind31:checked ~ .selector-buttons label[for="ind31"],
  .png-selector #ind32:checked ~ .selector-buttons label[for="ind32"],
  .png-selector #prev1:checked ~ .selector-buttons label[for="prev1"],
  .png-selector #prev2:checked ~ .selector-buttons label[for="prev2"],
  .png-selector #prev3:checked ~ .selector-buttons label[for="prev3"],
  .png-selector #prev4:checked ~ .selector-buttons label[for="prev4"],
  .png-selector #prev5:checked ~ .selector-buttons label[for="prev5"],
  .png-selector #prev6:checked ~ .selector-buttons label[for="prev6"],
  .png-selector #prev7:checked ~ .selector-buttons label[for="prev7"],
  .png-selector #prev8:checked ~ .selector-buttons label[for="prev8"],
  .png-selector #prev9:checked ~ .selector-buttons label[for="prev9"],
  .png-selector #prev10:checked ~ .selector-buttons label[for="prev10"],
  .png-selector #prev11:checked ~ .selector-buttons label[for="prev11"],
  .png-selector #prev12:checked ~ .selector-buttons label[for="prev12"],
  .png-selector #prev13:checked ~ .selector-buttons label[for="prev13"],
  .png-selector #prev14:checked ~ .selector-buttons label[for="prev14"],
  .png-selector #prev15:checked ~ .selector-buttons label[for="prev15"],
  .png-selector #prev16:checked ~ .selector-buttons label[for="prev16"],
  .png-selector #prev17:checked ~ .selector-buttons label[for="prev17"],
  .png-selector #prev18:checked ~ .selector-buttons label[for="prev18"],
  .png-selector #prev19:checked ~ .selector-buttons label[for="prev19"],
  .png-selector #prev20:checked ~ .selector-buttons label[for="prev20"],
  .png-selector #prev21:checked ~ .selector-buttons label[for="prev21"],
  .png-selector #prev22:checked ~ .selector-buttons label[for="prev22"],
  .png-selector #prev23:checked ~ .selector-buttons label[for="prev23"],
  .png-selector #prev24:checked ~ .selector-buttons label[for="prev24"],
  .png-selector #prev25:checked ~ .selector-buttons label[for="prev25"],
  .png-selector #prev26:checked ~ .selector-buttons label[for="prev26"],
  .png-selector #prev27:checked ~ .selector-buttons label[for="prev27"],
  .png-selector #prev28:checked ~ .selector-buttons label[for="prev28"],
  .png-selector #prev29:checked ~ .selector-buttons label[for="prev29"],
  .png-selector #prev30:checked ~ .selector-buttons label[for="prev30"],
  .png-selector #prev31:checked ~ .selector-buttons label[for="prev31"],
  .png-selector #prev32:checked ~ .selector-buttons label[for="prev32"] {
    background: #007bff;
    color: white;
    border-color: #007bff;
  }

---

## Recapitulation and reproduction of Park et al., 2025

In this section, we provide a detailed description of Park et al. <d-cite key="park2025iclr"></d-cite>. We successfully reproduce their results on Llama-3.1-8B <d-cite key="grattafiori2024llama3"></d-cite>.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/2026-04-27-iclr-induction/park_fig_1.png" class="" %}
    </div>
</div>
<div class="caption">
   <b>Figure 1.</b> <b>Overview of Park et al., 2025.</b> The grid tracing task uses a $4 {\times} 4$ grid of words. Models observe random walks on the grid (e.g., <code>&nbsp;apple</code><code>&nbsp;bird</code><code>&nbsp;milk</code><code>&nbsp;sand</code><code>&nbsp;sun</code><code>&nbsp;plane</code><code>&nbsp;opera</code><code>&nbsp;...</code>) where consecutive words are always neighbors. As the sequence length grows, the model begins to predict valid next words based on the learned graph structure. More surprisingly, the geometry of the model's effective token representations mirrors that of the grid structure: the model learns (in context) to represent each node adjacent to its neighbor.
   <br>
   Figure reproduced from Park et al., 2025.
</div>


### The grid tracing task

Park et al. <d-cite key="park2025iclr"></d-cite> introduce the *in-context graph tracing* task.
The task involves a predefined graph $$\mathcal{G} = (\mathcal{T}, E)$$ where nodes $$\mathcal{T} = \{\tau_1, \tau_2, \ldots, \tau_n\}$$ are referenced via tokens (e.g., <code>&nbsp;apple</code>, <code>&nbsp;bird</code>, <code>&nbsp;math</code>, etc.). The graph's connectivity structure $$E$$ is defined independently of any semantic relationships between the concepts. The model is provided with traces of random walks on this graph as context and must predict valid next nodes based on the learned connectivity structure. While <d-cite key="park2025iclr"></d-cite> study graph tracing on three different graph structures, we focus exclusively on their $$4{\times}4$$ square grid setting (Figure 1). We provide details of the experimental setup below; our methodology always follows <d-cite key="park2025iclr"></d-cite> except when otherwise noted.

**Grid structure.** The task uses a $$4{\times}4$$ grid of 16 distinct word tokens: <code>&nbsp;apple</code>, <code>&nbsp;bird</code>, <code>&nbsp;car</code>, <code>&nbsp;egg</code>, <code>&nbsp;house</code>, <code>&nbsp;milk</code>, <code>&nbsp;plane</code>, <code>&nbsp;opera</code>, <code>&nbsp;box</code>, <code>&nbsp;sand</code>, <code>&nbsp;sun</code>, <code>&nbsp;mango</code>, <code>&nbsp;rock</code>, <code>&nbsp;math</code>, <code>&nbsp;code</code>, <code>&nbsp;phone</code>.<d-footnote>All words tokenize to exactly one token when preceded by a space (e.g., <code>&nbsp;apple</code> is a single token). Sequences are tokenized with a leading space before the first word, ensuring single-token-per-word encoding.</d-footnote> Each word occupies a unique position in the grid. Two words are *neighbors* if they are horizontally or vertically adjacent (not diagonally). This defines an adjacency matrix $$A \in \{0,1\}^{16 \times 16}$$ where $$A_{ij} = 1$$ if and only if words $$i$$ and $$j$$ are neighbors.

**Random walk generation.** Training sequences are generated via random walks on this grid. Starting from a random position, at each step the walk moves to a uniformly random neighbor. This produces sequences like <code>&nbsp;apple</code><code>&nbsp;bird</code><code>&nbsp;milk</code><code>&nbsp;sand</code><code>&nbsp;sun</code><code>&nbsp;plane</code><code>&nbsp;opera</code><code>&nbsp;...</code> where consecutive words are always grid neighbors.
<!-- Experiments use batch size 16 with *uniform initialization*: each sequence in the batch starts at a different grid position (one for each of the 16 words). This ensures all grid positions are represented across the batch. -->
Following <d-cite key="park2025iclr"></d-cite>, we use sequence lengths of 1400 tokens.

**Measuring accuracy.** At timestep $$t$$, the random walk is at node $$w_t$$ with neighbor set $$\mathcal{N}(w_t)$$. The model outputs a distribution $$p_{\theta}(\cdot \mid w_{1:t})$$ over vocabulary tokens.  "Rule following accuracy" is defined as the probability mass assigned to any valid next node:

$$
\text{acc}_t \;=\; \sum_{w \in \mathcal{N}(w_t)} p_{\theta}(w \mid w_{1:t}).
$$

<!-- At each timestep we average $$\text{acc}_t$$ over the 16 sequences in the batch, and report the resulting mean and standard deviation. For plots, we apply a 30-step moving-average smoothing. Figure 2 (left) shows accuracy increasing with context length, approaching $$\sim 0.90$$ after $$\sim10^3$$ tokens. -->

**PCA visualization.** To assess whether the model's representations come to resemble the grid structure, activations are extracted from a late layer (layer 26) and projected onto their top principal components. For each of the 16 words, a class-mean activation is computed by averaging over all occurrences in the final 200 positions of the sequence. The first two principal components of these 16 class-mean vectors define a 2D subspace onto which the class means are projected for visualization. If the representational geometry reflects the grid, neighboring tokens should appear nearby in this projection.

### Replication and interpretation


We replicate the results of Park et al. on Llama-3.1-8B. Figure 2 shows our reproduction: accuracy on the grid tracing task increases with context length, reaching $${\sim}0.90$$ after $${\sim}10^3$$ tokens, and PCA projections of late-layer activations reveal geometric structure mirroring the grid.

<div class="l-page">
  <div class="plot-row">
    <!-- Accuracy curve plot -->
    <div class="plot-container plot-standard w-65">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/repro_paper/accuracy_curve.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/repro_paper/accuracy_curve.png' | relative_url }}" alt="Accuracy curve showing model performance increasing with context length">
    </div>

    <!-- PCA visualization plot -->
    <div class="plot-container plot-tall w-35">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/repro_paper/pca_visualization.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/repro_paper/pca_visualization.png' | relative_url }}" alt="PCA visualization showing grid structure in token representations">
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure 2.</b> <b>Reproduction of main results from Park et al., 2025.</b>
  <b>Left:</b> Model accuracy on the grid tracing task increases with context length, reaching
  ${\sim}0.90$ accuracy after ${\sim}10^3$ tokens. Shaded region shows $\pm 1$ standard
  deviation across 16 random sequences. <b>Right:</b> PCA projection of class-mean activations at layer 26
  after seeing 1400 tokens. Gray dashed lines connect grid neighbors. The geometry of the
  effective representations resembles the grid structure underlying the data.
</div>

Park et al. interpret these findings as evidence that the geometric reorganization plays a functional role in task performance: the model learns the graph structure in its representations, and this learned structure is what enables accurate next-node predictions.

> "We see once a critical amount of context is seen by the model, accuracy starts to rapidly improve. We find this point in fact closely matches when Dirichlet Energy<d-footnote>Dirichlet energy measures how much a signal varies across graph edges. Low energy means neighboring nodes have similar representations, so Park et al. use it to quantify how well the model's representations respect the graph structure.</d-footnote> reaches its minimum value: energy is minimized shortly before the rapid increase in in-context task accuracy, suggesting that the structure of the data is correctly learned before the model can make valid predictions. This leads us to the claim that as the amount of context is scaled, there is an **emergent re-organization of representations that allows the model to perform well** on our in-context graph tracing task." <d-cite key="park2025iclr"></d-cite> (Section 4.1)

We propose a simpler mechanistic account in the following sections.

<!--
Other relevant quotes from Park et al. for reference:

> "As we show, increasing the amount of context leads to a sudden re-organization of representations in accordance with the graph's connectivity. This suggests LLMs can manipulate their representations in order to reflect concept semantics specified entirely in-context, inline with theories of inferential semantics from cognitive science." (Section 1)

> "This suggests an implicit optimization process, as hypothesized by theoretical work on ICL in toy setups (e.g., in-context linear regression), can transpire in more naturalistic settings." (Section 1)

> "In this work, we show that LLMs can flexibly manipulate their representations from semantics internalized based on pretraining data to semantics defined entirely in-context." (Section 7)

> "Interestingly, we find the ability to flexibly manipulate representations is in fact emergent with respect to context size—we propose a model based on energy minimization to hypothesize a mechanism for the underlying dynamics of this behavior. These results suggest context-scaling can unlock new capabilities, and, more broadly, this axis may have as of yet been underappreciated for improving a model." (Section 7)
-->

---

## A simpler explanation: induction circuits

We propose that the grid navigation task can be solved by a much simpler mechanism than abstract spatial representation learning: *induction circuits* <d-cite key="elhage2021mathematical"/>, <d-cite key="olsson2022context"/>.

An induction circuit consists of two types of attention heads working together. *Previous-token heads* attend from position $$t$$ to position $$t{-}1$$, copying information about the previous token into the current position's residual stream. *Induction heads* then attend to positions that follow previous occurrences of the current token. Together, they implement in-context bigram recall: "if $$A$$ followed $$B$$ before, predict $$A$$ when seeing $$B$$ again."<d-footnote>In the literature, the term "induction head" is sometimes used to refer to both the individual attention head and the full two-component circuit. We use "induction circuit" for the full mechanism and "induction head" for the specific head that attends to tokens following previous occurrences, to avoid ambiguity.</d-footnote>

In the grid task, if the model has seen the bigram <code>&nbsp;apple</code><code>&nbsp;bird</code> earlier in the sequence, then upon encountering <code>&nbsp;apple</code> again, the induction circuit can retrieve and predict <code>&nbsp;bird</code>. With enough context, the model will have observed multiple successors for each token, and can aggregate over these to assign probability mass to all valid neighbors.<d-footnote>For example, if the model has seen both <code>&nbsp;apple</code><code>&nbsp;bird</code> and <code>&nbsp;apple</code><code>&nbsp;house</code>, it can distribute probability across both <code>&nbsp;bird</code> and <code>&nbsp;house</code> when predicting the next token after <code>&nbsp;apple</code>.</d-footnote>

### Testing the induction hypothesis

If the model relies on induction heads to solve the task, then ablating these heads should substantially degrade task performance. We test this via *zero ablation*: setting targeted attention heads' outputs to zero and measuring the causal impact on both task accuracy and in-context representations.

**Head identification.** Following <d-cite key="olsson2022incontext"></d-cite>, we identify induction heads and previous-token heads using attention pattern analysis on repeated sequences (see Appendix A for details). Induction heads attend to positions one token *after* where the current token previously appeared. Previous-token heads simply attend to the immediately preceding position. We rank all 1024 heads in Llama-3.1-8B by their induction and previous-token scores, yielding two ranked lists.

**Ablation procedure.** For each head type, we ablate the top-$$k$$ heads for $$k \in \{1, 2, 4, 8, 16, 32\}$$ and measure impact on task accuracy and PCA structure of representations. As a control, we ablate random heads sampled from all heads excluding the top 32 induction and top 32 previous-token heads.

### Results


<div class="l-page">
  <div class="plot-row">
    <!-- Induction head ablation -->
    <div class="plot-container">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/ablation_induction.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/ablation_induction.png' | relative_url }}" alt="Effect of ablating induction heads on accuracy">
    </div>

    <!-- Previous-token head ablation -->
    <div class="plot-container">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/ablation_prev_token.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/ablation_prev_token.png' | relative_url }}" alt="Effect of ablating previous-token heads on accuracy">
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure 3.</b> <b>Effect of head ablation on task accuracy.</b>
  <b>Left:</b> Ablating top induction heads progressively degrades accuracy, but the model still learns with context.
  <b>Right:</b> Ablating top previous-token heads causes accuracy to plateau, preventing learning even with more context.
  <span style="color: gray;">Gray line</span> shows ablating 32 random heads (excluding top induction and prev-token heads) as a control.
</div>


**Both induction heads and previous-token heads are critical to task performance.**
Figure 3 shows task accuracy under head ablations. Ablating the top-4 induction heads causes accuracy to drop from $${\sim}0.90$$ to $${\sim}0.70$$, and ablating the top-32 drops accuracy all the way to $${\sim}0.40$$.
Ablating just the top-2 previous-token heads reduces accuracy $${<}0.60$$, and ablating the top-32 previous-token heads further drops accuracy to $${\sim}0.30$$.

In contrast, ablating $$k$$ random heads causes only very small degradation to $${\sim}0.85$$, suggesting that induction and previous-token heads are *particularly* important for task performance.<d-footnote>The random heads are sampled from the set of all heads <em>excluding</em> the top-32 induction heads and top-32 previous-token heads. We sample 4 different random sets of 32 heads and report the averaged results.</d-footnote>

While ablating induction heads significantly impairs task performance, accuracy continues to ascend as context length increases.
In contrast, ablating previous token heads causes accuracy to plateau, even as context length grows.

<div class="l-page">
  <div class="plot-row-triple">
    <!-- Baseline PCA -->
    <div class="plot-container">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_baseline.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_baseline.png' | relative_url }}" alt="PCA baseline - grid structure visible">
    </div>

    <!-- Induction heads ablated -->
    <div class="plot-container">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_induction.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_induction.png' | relative_url }}" alt="PCA with induction heads ablated - grid structure preserved">
    </div>

    <!-- Prev-token heads ablated -->
    <div class="plot-container">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_prev_token.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/pca_ablation_prev_token.png' | relative_url }}" alt="PCA with prev-token heads ablated - grid structure disrupted">
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure 4.</b> <b>Effect of head ablation on representational geometry.</b>
  PCA projections of class-mean activations under different ablation conditions.
  <b>Left:</b> Baseline (no ablation) shows clear grid structure.
  <b>Center:</b> Ablating top-32 induction heads preserves the grid geometry.
  <b>Right:</b> Ablating top-32 previous-token heads disrupts the spatial organization.
  This suggests previous-token heads are necessary for the geometric structure, while induction heads are not.
</div>

**Ablating previous-token heads disrupts representational organization.**
While both head types are important for accuracy, they seem to have different effects on learned representations. The figure below shows that ablating induction heads preserves the grid-like geometric structure in PCA visualizations, as the 2D projections still resemble the spatial grid. However, ablating previous-token heads disrupts this structure, causing representations to lose their apparent spatial organization.

## Previous-token mixing can account for representational structure

In the previous section, we studied *task performance* and argued that the model achieves high task accuracy by using induction circuits.
We now study the *representational geometry*, and ask if we can give a simple explanation for the grid-like PCA plots.
We will argue that it is plausible that the structures are byproducts of mixing performed by previous-token heads.

### The neighbor-mixing hypothesis

Figure 4 shows that ablating previous-token heads disrupts the grid structure, while ablating induction heads preserves it.
This suggests that previous-token heads are somehow necessary for the geometric organization.
But what mechanism could link previous-token heads to spatial structure?

Previous-token heads mix information from position $$t-1$$ into position $$t$$. In a random walk, the token at $$t-1$$ is always a grid neighbor of the token at $$t$$. So each token's representation gets mixed with a neighbor's. When we compute the class mean for word $$c$$, we average over all positions where $$c$$ appears, each mixed with whichever neighbor preceded it. Over many occurrences, $$c$$ is preceded by each of its neighbors roughly equally, so the class mean for $$c$$ roughly encodes $$c$$ plus an average of its neighbors.

To test whether neighbor-mixing alone can create the observed geometry, we construct a minimal toy model.

### A toy model of previous-token mixing

We work directly in a 16-token space indexed by the $$4{\times}4$$ grid nodes. Each node $$i$$ is assigned an initial random vector $$\mathbf{e}_i \in \mathbb{R}^{4096}$$, sampled i.i.d. from $$\mathcal{N}(0,I)$$. PCA of just the raw embeddings $$\{\mathbf{e}_i\}$$ produces an essentially unstructured cloud: there is no visible trace of the grid.

We then apply a single, "neighbor mixing" step:

$$
\tilde{\mathbf{e}}_i
\;=\;
\mathbf{e}_i
\;+\;
\frac{1}{|\mathcal{N}(i)|}
\sum_{j \in \mathcal{N}(i)} \mathbf{e}_j,
$$

where $$\mathcal{N}(i)$$ denotes the set of neighbors of node $$i$$.

After this one step, PCA of the 16 mixed vectors $$\{\tilde{\mathbf{e}}_i\}$$ recovers a clear $$4{\times}4$$ grid: neighbors are close in the 2D projection and non-neighbors are far (Figure 5).

<div class="l-page">
  <div class="plot-row">
    <!-- Random embeddings baseline (no mixing) -->
    <div class="plot-container plot-tall">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/neighbor_mixing/mixing_random_baseline.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/neighbor_mixing/mixing_random_baseline.png' | relative_url }}" alt="PCA of random embeddings without mixing - no structure visible">
    </div>

    <!-- After neighbor mixing -->
    <div class="plot-container plot-tall">
      <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/neighbor_mixing/mixing_random_mixed.html' | relative_url }}"></iframe>
      <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/neighbor_mixing/mixing_random_mixed.png' | relative_url }}" alt="PCA after neighbor mixing - grid structure emerges">
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure 5.</b> <b>One round of neighbor mixing creates grid structure from random embeddings.</b>
  <b>Left:</b> PCA projection of 16 random Gaussian vectors $\mathbf{e}_i \sim \mathcal{N}(0, I)$ shows no spatial structure.
  <b>Right:</b> After applying one neighbor-mixing step, the same embeddings exhibit clear grid organization in PCA space. Gray dashed lines connect grid neighbors.
</div>

### Evidence of neighbor mixing in individual model activations

We find additional evidence that the representational structure is a result of previous-token mixing.

Instead of collapsing each word into a single class mean, we take the final 200 positions of a length-1400 random-walk sequence and project all 200 residual-stream vectors into the same 2D PCA space used for the class means. Each point now corresponds to a specific activation.
For each point, we display bigram information: the center color indicates the current token $$w_t$$ and the border color indicates the previous token $$w_{t-1}$$.

<div class="l-page">
  <div class="plot-container plot-square">
    <iframe src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/bigram_pca_seed0.html' | relative_url }}"></iframe>
    <img class="plot-fallback" src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/bigram_pca_seed0.png' | relative_url }}" alt="Bigram PCA showing each point colored by current token (fill) and previous token (border)">
  </div>
</div>
<div class="caption">
  <b>Figure 6.</b> <b>Bigram-level PCA visualization.</b>
  Each point represents a single position's activation. Fill color indicates the current token; border color indicates the previous token. Points with the same current token but different previous tokens form distinct clusters, suggesting the representation encodes information about both. Star markers show token centroids.
</div>

Figure 6 shows a consistent pattern.
Points whose previous bigram is <code>&nbsp;plane</code><code>&nbsp;math</code> tend to lie between the <code>&nbsp;plane</code> and <code>&nbsp;math</code> centroids. Points with previous bigram <code>&nbsp;egg</code><code>&nbsp;math</code> tend to lie between <code>&nbsp;egg</code> and <code>&nbsp;math</code>. We see similar "in-between" behavior for all other bigrams.
This is what one would expect if the representation of $$w_t$$ contains something like a mixture of "self" and "previous token" rather than depending only on the current word.

## Limitations and open questions

Our experiments point towards a simple explanation: the model performs in-context graph tracing via induction circuits, and the grid-like PCA geometry is a byproduct of previous-token mixing.
However, our understanding remains incomplete in important ways.

**The toy model is a significant simplification.** Our neighbor-mixing rule assumes that previous-token heads simply add the raw previous-token vector $$\mathbf{h}_{t-1}$$ to the current position. In reality, attention heads apply value and output projections: they add $$W_O W_V \mathbf{h}_{t-1}$$, where $$W_O W_V \in \mathbb{R}^{d_{\text{model}} \times d_{\text{model}}}$$ is a low-rank matrix (rank $$\leq d_{\text{head}}$$). This projection could substantially transform the information being mixed, and notably cannot implement the identity mapping since it is low-rank. We also model everything as a single mixing step on static vectors, whereas the actual network has many attention heads, MLP blocks, and multiple layers that repeatedly transform the residual stream.

**Why does the grid structure emerge late in the sequence?** Previous-token heads are active from the start of the sequence, yet the grid-like PCA structure only becomes clearly visible after many tokens have been processed. If neighbor-mixing were the whole story, we might expect the geometric structure to appear earlier.

These gaps mean our account should be understood as a *plausible mechanistic hypothesis* rather than a complete explanation. The key empirical findings stand: ablating induction heads degrades task performance while preserving PCA geometry, and ablating previous-token heads disrupts both performance and geometry.

## Conclusion

We have argued that the phenomena observed by Park et al. <d-cite key="park2025iclr"></d-cite> can be explained by well-known mechanisms in language models. Task performance on in-context graph tracing is well-explained by induction circuits, which recall previously-seen bigrams. The geometric organization visible in PCA plots appears to be a byproduct of previous-token mixing: because random walks traverse graph edges, previous-token heads mix each position's representation with that of a graph neighbor, and this mixing alone is sufficient to produce grid-like structure from unstructured embeddings.

These findings suggest that the "representation reorganization" observed by Park et al. may not reflect a sophisticated in-context learning strategy, but rather an artifact of previous-token head behavior.

That said, our understanding has clear limits. Our toy model ignores the value and output projections of real attention heads, and we cannot yet explain why the geometric structure emerges only late in the sequence.

---

## Appendix A: Head Detection Methodology

We identify induction heads and previous-token heads using attention pattern analysis on synthetic repeated sequences, following the approach of Olsson et al. <d-cite key="olsson2022incontext"></d-cite>.

### A.1 Test Sequence Construction

We construct a test sequence by repeating a random sequence of tokens:

$$[\text{tok}_1, \ldots, \text{tok}_{32}, \text{tok}_1, \ldots, \text{tok}_{32}]$$

where tokens are sampled uniformly from the lower half of the vocabulary. The full sequence has length $$T = 64$$. We run the model on a batch of 32 such sequences and extract attention patterns from all heads.

For layer $$\ell \in \{1, \ldots, 32\}$$ and head $$h \in \{1, \ldots, 32\}$$ (Llama-3.1-8B has 32 layers and 32 heads per layer), let $$P^{(\ell,h)} \in \mathbb{R}^{T \times T}$$ denote the attention pattern, where $$P^{(\ell,h)}_{t,s}$$ is the attention weight from position $$t$$ (query) to position $$s$$ (key).

### A.2 Induction Score

Induction heads exhibit a characteristic pattern: for a repeated token at position $$i$$ in the second half of the sequence, they attend not to its earlier occurrence at position $$i - 32$$, but to the token *after* that earlier occurrence, i.e., position $$i - 31$$.

We compute the induction score as the average attention along this offset-31 diagonal:

$$s_{\text{ind}}^{(\ell,h)} = \frac{1}{32} \sum_{i=33}^{64} P^{(\ell,h)}_{i, i-31}$$

This measures how strongly the head attends to "the token that followed the previous occurrence of the current token."

### A.3 Previous-Token Score

Previous-token heads implement a simpler pattern: at each position, they attend primarily to the immediately preceding token.

We compute the previous-token score as the average attention along the offset-1 diagonal:

$$s_{\text{prev}}^{(\ell,h)} = \frac{1}{T-1} \sum_{i=2}^{T} P^{(\ell,h)}_{i, i-1}$$

### A.4 Head Ranking

We average scores across the batch of 32 sequences, then rank all 1024 heads (32 layers $$\times$$ 32 heads) by their induction and previous-token scores separately. This yields two ranked lists. For ablation experiments, we ablate the top-$$k$$ heads from each list for $$k \in \{1, 2, 4, 8, 16, 32\}$$.

---

## Appendix B: Attention Pattern Gallery

This appendix provides detailed visualizations of the attention patterns for the top-ranked induction heads and previous-token heads identified in our analysis. Click the buttons to explore different heads, ranked by their induction or previous-token score.

### B.1 Induction Head Attention Patterns

Induction heads attend to positions where the current token previously appeared, specifically attending one position *after* the previous occurrence. In the visualization below, the induction diagonal (offset by half the sequence length) shows elevated attention weights.

<div class="l-page">
  <div class="png-selector">
    <input type="radio" name="ind-head" id="ind1" checked>
    <input type="radio" name="ind-head" id="ind2">
    <input type="radio" name="ind-head" id="ind3">
    <input type="radio" name="ind-head" id="ind4">
    <input type="radio" name="ind-head" id="ind5">
    <input type="radio" name="ind-head" id="ind6">
    <input type="radio" name="ind-head" id="ind7">
    <input type="radio" name="ind-head" id="ind8">
    <input type="radio" name="ind-head" id="ind9">
    <input type="radio" name="ind-head" id="ind10">
    <input type="radio" name="ind-head" id="ind11">
    <input type="radio" name="ind-head" id="ind12">
    <input type="radio" name="ind-head" id="ind13">
    <input type="radio" name="ind-head" id="ind14">
    <input type="radio" name="ind-head" id="ind15">
    <input type="radio" name="ind-head" id="ind16">
    <input type="radio" name="ind-head" id="ind17">
    <input type="radio" name="ind-head" id="ind18">
    <input type="radio" name="ind-head" id="ind19">
    <input type="radio" name="ind-head" id="ind20">
    <input type="radio" name="ind-head" id="ind21">
    <input type="radio" name="ind-head" id="ind22">
    <input type="radio" name="ind-head" id="ind23">
    <input type="radio" name="ind-head" id="ind24">
    <input type="radio" name="ind-head" id="ind25">
    <input type="radio" name="ind-head" id="ind26">
    <input type="radio" name="ind-head" id="ind27">
    <input type="radio" name="ind-head" id="ind28">
    <input type="radio" name="ind-head" id="ind29">
    <input type="radio" name="ind-head" id="ind30">
    <input type="radio" name="ind-head" id="ind31">
    <input type="radio" name="ind-head" id="ind32">
    <div class="selector-buttons">
      <label for="ind1">L15H30</label>
      <label for="ind2">L8H1</label>
      <label for="ind3">L16H20</label>
      <label for="ind4">L2H22</label>
      <label for="ind5">L10H14</label>
      <label for="ind6">L5H11</label>
      <label for="ind7">L15H1</label>
      <label for="ind8">L24H27</label>
      <label for="ind9">L20H14</label>
      <label for="ind10">L5H8</label>
      <label for="ind11">L20H1</label>
      <label for="ind12">L19H3</label>
      <label for="ind13">L2H20</label>
      <label for="ind14">L26H15</label>
      <label for="ind15">L2H12</label>
      <label for="ind16">L27H6</label>
      <label for="ind17">L26H13</label>
      <label for="ind18">L28H15</label>
      <label for="ind19">L2H25</label>
      <label for="ind20">L13H6</label>
      <label for="ind21">L16H1</label>
      <label for="ind22">L22H14</label>
      <label for="ind23">L27H5</label>
      <label for="ind24">L27H7</label>
      <label for="ind25">L27H4</label>
      <label for="ind26">L2H21</label>
      <label for="ind27">L10H13</label>
      <label for="ind28">L5H9</label>
      <label for="ind29">L16H23</label>
      <label for="ind30">L20H13</label>
      <label for="ind31">L27H20</label>
      <label for="ind32">L19H0</label>
    </div>
    <div class="selector-images">
      <div class="selector-image img-ind1"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank01_L15H30.png' | relative_url }}" alt="L15H30"></div>
      <div class="selector-image img-ind2"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank02_L8H1.png' | relative_url }}" alt="L8H1"></div>
      <div class="selector-image img-ind3"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank03_L16H20.png' | relative_url }}" alt="L16H20"></div>
      <div class="selector-image img-ind4"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank04_L2H22.png' | relative_url }}" alt="L2H22"></div>
      <div class="selector-image img-ind5"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank05_L10H14.png' | relative_url }}" alt="L10H14"></div>
      <div class="selector-image img-ind6"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank06_L5H11.png' | relative_url }}" alt="L5H11"></div>
      <div class="selector-image img-ind7"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank07_L15H1.png' | relative_url }}" alt="L15H1"></div>
      <div class="selector-image img-ind8"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank08_L24H27.png' | relative_url }}" alt="L24H27"></div>
      <div class="selector-image img-ind9"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank09_L20H14.png' | relative_url }}" alt="L20H14"></div>
      <div class="selector-image img-ind10"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank10_L5H8.png' | relative_url }}" alt="L5H8"></div>
      <div class="selector-image img-ind11"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank11_L20H1.png' | relative_url }}" alt="L20H1"></div>
      <div class="selector-image img-ind12"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank12_L19H3.png' | relative_url }}" alt="L19H3"></div>
      <div class="selector-image img-ind13"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank13_L2H20.png' | relative_url }}" alt="L2H20"></div>
      <div class="selector-image img-ind14"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank14_L26H15.png' | relative_url }}" alt="L26H15"></div>
      <div class="selector-image img-ind15"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank15_L2H12.png' | relative_url }}" alt="L2H12"></div>
      <div class="selector-image img-ind16"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank16_L27H6.png' | relative_url }}" alt="L27H6"></div>
      <div class="selector-image img-ind17"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank17_L26H13.png' | relative_url }}" alt="L26H13"></div>
      <div class="selector-image img-ind18"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank18_L28H15.png' | relative_url }}" alt="L28H15"></div>
      <div class="selector-image img-ind19"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank19_L2H25.png' | relative_url }}" alt="L2H25"></div>
      <div class="selector-image img-ind20"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank20_L13H6.png' | relative_url }}" alt="L13H6"></div>
      <div class="selector-image img-ind21"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank21_L16H1.png' | relative_url }}" alt="L16H1"></div>
      <div class="selector-image img-ind22"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank22_L22H14.png' | relative_url }}" alt="L22H14"></div>
      <div class="selector-image img-ind23"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank23_L27H5.png' | relative_url }}" alt="L27H5"></div>
      <div class="selector-image img-ind24"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank24_L27H7.png' | relative_url }}" alt="L27H7"></div>
      <div class="selector-image img-ind25"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank25_L27H4.png' | relative_url }}" alt="L27H4"></div>
      <div class="selector-image img-ind26"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank26_L2H21.png' | relative_url }}" alt="L2H21"></div>
      <div class="selector-image img-ind27"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank27_L10H13.png' | relative_url }}" alt="L10H13"></div>
      <div class="selector-image img-ind28"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank28_L5H9.png' | relative_url }}" alt="L5H9"></div>
      <div class="selector-image img-ind29"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank29_L16H23.png' | relative_url }}" alt="L16H23"></div>
      <div class="selector-image img-ind30"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank30_L20H13.png' | relative_url }}" alt="L20H13"></div>
      <div class="selector-image img-ind31"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank31_L27H20.png' | relative_url }}" alt="L27H20"></div>
      <div class="selector-image img-ind32"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/induction_patterns/rank32_L19H0.png' | relative_url }}" alt="L19H0"></div>
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure A1.</b> <b>Induction head attention patterns.</b> Each heatmap shows the attention pattern for one of the top induction heads. The x-axis is the key (source) position, and the y-axis is the query (destination) position. Brighter colors indicate higher attention weights. Click the buttons to switch between heads.
</div>

### B.2 Previous-Token Head Attention Patterns

Previous-token heads attend primarily to position $$t-1$$, implementing a simple "look at what came before" operation. This creates a strong diagonal pattern one position below the main diagonal.

<div class="l-page">
  <div class="png-selector">
    <input type="radio" name="prev-head" id="prev1" checked>
    <input type="radio" name="prev-head" id="prev2">
    <input type="radio" name="prev-head" id="prev3">
    <input type="radio" name="prev-head" id="prev4">
    <input type="radio" name="prev-head" id="prev5">
    <input type="radio" name="prev-head" id="prev6">
    <input type="radio" name="prev-head" id="prev7">
    <input type="radio" name="prev-head" id="prev8">
    <input type="radio" name="prev-head" id="prev9">
    <input type="radio" name="prev-head" id="prev10">
    <input type="radio" name="prev-head" id="prev11">
    <input type="radio" name="prev-head" id="prev12">
    <input type="radio" name="prev-head" id="prev13">
    <input type="radio" name="prev-head" id="prev14">
    <input type="radio" name="prev-head" id="prev15">
    <input type="radio" name="prev-head" id="prev16">
    <input type="radio" name="prev-head" id="prev17">
    <input type="radio" name="prev-head" id="prev18">
    <input type="radio" name="prev-head" id="prev19">
    <input type="radio" name="prev-head" id="prev20">
    <input type="radio" name="prev-head" id="prev21">
    <input type="radio" name="prev-head" id="prev22">
    <input type="radio" name="prev-head" id="prev23">
    <input type="radio" name="prev-head" id="prev24">
    <input type="radio" name="prev-head" id="prev25">
    <input type="radio" name="prev-head" id="prev26">
    <input type="radio" name="prev-head" id="prev27">
    <input type="radio" name="prev-head" id="prev28">
    <input type="radio" name="prev-head" id="prev29">
    <input type="radio" name="prev-head" id="prev30">
    <input type="radio" name="prev-head" id="prev31">
    <input type="radio" name="prev-head" id="prev32">
    <div class="selector-buttons">
      <label for="prev1">L0H2</label>
      <label for="prev2">L14H26</label>
      <label for="prev3">L1H20</label>
      <label for="prev4">L1H18</label>
      <label for="prev5">L9H11</label>
      <label for="prev6">L1H16</label>
      <label for="prev7">L0H3</label>
      <label for="prev8">L7H2</label>
      <label for="prev9">L0H24</label>
      <label for="prev10">L14H8</label>
      <label for="prev11">L1H13</label>
      <label for="prev12">L0H26</label>
      <label for="prev13">L11H16</label>
      <label for="prev14">L21H7</label>
      <label for="prev15">L7H1</label>
      <label for="prev16">L0H6</label>
      <label for="prev17">L1H24</label>
      <label for="prev18">L1H4</label>
      <label for="prev19">L12H16</label>
      <label for="prev20">L1H26</label>
      <label for="prev21">L9H10</label>
      <label for="prev22">L6H8</label>
      <label for="prev23">L9H8</label>
      <label for="prev24">L25H20</label>
      <label for="prev25">L1H30</label>
      <label for="prev26">L16H29</label>
      <label for="prev27">L1H1</label>
      <label for="prev28">L0H29</label>
      <label for="prev29">L14H9</label>
      <label for="prev30">L18H26</label>
      <label for="prev31">L21H10</label>
      <label for="prev32">L9H9</label>
    </div>
    <div class="selector-images">
      <div class="selector-image img-prev1"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank01_L0H2.png' | relative_url }}" alt="L0H2"></div>
      <div class="selector-image img-prev2"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank02_L14H26.png' | relative_url }}" alt="L14H26"></div>
      <div class="selector-image img-prev3"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank03_L1H20.png' | relative_url }}" alt="L1H20"></div>
      <div class="selector-image img-prev4"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank04_L1H18.png' | relative_url }}" alt="L1H18"></div>
      <div class="selector-image img-prev5"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank05_L9H11.png' | relative_url }}" alt="L9H11"></div>
      <div class="selector-image img-prev6"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank06_L1H16.png' | relative_url }}" alt="L1H16"></div>
      <div class="selector-image img-prev7"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank07_L0H3.png' | relative_url }}" alt="L0H3"></div>
      <div class="selector-image img-prev8"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank08_L7H2.png' | relative_url }}" alt="L7H2"></div>
      <div class="selector-image img-prev9"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank09_L0H24.png' | relative_url }}" alt="L0H24"></div>
      <div class="selector-image img-prev10"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank10_L14H8.png' | relative_url }}" alt="L14H8"></div>
      <div class="selector-image img-prev11"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank11_L1H13.png' | relative_url }}" alt="L1H13"></div>
      <div class="selector-image img-prev12"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank12_L0H26.png' | relative_url }}" alt="L0H26"></div>
      <div class="selector-image img-prev13"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank13_L11H16.png' | relative_url }}" alt="L11H16"></div>
      <div class="selector-image img-prev14"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank14_L21H7.png' | relative_url }}" alt="L21H7"></div>
      <div class="selector-image img-prev15"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank15_L7H1.png' | relative_url }}" alt="L7H1"></div>
      <div class="selector-image img-prev16"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank16_L0H6.png' | relative_url }}" alt="L0H6"></div>
      <div class="selector-image img-prev17"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank17_L1H24.png' | relative_url }}" alt="L1H24"></div>
      <div class="selector-image img-prev18"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank18_L1H4.png' | relative_url }}" alt="L1H4"></div>
      <div class="selector-image img-prev19"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank19_L12H16.png' | relative_url }}" alt="L12H16"></div>
      <div class="selector-image img-prev20"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank20_L1H26.png' | relative_url }}" alt="L1H26"></div>
      <div class="selector-image img-prev21"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank21_L9H10.png' | relative_url }}" alt="L9H10"></div>
      <div class="selector-image img-prev22"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank22_L6H8.png' | relative_url }}" alt="L6H8"></div>
      <div class="selector-image img-prev23"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank23_L9H8.png' | relative_url }}" alt="L9H8"></div>
      <div class="selector-image img-prev24"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank24_L25H20.png' | relative_url }}" alt="L25H20"></div>
      <div class="selector-image img-prev25"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank25_L1H30.png' | relative_url }}" alt="L1H30"></div>
      <div class="selector-image img-prev26"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank26_L16H29.png' | relative_url }}" alt="L16H29"></div>
      <div class="selector-image img-prev27"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank27_L1H1.png' | relative_url }}" alt="L1H1"></div>
      <div class="selector-image img-prev28"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank28_L0H29.png' | relative_url }}" alt="L0H29"></div>
      <div class="selector-image img-prev29"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank29_L14H9.png' | relative_url }}" alt="L14H9"></div>
      <div class="selector-image img-prev30"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank30_L18H26.png' | relative_url }}" alt="L18H26"></div>
      <div class="selector-image img-prev31"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank31_L21H10.png' | relative_url }}" alt="L21H10"></div>
      <div class="selector-image img-prev32"><img src="{{ 'assets/html/2026-04-27-iclr-induction/induction_analysis/prev_token_patterns/rank32_L9H9.png' | relative_url }}" alt="L9H9"></div>
    </div>
  </div>
</div>
<div class="caption">
  <b>Figure A2.</b> <b>Previous-token head attention patterns.</b> Each heatmap shows the attention pattern for one of the top previous-token heads. The strong sub-diagonal pattern indicates attention to position $t-1$. Click the buttons to switch between heads.
</div>
