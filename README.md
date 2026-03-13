# 🤖 SHAP Governance Framework for AI Agents

> A comprehensive, production-ready framework for explaining, auditing, and monitoring AI agent behaviour using SHAP — covering LLM agents, RL agents, tool-calling agents, and multi-agent systems.

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![SHAP](https://img.shields.io/badge/shap-latest-orange.svg)](https://shap.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-violet.svg)](https://opensource.org/licenses/MIT)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626.svg)](https://jupyter.org/)

---

## 📋 What's Inside

The framework is delivered as a **Jupyter notebook + presentation deck**, structured into 9 sections:

| Section | Description |
|---------|-------------|
| **0. Setup & Log Loading** | Universal agent log schema, drop-in data interface for any log format |
| **1. Feature Proxy Layer** | Convert opaque embeddings/state into SHAP-ready numeric features |
| **2. Explainer Selection** | Auto-detect optimal SHAP explainer per agent type |
| **3. Attribution Analysis** | Action-level, trajectory-level & tool-call SHAP explanations |
| **4. Multi-Agent Attribution** | Hierarchical SHAP composition across agent boundaries |
| **5. Governance Checks** | Automated quality gates with configurable thresholds |
| **6. Bias & Fairness Audit** | Protected attribute attribution analysis |
| **7. Behaviour Drift Monitoring** | KS-test based SHAP drift detection |
| **8. Agent Model Card** | Auto-generate explainability documentation |
| **9. Production Checklist** | Deployment readiness scoring & artefact export |

---

## 🤖 Supported Agent Types

| Agent Type | SHAP Approach | Key Challenge Solved |
|-----------|---------------|---------------------|
| 🧠 **LLM Agents** (GPT, Claude, Llama) | `KernelExplainer` on feature proxies | Black-box embeddings, non-determinism |
| 🎮 **RL Agents** | `TreeExplainer` / `LinearExplainer` | Sequential decisions, temporal credit assignment |
| 🔧 **Tool-Calling Agents** | `KernelExplainer` (multi-class) | Explaining *why* each tool was selected |
| 🕸️ **Multi-Agent Systems** | Hierarchical SHAP composition | Cross-agent attribution tracing |

---

## 🚀 Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/cyycan/shap-agent-governance.git
cd shap-agent-governance
```

### 2. Install dependencies

```bash
not available install one by one please -- pip install -r requirements.txt
```

### 3. Launch the notebook

```bash
jupyter notebook shap_agent_governance.ipynb
```

### 4. Load your agent logs

In **Section 0.3**, replace the simulator block with your own log loader:

```python
# Load from CSV / JSONL / Parquet
df_logs = pd.read_csv('your_agent_logs.csv')
# or
df_logs = pd.read_json('your_agent_logs.jsonl', lines=True)

# Configure column names
ACTION_COL        = 'your_action_column'
REWARD_COL        = 'your_reward_column'
TRACE_COL         = 'your_trace_id_column'
AGENT_TYPE_COL    = 'your_agent_type_column'
PROTECTED_ATTRS   = ['user_age', 'region']   # for bias auditing
```

The rest adapts automatically.

---

## 🗂️ Universal Agent Log Schema

Every row in your log should represent **one agent step**. Required fields:

| Field | Type | Description |
|-------|------|-------------|
| `trace_id` | str | Unique episode or trajectory ID |
| `step_idx` | int | Step index within the episode |
| `agent_type` | str | `'llm'`, `'rl'`, `'tool_calling'`, `'multi_agent'` |
| `action_taken` | str | Action label or tool name selected |
| `reward` | float | Step reward (RL) or outcome score (LLM) |
| `feat_*` | float | Any number of proxy feature columns (SHAP input) |

Optional but recommended:
- `agent_id` — for multi-agent systems
- `action_logprob` — log-probability of the chosen action
- `tool_output_score` — quality score of tool output

> **Don't have the `feat_*` columns yet?** See Section 1 of the notebook for the Feature Proxy Layer, which converts raw agent state (embeddings, token counts, observations) into SHAP-ready numeric features.

---

## ⚙️ Configuration

All governance thresholds are centralised in **Section 5**:

```python
AGENT_GOVERNANCE_CONFIG = {
    'min_top3_coverage':              0.55,  # Top-3 features explain ≥ 55% of SHAP
    'max_single_feature_dominance':   0.65,  # No feature explains > 65%
    'max_attribution_cv':             0.18,  # Attribution stability CV threshold
    'min_meaningful_features':        4,     # Minimum non-trivial features
    'trivial_threshold':              0.01,  # Below this fraction = trivial
}

DRIFT_KS_THRESHOLD = 0.10   # KS statistic above which drift is flagged
BIAS_THRESHOLD     = 0.05   # Flag protected attrs explaining > 5% of SHAP
```

---

## 🏛️ Governance Framework

### Quality Gates (Section 5)

- **Top-3 coverage** — Are decisions sufficiently concentrated to explain?
- **Single-feature dominance** — Is the agent over-relying on one feature?
- **Attribution stability** — Are SHAP values stable across bootstrap samples?
- **Meaningful feature count** — Does the agent use enough features for decisions?

### Bias Audit (Section 6)

Set `PROTECTED_ATTRS = ['col1', 'col2']` to flag whether sensitive attributes disproportionately drive agent actions.

### Drift Monitoring (Section 7)

Compares SHAP distributions between a reference window and current logs using KS test — works for all four agent types. Integrate into your MLOps pipeline for continuous monitoring.

### Hierarchical Multi-Agent Attribution (Section 4)

Traces *which agent* contributed *how much* to the overall system output. Essential for debugging multi-agent pipelines and meeting regulatory attribution requirements.

---

## 🗂️ Repository Structure

```
shap-agent-governance/
├── shap_agent_governance.ipynb          # Main notebook
├── SHAP_Agent_Governance_Presentation.pptx  # Slide deck
├── requirements.txt                     # Dependencies
├── README.md                            # This file
└── AGENT_MODEL_CARD.md                  # Generated after first run
```

---

## 📦 Requirements

```
shap
pandas
numpy
scikit-learn
matplotlib
seaborn
xgboost
scipy
joblib
ipywidgets
jupyter
```

Install:
```bash
pip install -r requirements.txt
```

---

## 📖 References

- [Lundberg & Lee (2017) — A Unified Approach to Interpreting Model Predictions](https://arxiv.org/abs/1705.07874)
- [SHAP Documentation](https://shap.readthedocs.io/en/latest/)
- [Towards Explainability for LLM Agents](https://arxiv.org/abs/2401.13138)
- [EU AI Act — High-Risk AI Transparency Requirements](https://artificialintelligenceact.eu/)
- [NIST AI RMF](https://www.nist.gov/system/files/documents/2023/01/26/AI_RMF_1.0.pdf)

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

*Built to help teams implement explainability standards for AI agents in production systems.*
