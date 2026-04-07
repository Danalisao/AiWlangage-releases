# Démarrage Rapide — AiWlangage

> Migrez votre projet WinDev/WebDev vers React, Django, Spring ou FastAPI en quelques commandes.

---

## 1. Prérequis

| Outil | Version minimale | Vérification |
|-------|-----------------|--------------|
| Python | 3.10+ | `python3 --version` |
| Git | — | `git --version` |
| VS Code | 1.90+ | `code --version` |
| GitHub Copilot | actif | Extension installée |

Un backend LLM est requis pour les analyses. Options recommandées :

- **GitHub Models** (⭐ recommandé) — token PAT gratuit [github.com/settings/tokens](https://github.com/settings/tokens) (scope `models:read`), accès GPT-4.1, Llama 4, DeepSeek-V3, Phi-4…
- **GitHub Copilot Pro/Business/Enterprise** (⭐⭐ si vous êtes abonné) — limites augmentées + modèles raisonneurs (o3-mini, o3, Grok-3, DeepSeek-R1) via GitHub Models ou Copilot Chat API
- **Ollama (local)** — `ollama serve` + `ollama pull qwen2.5-coder:7b`
- **Groq / Gemini / OpenAI / Anthropic** — clé API dans `.env`

---

## 2. Installation (2 min)

### ✅ Recommandé — Depuis la dernière release (tous les systèmes)

**Flux standard pour développeurs et utilisateurs** :

1. Allez à : [github.com/Danalisao/AiWlangage/releases/latest](https://github.com/Danalisao/AiWlangage/releases/latest)
2. Téléchargez la source zip (`.zip`) ou archive tar (`.tar.gz`) disponible
3. Extrayez-la localement
4. Lancez l'installation :

```bash
# macOS / Linux
cd chemin/vers/AiWlangage
bash install.sh

# Windows (PowerShell)
cd C:\Mes Projets\AiWlangage    # adapter le chemin
.\install.ps1
```

### Fallback — Vous avez déjà cloné le dépôt

Si vous avez déjà le dépôt localement :

```bash
cd ~/AiWlangage
./install.sh                     # ou .\install.ps1 sur Windows
```

### ⚡ Option rapide — One-liner (attention antivirus)

**macOS / Linux** :
```bash
curl -sSL https://raw.githubusercontent.com/Danalisao/AiWlangage/main/install.sh | bash
```

**Windows (PowerShell)** :
```powershell
irm https://raw.githubusercontent.com/Danalisao/AiWlangage/main/install.ps1 | iex
```

⚠️ Certains antivirus bloquent ce pattern. Si l'exécution échoue, utilisez le **flux recommandé** (release + exécution locale).

---

## 3. Configuration

Deux fichiers `.env` doivent être configurés :

### 3a. Backend RAG — `wlangage-rag/rag/.env` (obligatoire)

Cette configuration active le moteur RAG et ses outils MCP :

```bash
cd wlangage-rag/rag
nano .env      # macOS/Linux
notepad .env   # Windows
```

Choisissez **un backend LLM** parmi :

```env
# Option A — Groq (gratuit, rapide, recommandé)
LLM_BACKEND=groq
GROQ_API_KEY=gsk_votreclé

# Option B — Gemini
LLM_BACKEND=gemini
GEMINI_API_KEY=votreclé

# Option C — Ollama local (modèle fine-tuné ou cloud-compatible)
LLM_BACKEND=ollama
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=qwen3-coder:480b-cloud

# Option D — GitHub Copilot / Models
LLM_BACKEND=copilot
COPILOT_API_KEY=your_github_token_here
```

> Sans configuration LLM, les outils `wl_explain_procedure`, `wl_to_pseudocode`, `wl_migration_guidance` retourneront une erreur.

### 3b. Configuration racine — `.env` (optionnel, pour migration)

Éditez `.env` à la **racine du dépôt** :

```bash
nano .env     # macOS/Linux
notepad .env  # Windows
```

Paramètres optionnels :

```env
# Stack cible de migration (optionnel)
MIGRATION_TARGET_STACK=react-node-postgres
```

---

## 4. Démarrer le kit

### 4a. Automatique (recommandé)

Ouvrez VS Code dans le dossier `AiWlangage` :

```bash
code .
```

Le serveur MCP démarre automatiquement via `.vscode/mcp.json` fourni dans le dépôt.

### 4b. Manuel si nécessaire

```bash
make start-rag   # MCP en mode SSE (HTTP) → http://localhost:8000/sse
make start-mcp   # MCP en mode stdio (pour VS Code)
```

---

## 5. Migrer un projet WinDev

Dans GitHub Copilot Chat (mode Agent), invoquez `@WLangage-Atlas` :

```
@WLangage-Atlas analyse mon projet C:\MonProjet\MonApplication.wdp
```

Atlas orchestre automatiquement la chaîne complète :

```
Analyst → cartographie les procédures + tables HFSQL
    ↓
Spec-Writer → génère ERD Mermaid + user stories Gherkin + API OpenAPI 3.1
    ↓
Migration-Director → code React/Django/Spring/Vue cible
    ↓
Test-Comparator → tests de parité fonctionnelle
```

---

## 6. Commandes utiles

```bash
make help        # liste toutes les commandes
make start       # lance MCP SSE + MCP stdio
make start-rag   # MCP en mode SSE (HTTP) → http://localhost:8000/sse
make start-mcp   # MCP en mode stdio (pour VS Code)
make test        # lance les 89 tests (pytest)
make update      # git pull + réinstallation
make dataset     # collecte des données depuis votre projet
make clean       # supprime les caches Python
```

---

## 7. Stacks cibles disponibles (optionnel)

| Valeur `MIGRATION_TARGET_STACK` | Stack |
|---------------------------------|-------|
| `react-node-postgres` | React 18 TypeScript + Node.js + PostgreSQL + Prisma |
| `django` | Django 5 + PostgreSQL + Django ORM |
| `spring` | Spring Boot 3 + Java 21 + Spring Data JPA |
| `vue-fastapi` | Vue 3 TypeScript + FastAPI + SQLAlchemy |

---

## 8. Structure du kit

```
AiWlangage/
├── wlangage-rag/           ← Moteur RAG (pip install -e .)
├── wlangage-mcp-server/    ← 22 outils MCP pour VS Code
├── wlangage-agents/        ← 13 agents VS Code Copilot
├── wlangage-dataset-kit/   ← Collecteur de contributions
├── data/processed/         ← 14 000+ docs WLangage indexés
├── examples/               ← Exemples WLangage (.wl)
├── install.sh              ← Installation macOS/Linux
├── install.ps1             ← Installation Windows
├── Makefile                ← Commandes courantes
└── .env.example            ← Template de configuration
```

---

## Problèmes courants

**`pip install -e .` échoue** → vérifiez que vous êtes dans le dossier `AiWlangage/` (pas dans un sous-dossier)

**Le MCP server ne démarre pas** → le dépôt fournit `.vscode/mcp.json` ; s'il manque, réinstallez avec `./install.sh` (ou `.\install.ps1` sur Windows). Puis `Ctrl+Shift+P` → `MCP: Restart Server`

**Atlas ne répond pas** → vérifiez que le backend LLM est actif (`COPILOT_API_KEY` dans `.env`, ou `ollama list` si vous utilisez Ollama)

**Erreur d'embeddings** → le premier démarrage télécharge le modèle E5 (~550 MB), patientez 2-3 min
