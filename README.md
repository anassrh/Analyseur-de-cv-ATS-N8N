# 📄 Analyseur de CV basé sur Workflows n8n + Supabase + React

## 📌 Contexte du projet
Ce projet met en place un **outil d’analyse automatique de CVs** destiné aux entreprises afin de :  
- **Filtrer efficacement** les candidatures reçues en grand volume.  
- **Analyser et structurer** les informations clés contenues dans les CVs.  
- **Identifier les candidats les plus pertinents** avant un processus de recrutement.  

🎯 L’objectif : automatiser la **réception, l’extraction, la structuration et le stockage** des informations de CVs (expérience professionnelle, formation, informations personnelles, compétences, etc.) dans une base de données centralisée.

---

## 🏗️ Architecture du projet

Le système repose sur **deux workflows principaux** interconnectés dans **n8n**, et une application front-end en **React + TypeScript** reliée à une base **Supabase (Postgres)**.

---

### 1. 🔄 Workflow A : Réception et prétraitement du CV
- Récupère le CV envoyé par l’utilisateur (via upload ou formulaire).  
- Vérifie :
  - **La qualité du fichier** (lisible, non corrompu).  
  - **Le type du fichier** : PDF, DOCX, PNG, JPEG.  
- Redirige vers un **sous-workflow spécifique** en fonction du type de fichier :  
  - Workflow PDF  
  - Workflow DOCX  
  - Workflow PNG  
  - Workflow JPEG  
- Chaque sous-workflow effectue une **extraction de texte** adaptée au format du CV.  
- Une fois l’extraction terminée, le contenu textuel est renvoyé au workflow principal.  
- Les données textuelles passent ensuite par un **AI Agent** (OpenAI GPT) qui :  
  - Nettoie le contenu.  
  - Structure les informations extraites en **sections standardisées** :  
    - Expérience professionnelle  
    - Formation / Éducation  
    - Informations personnelles  
    - Compétences  

👉 Résultat : un CV brut est transformé en **données structurées**.

---

### 2. 🗂️ Workflow B : Stockage des sections
- Reçoit les données structurées depuis Workflow A.  
- Distribue les informations dans des **tables dédiées** de la base Supabase (Postgres) :  
  - `experiences_pro`  
  - `education`  
  - `personal_info`  
  - `skills`  
- Chaque table contient les données déjà normalisées pour faciliter les recherches et filtres.  

👉 Résultat : les CVs sont **indexés et stockés en base** de façon exploitable par l’entreprise.

---

### 3. 🌐 Application React + TypeScript
- Interface pour uploader les CVs (frontend en **React + Tailwind**).  
- Communication avec les workflows via **webhooks n8n**.  
- Affichage des résultats (sections extraites, score de qualité du CV, etc.).  
- Connexion directe à **Supabase** pour lire et exploiter les données stockées.  

---

## 🛠️ Technologies utilisées
- **n8n** : orchestration des workflows.  
- **OpenAI API** : structuration des données de CV.  
- **Supabase (Postgres + pgvector)** : stockage des sections extraites.  
- **React + TypeScript** : interface front-end pour l’upload et la visualisation des résultats.  
- **OCR & parsers** : pour l’extraction des données depuis PDF/DOCX/PNG/JPEG.  

---

## 🚀 Guide de démarrage

### 1. Pré-requis
- Instance n8n (Docker ou Cloud).  
- Compte OpenAI + clé API.  
- Compte Supabase avec Postgres et pgvector activé.  
- Application React (Node.js v18+).  

### 2. Variables d’environnement (`.env`)
```env
OPENAI_API_KEY=sk-xxxxxxxx
SUPABASE_URL=https://xxxx.supabase.co
SUPABASE_KEY=xxxx
3. Déploiement des workflows

Importer les fichiers JSON des workflows dans n8n.

Vérifier les connexions (OpenAI, Supabase).

4. Démarrer l’application

Lancer le workflow A → réception CV + structuration.

Vérifier que les données arrivent bien dans Workflow B → stockage.

Lancer l’application React pour tester l’upload et l’affichage.

📂 Structure du projet

/workflows
  ├── workflow_A_pretraitement.json   # Réception et vérification du CV
  ├── workflow_B_stockage.json        # Stockage des données structurées
  ├── subworkflow_pdf.json            # Extraction PDF
  ├── subworkflow_docx.json           # Extraction DOCX
  ├── subworkflow_png.json            # Extraction PNG
  ├── subworkflow_jpeg.json           # Extraction JPEG
/frontend
  ├── src/
  └── package.json

  📊 Schéma d’architecture (Mermaid)
  flowchart TD
    A[CV Upload] --> B[Workflow A - Vérification type + qualité]
    B --> C1[Subworkflow PDF]
    B --> C2[Subworkflow DOCX]
    B --> C3[Subworkflow PNG]
    B --> C4[Subworkflow JPEG]

    C1 --> D[Extraction de texte]
    C2 --> D
    C3 --> D
    C4 --> D

    D --> E[AI Agent - Structuration des sections]
    E --> F[Workflow B - Stockage dans Supabase]
    F --> G[(Postgres / Supabase DB)]

    G --> H[React + TS App]
    H --> I[Affichage résultats / filtres]
📌 Améliorations possibles

Calcul d’un score ATS pour chaque CV.

Détection automatique des compétences clés.

Génération de rapports de synthèse pour les recruteurs.

Intégration multicanal (upload via email, Slack, WhatsApp).

✨ Résultat attendu

Un analyseur de CV intelligent capable de :
✔️ Recevoir et analyser des CVs dans plusieurs formats (PDF, DOCX, PNG, JPEG).
✔️ Extraire et structurer automatiquement les informations clés.
✔️ Stocker ces données dans une base Postgres / Supabase.
✔️ Permettre aux entreprises de filtrer et analyser les candidats de manière rapide et efficace.
