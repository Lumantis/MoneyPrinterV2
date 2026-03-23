# MoneyPrinter V2

> ♥︎ **Sponsor** : La meilleure application de chat IA : [shiori.ai](https://www.shiori.ai). Utilisez le code **MPV2** pour 20 % de réduction.

---

> 𝕏 Suivez-moi sur X : [@DevBySami](https://x.com/DevBySami).

[![madewithlove](https://img.shields.io/badge/made_with-%E2%9D%A4-red?style=for-the-badge&labelColor=orange)](https://github.com/FujiwaraChoki/MoneyPrinterV2)
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Donate-brightgreen?logo=buymeacoffee)](https://www.buymeacoffee.com/fujicodes)
[![GitHub license](https://img.shields.io/github/license/FujiwaraChoki/MoneyPrinterV2?style=for-the-badge)](https://github.com/FujiwaraChoki/MoneyPrinterV2/blob/main/LICENSE)
[![GitHub issues](https://img.shields.io/github/issues/FujiwaraChoki/MoneyPrinterV2?style=for-the-badge)](https://github.com/FujiwaraChoki/MoneyPrinterV2/issues)
[![GitHub stars](https://img.shields.io/github/stars/FujiwaraChoki/MoneyPrinterV2?style=for-the-badge)](https://github.com/FujiwaraChoki/MoneyPrinterV2/stargazers)
[![Discord](https://img.shields.io/discord/1134848537704804432?style=for-the-badge)](https://dsc.gg/fuji-community)

**MoneyPrinterV2 (MPV2)** est un outil en ligne de commande (CLI) Python 3.12 qui automatise quatre flux de travail complets pour générer des revenus en ligne — entièrement sans intervention manuelle. Il constitue une réécriture complète de la première version, avec une architecture modulaire, un pipeline de génération de contenu piloté par l'IA, et une prise en charge native des tâches planifiées.

> **Remarque :** MPV2 nécessite Python 3.12 pour fonctionner correctement.
> Regardez la vidéo YouTube de présentation [ici](https://youtu.be/wAZ_ZSuIqfk).

---

## Fonctionnalités

- [x] **Bot Twitter/X** — génération de tweets par LLM et publication automatique via Selenium, avec planification CRON intégrée
- [x] **Automatiseur YouTube Shorts** — pipeline complet (script → voix → images IA → montage vidéo → sous-titres → upload) avec planification CRON
- [x] **Marketing d'affiliation** — scraping de produits Amazon, génération d'un pitch de vente par IA et partage automatique sur Twitter
- [x] **Prospection de commerces locaux** — scraping Google Maps, extraction d'e-mails et envoi de messages de prospection à froid par SMTP

---

## Détail des modules

### 1. Bot Twitter / X

Le bot Twitter génère du contenu texte grâce à un LLM local (Ollama) et le publie automatiquement sur X.com via Selenium. Vous n'avez pas besoin de saisir vos identifiants : le bot réutilise votre profil Firefox déjà connecté.

**Possibilités :**
- Générer et poster des tweets dans la langue de votre choix (`twitter_language`)
- Planifier des publications récurrentes avec le scheduler intégré
- Gérer plusieurs comptes simultanément grâce au système de cache JSON
- Personnaliser le ton, la longueur et le style des tweets en ajustant le prompt LLM

---

### 2. Automatiseur YouTube Shorts

C'est le module le plus complet de MPV2. Il prend en charge l'intégralité du pipeline de création de vidéos courtes, de l'idée à la publication.

**Pipeline complet :**

```
Sujet → Script (LLM) → Métadonnées (titre, description, tags)
     → Prompts d'images → Images générées par IA (Gemini)
     → Voix off (KittenTTS) → Sous-titres (Whisper / AssemblyAI)
     → Montage vidéo (MoviePy + ImageMagick) → Upload YouTube Studio (Selenium)
```

**Possibilités :**
- Générer des vidéos sur n'importe quel sujet, entièrement automatisées
- Utiliser des images 100 % générées par IA (format 9:16 optimisé pour les Shorts)
- Choisir parmi 8 voix de synthèse (Bella, Jasper, Luna, Bruno, Rosie, Hugo, Kiki, Leo)
- Générer des sous-titres localement (Whisper, sans frais d'API) ou via AssemblyAI
- Ajouter de la musique de fond (fichiers ZIP de musique configurables)
- Marquer les vidéos comme destinées aux enfants (`is_for_kids`)
- Planifier des uploads réguliers avec le CRON intégré
- Accélérer le rendu en configurant le nombre de threads (`threads`)

---

### 3. Marketing d'affiliation (Amazon + Twitter)

Ce module automatise la promotion de produits Amazon sur Twitter. Il scrape les informations d'un produit, génère un argumentaire de vente percutant grâce au LLM, et le poste directement sur votre compte X.

**Possibilités :**
- Scraper automatiquement les détails d'un produit Amazon (titre, prix, description, avis)
- Générer un pitch de vente optimisé pour Twitter avec lien d'affiliation
- Publier le contenu promotionnel sur un ou plusieurs comptes Twitter
- Archiver les produits promus dans le cache local pour éviter les doublons

---

### 4. Prospection de commerces locaux

Ce module permet de trouver des entreprises locales sur Google Maps, d'extraire leurs coordonnées e-mail, et d'envoyer des messages de prospection personnalisés en masse.

**Possibilités :**
- Scraper Google Maps pour une niche et une zone géographique données
- Extraire automatiquement les adresses e-mail des sites web des entreprises (via un binaire Go)
- Envoyer des e-mails de prospection HTML personnalisés via votre serveur SMTP
- Personnaliser l'objet et le corps du message (`{{COMPANY_NAME}}` est remplacé dynamiquement)
- Configurer un délai d'expiration pour le scraper (`scraper_timeout`)

> ⚠️ Pour utiliser ce module, vous devez installer le [langage Go](https://golang.org/) sur votre machine.

---

## Planification automatique (CRON)

MPV2 intègre un scheduler basé sur la bibliothèque Python `schedule`. Il permet de déclencher automatiquement des tâches (publication de tweets, upload de vidéos) à intervalles réguliers, sans interaction humaine. Chaque tâche planifiée lance un sous-processus isolé (`src/cron.py`), ce qui garantit la stabilité même en cas d'erreur.

---

## Installation

```bash
# Cloner le dépôt
git clone https://github.com/FujiwaraChoki/MoneyPrinterV2.git
cd MoneyPrinterV2

# Copier la configuration d'exemple et remplir les valeurs
cp config.example.json config.json

# Créer et activer un environnement virtuel
python -m venv venv

# Windows
.\venv\Scripts\activate

# Unix / macOS
source venv/bin/activate

# Installer les dépendances
pip install -r requirements.txt
```

### Configuration rapide (macOS uniquement)

```bash
# Configure automatiquement Ollama, ImageMagick et le profil Firefox
bash scripts/setup_local.sh
```

### Vérification pré-lancement

```bash
# Valide que tous les services nécessaires sont accessibles
python scripts/preflight_local.py
```

---

## Utilisation

```bash
# Lancer le menu interactif
python src/main.py
```

---

## Configuration

Toute la configuration se trouve dans `config.json` à la racine du projet. Copiez `config.example.json` comme point de départ et renseignez les valeurs selon vos besoins.

Les principales dépendances externes à configurer sont :

| Dépendance | Utilisation |
|---|---|
| **ImageMagick** | Rendu des sous-titres dans MoviePy (`imagemagick_path`) |
| **Profil Firefox** | Automatisation Selenium sans gestion de connexion (`firefox_profile`) |
| **Ollama** | Génération de texte par LLM en local (`ollama_base_url`, `ollama_model`) |
| **Clé API Gemini** | Génération d'images IA (`nanobanana2_api_key` ou variable `GEMINI_API_KEY`) |
| **Go** | Uniquement pour le scraper Google Maps (module Outreach) |
| **AssemblyAI** | Uniquement si `stt_provider` est `third_party_assemblyai` |

Consultez [docs/Configuration.md](docs/Configuration.md) pour la référence complète de toutes les options.

---

## Documentation

Toute la documentation détaillée est disponible dans le dossier [`docs/`](docs/) :

- [Configuration.md](docs/Configuration.md) — référence complète de `config.json`
- [YouTube.md](docs/YouTube.md) — guide du module YouTube Shorts
- [TwitterBot.md](docs/TwitterBot.md) — guide du bot Twitter
- [AffiliateMarketing.md](docs/AffiliateMarketing.md) — guide du module affiliation
- [Roadmap.md](docs/Roadmap.md) — fonctionnalités planifiées

---

## Scripts utilitaires

Le dossier `scripts/` contient des scripts pour accéder directement aux fonctionnalités principales sans passer par le menu interactif. Tous les scripts doivent être exécutés depuis la racine du projet :

```bash
bash scripts/upload_video.sh
```

---

## Versions communautaires

MoneyPrinter existe en plusieurs versions développées par la communauté :

- **Chinois** : [MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo)

Si vous souhaitez soumettre votre propre fork, ouvrez une issue en décrivant vos modifications.

---

## Contribution

Veuillez lire [CONTRIBUTING.md](CONTRIBUTING.md) pour les détails sur notre code de conduite et la procédure de soumission de pull requests. Consultez [docs/Roadmap.md](docs/Roadmap.md) pour la liste des fonctionnalités à implémenter.

---

## Code de conduite

Veuillez lire [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) pour les règles de notre communauté.

---

## Licence

MoneyPrinterV2 est distribué sous la licence `Affero General Public License v3.0`. Voir [LICENSE](LICENSE) pour plus d'informations.

---

## Remerciements

- [KittenTTS](https://github.com/KittenML/KittenTTS) — moteur de synthèse vocale
- [gpt4free](https://github.com/xtekky/gpt4free) — inspiration initiale

---

## Avertissement

Ce projet est fourni à des fins éducatives uniquement. L'auteur décline toute responsabilité en cas d'utilisation abusive des informations fournies. Toutes les informations sont publiées de bonne foi et à titre informatif général. L'auteur ne garantit pas l'exhaustivité, la fiabilité ou l'exactitude de ces informations. Toute action que vous entreprendrez sur la base des informations trouvées ici est strictement à vos propres risques.
