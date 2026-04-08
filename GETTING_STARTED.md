# Prise en main — Migration d'un projet WinDev vers une stack moderne

> **Objectif :** Lire un projet WinDev/WebDev, comprendre sa logique métier, générer un cahier des charges et produire le code cible dans la stack de votre choix.
>
> Temps estimé pour la mise en place : **15 minutes**

---

## Ce que vous allez obtenir

À partir d'un projet WinDev existant (`*.wdp` / `*.wdw` / `*.wdc`) :

```text
Projet WinDev
     │
     ▼  @WLangage-Atlas
Cartographie JSON (tables, procédures, domaines métier)
     │
     ▼
Cahier des charges technique
     │  ├── ERD Mermaid (modèle de données)
     │  ├── User stories Gherkin (comportements métier)
     │  └── API OpenAPI 3.1 (contrats d'interface)
     │
     ▼
Code cible dans la stack choisie
     │  ├── React 18 + Node.js + PostgreSQL (+ Prisma)
     │  ├── Django 5 + PostgreSQL
     │  ├── Spring Boot 3 + Java 21
     │  └── Vue 3 + FastAPI + SQLAlchemy
     │
     ▼
Tests de parité fonctionnelle (WLangage ↔ stack cible)
```

---

## Prérequis

| Outil | Version | Vérification |
|-------|---------|--------------|
| Python | 3.10+ | `python --version` |
| Git | - | `git --version` |
| VS Code | 1.90+ | avec extension **GitHub Copilot** |
| Accès LLM | Groq (gratuit) ou Gemini | voir [Configuration LLM](#3-configuration-llm) |

> **💡 Utilisateurs Windows:** Si vous préférez une installation automatisée plutôt que manuelle, ou si vous rencontrez un blocage antivirus, consultez [README.md](README.md) ou [QUICKSTART.md](QUICKSTART.md) pour les options rapides (one-liner ou téléchargement local du script).

---

## Étape 1 — Installation

```bash
git clone https://github.com/Danalisao/AiWlangage-releases.git
cd AiWlangage

# Créer l'environnement virtuel
python -m venv .venv
.venv\Scripts\activate          # Windows
# source .venv/bin/activate     # Linux/macOS

# Installer les composants depuis la racine (RAG + MCP server)
pip install -e .
```

---

## Étape 2 — Installer les agents VS Code

**Windows (PowerShell) :**
```powershell
# Depuis la racine du repo
.\wlangage-agents\install-agents.ps1
```

**macOS / Linux (bash) :**
```bash
# Depuis la racine du repo
bash wlangage-agents/install-agents.sh
```

Ce script copie les 13 fichiers `.agent.md` dans votre dossier d'instructions VS Code et active les paramètres nécessaires (`chat.agent.enabled`, `chat.subagent.enabled`).
Seul **WLangage-Atlas** apparaîtra dans le picker Copilot — les 12 sous-agents sont invoqués automatiquement.

**Options disponibles :**
| Option | Effet |
|--------|-------|
| `--insiders` / `-Insiders` | VS Code Insiders |
| `--project .` / `-Project .` | Dossier `.github/agents/` du projet courant |
| `--uninstall` / `-Uninstall` | Supprimer les agents installés |

**Vérification :** Ouvrez Copilot Chat (mode Agent `@`) : vous devriez voir `WLangage-Atlas` dans la liste.

---

## Étape 3 — Configuration LLM

Copiez le fichier d'exemple et renseignez au moins une clé API :

```bash
copy wlangage-rag\rag\.env.example wlangage-rag\rag\.env
```

Éditez `wlangage-rag\rag\.env` :

```ini
# Option A — Groq (gratuit, rapide)
GROQ_API_KEY=gsk_votreclé

# Option B — Gemini
GEMINI_API_KEY=votreclé

# Option C — Ollama local (cloud ou modèle fine-tuné)
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3-coder:480b-cloud   # 90% qualité RAG
# OLLAMA_MODEL=qwen-wlang             # modèle fine-tuné local (Phase H)

# Modèle utilisé par défaut
LLM_BACKEND=ollama                   # groq | gemini | ollama | openai | anthropic | copilot | llamacpp | lmstudio
```

**8 backends LLM supportés :** `ollama`, `lmstudio`, `gemini`, `groq`, `llamacpp`, `copilot`, `openai`, `anthropic`

> **Sans clé LLM :** les outils `wl_explain_procedure`, `wl_to_pseudocode` et `wl_migration_guidance` retourneront une erreur. Les outils de scan et de lecture (`wl_scan_project`, `wl_read_procedure`) fonctionnent sans LLM.
>
> **Modèle local (optionnel, post-v1) :** Un modèle WLangage fine-tuné (GGUF ~4.5 GB via Ollama) est en cours de préparation mais n'est pas prioritaire. La chaîne agentique fonctionne parfaitement avec Groq/Gemini/GPT-5 dès maintenant.

---

## Étape 4 — Démarrer le serveur MCP

Le serveur MCP se lance **automatiquement via VS Code** grâce au fichier `.vscode/mcp.json` fourni dans le dépôt.

**Démarrage manuel si nécessaire :**
```
Ctrl+Shift+P → MCP: Restart Server → wlangage-rag
```

**Vérification :** Dans Copilot Chat, tapez :
```
@WLangage-Atlas donne-moi ton statut
```
Atlas doit répondre en listant les outils MCP disponibles.

---

## Étape 5 — Migrer votre premier projet WinDev

### Commande simple (recommandée)

Dans Copilot Chat (mode Agent), tapez :

```
@WLangage-Atlas migre le projet C:\MonProjet\MonApplication.wdp vers react-node-postgres
```

Atlas orchestrera automatiquement la chaîne complète :

```
WLangage-Analyst      → scan + cartographie du projet
WLangage-Spec-Writer  → ERD + Gherkin + OpenAPI
Migration-Director    → code cible React/Node/Postgres
WLangage-Sisyphus     → implémentation backend
WLangage-Test-Comparator → tests de parité
```

### Stacks disponibles

| Stack | Commande |
|-------|----------|
| React 18 + Node.js + PostgreSQL + Prisma | `vers react-node-postgres` |
| Django 5 + PostgreSQL | `vers django` |
| Spring Boot 3 + Java 21 + JPA | `vers spring` |
| Vue 3 + FastAPI + SQLAlchemy | `vers vue-fastapi` |

---

## Étape 6 — Analyse pas à pas (optionnel)

Vous pouvez aussi orchestrer chaque étape manuellement pour plus de contrôle.

### 6a. Scanner le projet

```
@WLangage-Atlas scanne le projet C:\MonProjet\MonApplication.wdp
```

Résultat : un `ProjectDeepIndex` JSON avec :
- Tables HFSQL et leurs champs
- Procédures et leurs dépendances
- Domaines métier identifiés automatiquement

### 6b. Lire une procédure spécifique

```
@WLangage-Atlas lis la procédure GestionCommande dans C:\MonProjet\Commandes.wdc
```

### 6c. Comprendre la logique métier

```
@WLangage-Atlas explique la procédure GestionStock :

PROCEDURE GestionStock(nIdArticle est un entier)
    HLitRecherche(Article, IdArticle, nIdArticle)
    SI Article.Stock <= Article.SeuilAlerte ALORS
        EnvoiAlerte(nIdArticle, Article.Stock)
    FIN
```

### 6d. Traduire en pseudocode

```
@WLangage-Atlas traduis en pseudocode TypeScript :

PROCEDURE CalculTVA(rMontantHT est un réel, nTaux est un entier)
    rTVA est un réel = rMontantHT * nTaux / 100
    RENVOYER rMontantHT + rTVA
```

### 6e. Obtenir le guide de migration

```
@WLangage-Atlas donne le guide de migration vers django pour :

PROCEDURE SauvegardeClient(sNom, sEmail, sTelephone)
    HAjoute(Client)
    Client.Nom = sNom
    Client.Email = sEmail
    Client.Telephone = sTelephone
    HModifie(Client)
```

---

## Contribuer des exemples WLangage — Dataset Kit

Si vous avez un projet WinDev réel, vous pouvez contribuer vos procédures au dataset open source pour améliorer le modèle.

**Vos données restent sur votre machine.** Rien n'est envoyé sans votre accord.

```bash
cd wlangage-dataset-kit
pip install -r requirements.txt

# Scanner votre projet et générer les exemples
python collect.py --project "C:\MonProjet\MonApplication.wdp" --anonymize

# → génère contribution.jsonl
# Soumettez-le via une Pull Request sur GitHub
```

Voir [wlangage-dataset-kit/README.md](wlangage-dataset-kit/README.md) pour le guide complet.

---

## Dépannage

### Le serveur MCP ne démarre pas

```powershell
# Vérifier que le package est installé
python -c "import rag; print(rag.__version__)"

# Relancer manuellement
cd wlangage-mcp-server
python server.py --mode stdio
```

### Les outils MCP retournent "RAG non initialisé"

Le moteur RAG doit indexer les documents au premier démarrage (~30 secondes). Attendez et réessayez.

Si `chroma_db/` est vide, lancez la réindexation :
```bash
python wlangage-rag\rag\scripts\reindex_database.py
```

### Les résultats de migration sont de mauvaise qualité

1. Vérifiez que `GROQ_API_KEY` ou `GEMINI_API_KEY` est configurée dans `wlangage-rag\rag\.env`
> 2. Essayez un modèle plus puissant via Ollama cloud : dans `.env`, changez `LLM_BACKEND=ollama` et `OLLAMA_MODEL=minimax-m2:cloud` (nécessite `OLLAMA_API_KEY`)
3. Fournissez plus de contexte à Atlas : nommez le domaine métier (`@WLangage-Atlas migre le module de gestion des commandes...`)

### @WLangage-Atlas n'apparaît pas dans le picker Copilot

```powershell
# Windows — Réinstaller les agents
.\wlangage-agents\install-agents.ps1
```

```bash
# macOS / Linux — Réinstaller les agents
bash wlangage-agents/install-agents.sh
```

```
# Puis recharger VS Code
Ctrl+Shift+P → Developer: Reload Window
```

---

## Architecture rapide

```text
VS Code Copilot
    │  (mode Agent @WLangage-Atlas)
    ▼
wlangage-agents/           ← 13 agents déclaratifs .agent.md
    │  (appels MCP)
    ▼
wlangage-mcp-server/       ← serveur stdio (22+ outils)
    │  (import aiwlangage-rag)
    ▼
wlangage-rag/rag/          ← moteur RAG Python
    ├── core/              ← Cartographer, SpecGenerator, StackAdapters, Betula
    ├── engine/            ← LLM unifié (Groq/Gemini/Ollama)
    ├── search/            ← BM25 + embeddings E5 (ChromaDB)
    └── data/              ← 11 106 docs WLangage indexés
```

---

## Liens utiles

- [README principal](README.md) — vue d'ensemble du projet
- [wlangage-agents/README.md](wlangage-agents/README.md) — détail des 9 agents
- [wlangage-mcp-server/README.md](wlangage-mcp-server/README.md) — liste des 14 outils MCP
- [docs/STATUS.md](docs/STATUS.md) — état détaillé de tous les modules
- [wlangage-dataset-kit/README.md](wlangage-dataset-kit/README.md) — guide contributeur
- [CLAUDE.md](CLAUDE.md) — conventions dataset (pour les contributeurs avancés)
