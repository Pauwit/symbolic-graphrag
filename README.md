# EPITA 2026 — Intelligence Symbolique
> Groupe de Matteo Atkinson et Paul Witkowski

## G3 — GraphRAG — combine Knowledge Graphs et LLM pour le RAG

Le GraphRAG (Graph-based Retrieval-Augmented Generation) represente une evolution majeure des systemes RAG en integrant la structure relationnelle des graphes de connaissances avec les capacites de generation des grands modeles de langage. Contrairement au RAG vectoriel classique qui s'appuie uniquement sur la similarite cosinus entre embeddings, le GraphRAG exploite les relations structurelles entre entites pour enrichir le contexte de generation, permettant ainsi de repondre a des questions necessitant du raisonnement multi-sauts. Ce sujet propose d'implementer un pipeline complet : extraction d'entites et de relations a partir de documents, construction du graphe, community detection pour le partitionnement thematique, et requetage combine graphe + LLM. L'evaluation comparative avec un RAG vectoriel classique sur un jeu de donnees de reference (HotpotQA, MuSiQue) mettra en evidence les gains en precision et en coherence des reponses.

### Objectifs
- Implementer un pipeline d'extraction d'entites et de relations depuis des documents textuels vers un graphe RDF ou property graph
- Integrate la structure du graphe dans le processus de retrieval (traversals, community summaries, subgraph extraction)
- Comparer les performances de GraphRAG vs. RAG vectoriel sur un benchmark de QA multi-hop
- Evaluer l'impact du partitionnement en communautes (Leiden, Louvain) sur la qualite des reponses
- Analyser les compromis entre cout de construction du graphe, latence de requetage et qualite des reponses

### References externes
- Edge, D., et al. (2024). "From Local to Global: A Graph RAG Approach to Query-Focused Summarization." *Microsoft Research*. [arXiv](https://arxiv.org/abs/2404.16130)
- Wu, L., et al. (2025). "Neural-Symbolic Reasoning over Knowledge Graphs." *ACM Computing Surveys*. [ACM](https://doi.org/10.1145/3638529)
- Lewis, P., et al. (2020). "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *NeurIPS*. [NeurIPS](https://proceedings.neurips.cc/paper/2020/hash/6b493230205f780e1bc2694567247b7f-Abstract.html)
- Hogan, A., et al. (2022). "Knowledge Graphs." *ACM Computing Surveys*, 54(4). [ACM](https://doi.org/10.1145/3447772)

---

## Structure du projet

```
groupe-G3-GraphRAG-combine_Knowledge_Graphs_et_LLM_pour_le_RAG/
├── app/                     # Serveur FastAPI + interface web
│   ├── main.py              # Endpoints REST et streaming SSE
│   ├── models.py            # Schemas Pydantic
│   └── static/              # Interface (index.html / style.css / app.js)
├── graphrag_core/           # Bibliotheque GraphRAG
│   ├── extractor.py         # Extraction triplets entite-relation-entite (LLM)
│   ├── graph.py             # Construction et requetage du KG (RDF + NetworkX)
│   ├── llm.py               # Client LLM unifie (OpenAI / Anthropic / Ollama)
│   ├── pipeline.py          # Pipeline QA — BFS multi-hop + LLM
│   └── retriever.py         # Retrieval hybride (BFS + embeddings)
├── notebooks/               # Notebooks Jupyter (benchmark, KG, GraphRAG, SPARQL)
├── tests/                   # Tests unitaires pytest
├── data/                    # Donnees auto-generees au runtime
│   ├── custom/              # Documents uploades via l'interface
│   └── hotpotqa/            # Echantillons HotpotQA (telecharges automatiquement)
├── docs/                    # Design docs et visualisations
├── .env.example             # Gabarit de configuration
├── pyproject.toml           # Dependances et config projet (uv)
└── requirements.txt         # Dependances pip
```

## Lancement

### Prerequis

- Python >= 3.13
- [uv](https://docs.astral.sh/uv/) (recommande) ou pip
- Une cle API : OpenAI, Anthropic, ou Ollama en local

### Installation

```bash
cd groupe-G3-GraphRAG-combine_Knowledge_Graphs_et_LLM_pour_le_RAG

# Avec uv (recommande)
uv sync

# Ou avec pip
python -m venv .venv
.venv\Scripts\activate       # Windows
# source .venv/bin/activate  # Linux/macOS
pip install -r requirements.txt
```

### Configuration

```bash
cp .env.example .env
```

Editer `.env` et renseigner au minimum :

```env
LLM_PROVIDER=openai          # openai | anthropic | ollama
LLM_MODEL=gpt-4o-mini
OPENAI_API_KEY=sk-...        # ou ANTHROPIC_API_KEY / OLLAMA_BASE_URL
```

### Demarrage du serveur

```bash
# Avec uv
uv run uvicorn app.main:app --reload

# Ou directement (dans le venv active)
uvicorn app.main:app --reload
```

Interface disponible sur **http://localhost:8000**

### Notebooks

```bash
uv run jupyter notebook notebooks/
```

### Tests

```bash
uv run pytest
```
