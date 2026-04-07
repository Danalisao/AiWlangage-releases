# AiWlangage — Kit de Migration WinDev/WebDev

> Lisez un projet WLangage, comprenez sa logique métier, migrez vers la stack de votre choix — orchestré par VS Code Copilot + MCP + RAG.

![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue)
![Licence MIT](https://img.shields.io/badge/Licence-MIT-green)
![VS Code](https://img.shields.io/badge/VS%20Code-1.90%2B-blue)
![Tests](https://img.shields.io/badge/Tests-89%20passed-brightgreen)

---

## Installation

### ✅ Recommandé — Depuis la dernière release

Le flux recommandé pour tous les utilisateurs (dev ou production) :

**Tous les systèmes (Windows, macOS, Linux)** :

1. Accédez à la page des releases : [github.com/Danalisao/AiWlangage/releases/latest](https://github.com/Danalisao/AiWlangage/releases/latest)
2. Téléchargez la source zip (`.zip`) ou archive tar (`.tar.gz`) disponible
3. Extrayez localement
4. Lancez le script d'installation :

```bash
# macOS / Linux
cd chemin/vers/AiWlangage
bash install.sh

# Windows (PowerShell)
cd C:\chemin\vers\AiWlangage
.\install.ps1
```

Le script clone les composants, crée l'environnement Python, configure le RAG et les agents VS Code. **Aucune étape manuelle supplémentaire.**

### Fallback — Clone local (vous avez déjà le dépôt)

Si vous avez déjà cloné le dépôt localement :

```bash
cd ~/AiWlangage              # ou C:\AiWlangage sur Windows
./install.sh                 # ou .\install.ps1 sur Windows
```

### ⚡ Option avancée — One-liner rapide (exposé à certains antivirus)

**macOS / Linux** :
```bash
curl -sSL https://raw.githubusercontent.com/Danalisao/AiWlangage/main/install.sh | bash
```

**Windows (PowerShell)** :
```powershell
irm https://raw.githubusercontent.com/Danalisao/AiWlangage/main/install.ps1 | iex
```

⚠️ **Attention :** Certains antivirus (Windows Defender, Kaspersky, Avast, Norton…) bloquent ce pattern PowerShell en tant que vecteur potentiel de malveillance. Si vous rencontrez une erreur d'exécution, utilisez le **flux recommandé** (release + exécution locale).

### Installation manuelle

Consultez [GETTING_STARTED.md](GETTING_STARTED.md) pour les étapes détaillées.

Ensuite :

```bash
code ~/AiWlangage   # puis @WLangage-Atlas dans Copilot Chat
```

→ [Démarrage rapide complet](QUICKSTART.md)

---

## Ce que vous obtenez

### Un kit en 4 composants autonomes

| Composant | Rôle | Commande |
|-----------|------|---------|
| **`wlangage-rag/`** | Moteur RAG — 14 000+ docs WLangage, recherche hybride BM25 + E5, API FastAPI | `make start-rag` |
| **`wlangage-mcp-server/`** | 22 outils MCP pour VS Code — scan projet, lecture procédures, migration guidée | auto (VS Code) |
| **`wlangage-agents/`** | 13 agents VS Code Copilot — Atlas orchestre la migration complète | `@WLangage-Atlas` |
| **`wlangage-dataset-kit/`** | Collecteur de contributions — anonymise + exporte vos procédures WLangage | `make dataset` |

### Un workflow de migration en 5 étapes

```
1. Scan          @WLangage-Atlas analyse C:\MonProjet\App.wdp
                   → cartographie procédures + tables HFSQL + dépendances

2. Compréhension  WLangage-Analyst explique chaque procédure métier
                   → pseudocode universel, cartographie HFSQL

3. Spec           WLangage-Spec-Writer génère le cahier des charges
                   → ERD Mermaid + user stories Gherkin + API OpenAPI 3.1

4. Migration      WLangage-Migration-Director implémente dans la stack cible
                   → React/Node, Django, Spring Boot, ou Vue/FastAPI

5. Parité         WLangage-Test-Comparator valide le comportement
                   → tests comparatifs WLangage ↔ stack cible
```

---

## Stacks cibles

| Stack | Technologies |
|-------|-------------|
| `react-node-postgres` | React 18 TypeScript + Node.js + PostgreSQL + Prisma |
| `django` | Django 5 + PostgreSQL + Django ORM |
| `spring` | Spring Boot 3 + Java 21 + Spring Data JPA |
| `vue-fastapi` | Vue 3 TypeScript + FastAPI + SQLAlchemy |

---

## Ce dont vous avez besoin

- **Python 3.10+** et **Git**
- **VS Code 1.90+** avec **GitHub Copilot** actif
- Un backend LLM — **GitHub Copilot/Models recommandé** (inclus si vous avez Copilot), ou Ollama, Groq, Gemini, OpenAI, Anthropic

### Configuration — Deux fichiers `.env`

**1. `.env` racine** — pour les scripts/notebooks :

```env
LLM_BACKEND=ollama
OLLAMA_URL=http://localhost:11434
OLLAMA_MODEL=qwen3-coder:480b-cloud
MIGRATION_TARGET_STACK=react-node-postgres
```

**2. `wlangage-rag/rag/.env`** — pour le moteur RAG (obligatoire) :

Configurez vos clés API (Groq, Gemini, Ollama, etc.) dans `wlangage-rag/rag/.env`.

→ [Configuration complète](.env.example) | [Guide détaillé](QUICKSTART.md#3-configuration)

---

## Architecture

```
 Projet WinDev (.wdp / .wl)
         │
         ▼
  wl_scan_project (MCP)         ← lit procédures, tables, modules
         │
         ▼
  wl_explain_procedure (MCP)    ← RAG 14k docs + LLM → compréhension
  wl_to_pseudocode (MCP)
         │
         ▼
  WLangage-Spec-Writer           ← ERD + Gherkin + OpenAPI 3.1
         │
         ▼
  wl_migration_guidance (MCP)   ← code cible + mapping Betula (52 patterns)
         │
  ┌──────┴────────┐
  ▼               ▼
Frontend (React/Vue)    Backend + DB (Node/Python/Java)
         │
         ▼
  WLangage-Test-Comparator      ← tests de parité fonctionnelle
```

**Moteur RAG :** ChromaDB + 14 227 documents + embeddings E5 multilingual + BM25 hybrid  
**Recall@5 : 85%** | **Temps réponse : 50–200 ms** | **Qualité LLM : 90%** (19/21 ✅ sur qwen3-coder:480b)

---

## Commandes

```bash
make help        # aide interactive
make start       # MCP server SSE + stdio
make start-rag   # MCP server en mode SSE (HTTP) → http://localhost:8000/sse
make start-mcp   # MCP server stdio (pour VS Code)
make test        # 89 tests pytest
make dataset     # collecte données depuis votre projet
make update      # git pull + réinstallation
make clean       # supprime caches Python
```

---

## Contribuer des données

Le `wlangage-dataset-kit` extrait et anonymise automatiquement les procédures de votre projet pour enrichir le RAG :

```bash
python wlangage-dataset-kit/collect.py \
  --project "C:\MonProjet\App.wdp" \
  --anonymize
# → contribution_YYYYMMDD_App.jsonl
```

Les données collectées n'incluent jamais : mots de passe, chaînes de connexion, emails, chemins absolus.  
→ [Guide du collecteur](wlangage-dataset-kit/README.md)

---

## Fine-tuning (optionnel, post-v1)

Un notebook Colab est disponible pour fine-tuner Qwen2.5-Coder-7B sur vos données WLangage :

```
notebooks/train_qwen_wlangage_understanding_colab.ipynb
```

Le kit fonctionne à 100% sans modèle fine-tuné — avec GitHub Copilot/Models, Ollama ou tout LLM cloud.  
→ [Guide ML](docs/ml-training.md)

---

## Structure du projet

```
AiWlangage/
├── wlangage-rag/           ← Moteur RAG Python (aiwlangage-rag v2.4.0)
├── wlangage-mcp-server/    ← Serveur MCP v1.0.0
├── wlangage-agents/        ← 13 agents VS Code (.agent.md)
├── wlangage-dataset-kit/   ← Kit contribution données
├── data/processed/         ← Datasets WLangage (14 000+ docs)
├── examples/               ← Exemples WLangage
├── docs/                   ← Documentation (adr, plans, STATUS.md)
├── notebooks/              ← Notebooks fine-tuning (avancé)
├── scripts/                ← Pipeline build datasets
├── QUICKSTART.md           ← Démarrage rapide
├── GETTING_STARTED.md      ← Prise en main détaillée

## Licence

MIT — voir [LICENSE](wlangage-dataset-kit/LICENSE)

---

*Projet développé pour migrer des milliers de projets WinDev/WebDev actifs vers des stacks modernes, en préservant toute la logique métier.*
