# Graph Report - ./wiki  (2026-04-22)

## Corpus Check
- Corpus is ~2,513 words - fits in a single context window. You may not need a graph.

## Summary
- 23 nodes · 36 edges · 3 communities detected
- Extraction: 94% EXTRACTED · 6% INFERRED · 0% AMBIGUOUS · INFERRED: 2 edges (avg confidence: 0.5)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Flow-based Text-to-Image Generation|Flow-based Text-to-Image Generation]]
- [[_COMMUNITY_gieok Knowledge Automation System|gieok Knowledge Automation System]]
- [[_COMMUNITY_Diffusion Model Bias Correction|Diffusion Model Bias Correction]]

## God Nodes (most connected - your core abstractions)
1. `MeanFlow Text-to-Image Extension (EMF)` - 10 edges
2. `Diffusion SNR-t Bias Analysis` - 7 edges
3. `Operation Log` - 6 edges
4. `Wiki Index` - 6 edges
5. `gieok Concept — Claude Code Second Brain` - 6 edges
6. `gieok Project Detail` - 5 edges
7. `macOS LaunchAgent Catchup Behavior` - 4 edges
8. `SNR-t Bias in Diffusion Probabilistic Models` - 4 edges
9. `MeanFlow — Average Velocity Flow Matching` - 3 edges
10. `EMF (Extended MeanFlow for Text-to-Image)` - 3 edges

## Surprising Connections (you probably didn't know these)
- `MeanFlow — Average Velocity Flow Matching` --conceptually_related_to--> `SNR-t Bias in Diffusion Probabilistic Models`  [INFERRED]
  wiki/analyses/meanflow-text-to-image.md → wiki/analyses/diffusion-snr-t-bias.md
- `Operation Log` --references--> `MeanFlow Text-to-Image Extension (EMF)`  [EXTRACTED]
  wiki/log.md → wiki/analyses/meanflow-text-to-image.md
- `Operation Log` --references--> `Diffusion SNR-t Bias Analysis`  [EXTRACTED]
  wiki/log.md → wiki/analyses/diffusion-snr-t-bias.md
- `Wiki Index` --references--> `MeanFlow Text-to-Image Extension (EMF)`  [EXTRACTED]
  wiki/index.md → wiki/analyses/meanflow-text-to-image.md
- `Wiki Index` --references--> `Diffusion SNR-t Bias Analysis`  [EXTRACTED]
  wiki/index.md → wiki/analyses/diffusion-snr-t-bias.md

## Communities

### Community 0 - "Flow-based Text-to-Image Generation"
Cohesion: 0.25
Nodes (9): MeanFlow Text-to-Image Extension (EMF), arXiv:2505.13447 — MeanFlow (Geng et al., 2025), arXiv:2510.15857 — BLIP3o-NEXT (Chen et al., 2025), arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image, BLIP3o-NEXT (Vision-Language Aligned Text Encoder), Text Encoder Discriminability (for Few-Step Generation), Text Encoder Disentanglement (Semantic Separation), EMF (Extended MeanFlow for Text-to-Image) (+1 more)

### Community 1 - "gieok Knowledge Automation System"
Cohesion: 0.42
Nodes (9): macOS LaunchAgent Catchup Behavior, Claude Code (CC), gieok Hook→LLM→Wiki→Git Pipeline, macOS LaunchAgent (launchd scheduling), Obsidian Wiki Vault, gieok Concept — Claude Code Second Brain, Wiki Index, Operation Log (+1 more)

### Community 2 - "Diffusion Model Bias Correction"
Cohesion: 0.6
Nodes (5): Diffusion SNR-t Bias Analysis, arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs, DCW — Wavelet Domain Difference Correction, Exposure Bias in Diffusion Models, SNR-t Bias in Diffusion Probabilistic Models

## Knowledge Gaps
- **9 isolated node(s):** `arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image`, `arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs`, `Text Encoder Discriminability (for Few-Step Generation)`, `Text Encoder Disentanglement (Semantic Separation)`, `BLIP3o-NEXT (Vision-Language Aligned Text Encoder)` (+4 more)
  These have ≤1 connection - possible missing edges or undocumented components.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `MeanFlow Text-to-Image Extension (EMF)` connect `Flow-based Text-to-Image Generation` to `gieok Knowledge Automation System`, `Diffusion Model Bias Correction`?**
  _High betweenness centrality (0.558) - this node is a cross-community bridge._
- **Why does `Diffusion SNR-t Bias Analysis` connect `Diffusion Model Bias Correction` to `Flow-based Text-to-Image Generation`, `gieok Knowledge Automation System`?**
  _High betweenness centrality (0.284) - this node is a cross-community bridge._
- **What connects `arXiv:2604.18168 — EMF: Extending MeanFlow to Text-to-Image`, `arXiv:2604.16044 — Elucidating SNR-t Bias in DPMs`, `Text Encoder Discriminability (for Few-Step Generation)` to the rest of the system?**
  _9 weakly-connected nodes found - possible documentation gaps or missing edges._