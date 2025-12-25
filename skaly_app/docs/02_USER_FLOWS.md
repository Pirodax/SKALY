# SKALY - User Flows & User Stories

## User Personas

### Persona 1: Marie - Freelance Designer
**Background**: 32 ans, micro-entreprise depuis 3 ans, 3-5k€/mois
**Tech Savviness**: Moyenne
**Financial Literacy**: Moyenne
**Primary Goal**: Constituer un apport immobilier
**Pain Point**: Ne sait jamais combien elle peut vraiment se payer

### Persona 2: Thomas - Multi-Entrepreneur
**Background**: 28 ans, SASU + ME, 6-12k€/mois
**Tech Savviness**: Élevée
**Financial Literacy**: Élevée
**Primary Goal**: Optimiser ses revenus entre activités
**Pain Point**: Gestion de 3 comptabilités différentes

### Persona 3: Sophie - Transition Salarié/Freelance
**Background**: 35 ans, mi-temps salarié + freelance, 3-5k€/mois
**Tech Savviness**: Moyenne
**Financial Literacy**: Faible-Moyenne
**Primary Goal**: Valider la viabilité du freelance
**Pain Point**: Incertitude financière sur la transition

## Core User Flows

### Flow 1: First-Time User Onboarding

```
[Landing Page]
    ↓
[Sign Up with Email/Google]
    ↓
[Welcome Screen] "Bienvenue sur SKALY"
    ↓
[Profile Setup]
  - Prénom
  - Statut (salarié/freelance/entrepreneur/mixte)
  - Objectif principal (dropdown)
    ↓
[Quick Setup Choice]
  Option A: "Configuration rapide (5 min)"
  Option B: "Configuration complète (15 min)"
    ↓
──────────────────────────────────────
Option A (Quick):                     │
    ↓                                 │
[Personal Income Setup]               │
  - Revenus mensuels moyens: ___€     │
  - Dépenses mensuelles moyennes: ___€│
    ↓                                 │
[Business Setup] (if applicable)      │
  - Nom du business: ___              │
  - CA mensuel moyen: ___€            │
  - Charges mensuelles: ___€          │
  - Statut juridique: [dropdown]      │
    ↓                                 │
[Success] "Votre compte est prêt!"    │
    ↓                                 │
[Dashboard] → First view              │
──────────────────────────────────────
Option B (Complete):
    ↓
[Guided Multi-Step Setup]
  Step 1: Revenus personnels
  Step 2: Dépenses personnelles
  Step 3: Business (si applicable)
  Step 4: Patrimoine initial
  Step 5: Objectifs d'investissement
    ↓
[Dashboard] → Fully configured
```

**Success Criteria:**
- < 5 min pour option A
- < 15 min pour option B
- 90% completion rate
- Clear progress indicator

---

### Flow 2: Adding a Transaction

```
[Dashboard] ou [Personal/Business Page]
    ↓
[Click "+" Button] (fixed bottom-right)
    ↓
[Transaction Modal]
  ┌─────────────────────────────┐
  │ Ajouter une transaction     │
  ├─────────────────────────────┤
  │ Type: ◉ Dépense ○ Revenu   │
  │ Montant: ___€               │
  │ Catégorie: [Auto-suggest]   │
  │ Date: [Date picker]         │
  │ Description: ___            │
  │ Contexte: ◉ Perso ○ Pro     │
  │ Business: [dropdown si Pro] │
  │                             │
  │ [Annuler] [Ajouter]        │
  └─────────────────────────────┘
    ↓
[Transaction Added]
    ↓
[Toast notification] "Transaction ajoutée ✓"
    ↓
[Dashboard/Page Updated] Real-time
```

**Smart Features:**
- Auto-suggest category based on description
- Remember last used business
- Detect recurring patterns (e.g., "Netflix" → "Abonnements")

**Edge Cases:**
- Duplicate detection: "Transaction similaire trouvée, confirmer?"
- Large amount warning: "Montant inhabituel, vérifier?"

---

### Flow 3: Creating a New Business

```
[Business Section]
    ↓
[Click "Créer un business"]
    ↓
[Business Creation Form]
  ┌──────────────────────────────────┐
  │ Nouveau Business                 │
  ├──────────────────────────────────┤
  │ Nom: ___                         │
  │ Type: [dropdown]                 │
  │   - Freelance/Consulting         │
  │   - E-commerce                   │
  │   - SaaS/Produit digital         │
  │   - Location                     │
  │   - Autre                        │
  │                                  │
  │ Statut juridique: [dropdown]     │
  │   - Micro-entreprise             │
  │   - SASU/EURL                    │
  │   - SAS/SARL                     │
  │   - Profession libérale          │
  │                                  │
  │ Date de création: [date]         │
  │                                  │
  │ Taux cotisations URSSAF: ___%    │
  │ (auto-rempli selon statut)       │
  │                                  │
  │ [Annuler] [Créer]               │
  └──────────────────────────────────┘
    ↓
[Business Created]
    ↓
[Business Dashboard] Affichage immédiat
    ↓
[Suggestion Modal]
  "Voulez-vous ajouter vos premières transactions?"
  [Plus tard] [Ajouter des transactions]
```

**Validation:**
- Nom unique par utilisateur
- Taux URSSAF cohérent avec statut

---

### Flow 4: Checking Investment Capacity

```
[Dashboard]
    ↓
[View "Capacité d'investissement" Card]
  ┌──────────────────────────────────┐
  │ Capacité d'investissement        │
  │ 850€ ce mois                     │
  │                                  │
  │ [Voir le détail] →               │
  └──────────────────────────────────┘
    ↓
[Click "Voir le détail"]
    ↓
[Investment Section]
    ↓
[Detailed Breakdown]
  ┌──────────────────────────────────┐
  │ Calcul de la capacité            │
  ├──────────────────────────────────┤
  │ Revenus personnels:    2000€ ✓   │
  │ Bénéfices business:    2800€ ✓   │
  │ Total revenus:         4800€     │
  │                                  │
  │ Dépenses personnelles: -2500€    │
  │ Provision sécurité:    -1450€    │
  │                                  │
  │ Capacité restante:     850€      │
  ├──────────────────────────────────┤
  │ Répartition actuelle             │
  │ ████░░░░░░ Épargne (20%) - 170€  │
  │ ███████░░░ PEA (40%) - 340€      │
  │ █████░░░░░ AV (25%) - 212€       │
  │ ██░░░░░░░░ Crypto (10%) - 85€    │
  │ █░░░░░░░░░ Libre (5%) - 43€      │
  │                                  │
  │ [Modifier la répartition]        │
  └──────────────────────────────────┘
    ↓
[Adjust if needed]
    ↓
[Save Changes] → Update dashboard
```

**Real-time Updates:**
- Recalcul automatique à chaque nouvelle transaction
- Notification si capacité change significativement (+/- 20%)

---

### Flow 5: Monthly Review

```
[Email Notification]
"Votre résumé mensuel SKALY est prêt 📊"
    ↓
[Click email link]
    ↓
[Monthly Summary Page]
  ┌──────────────────────────────────┐
  │ Résumé Décembre 2025             │
  ├──────────────────────────────────┤
  │ 🎯 Highlights                    │
  │ • Meilleur mois de l'année!      │
  │ • +15% revenus vs Nov            │
  │ • Objectif épargne atteint ✓     │
  │                                  │
  │ 💰 Finances                      │
  │ Revenus total: 4800€             │
  │ Dépenses total: 3200€            │
  │ Épargné: 1600€                   │
  │                                  │
  │ 📊 Business                      │
  │ [Graphique par business]         │
  │                                  │
  │ 🎯 Objectifs                     │
  │ Apport immobilier: 45% ████░░░░  │
  │ Matelas sécurité: 100% ████████  │
  │                                  │
  │ [Télécharger PDF]                │
  └──────────────────────────────────┘
```

---

### Flow 6: Budget Alert

```
[User adds transaction that exceeds budget]
    ↓
[Real-time Budget Check]
    ↓
[Alert Modal]
  ┌──────────────────────────────────┐
  │ ⚠️ Budget dépassé                │
  ├──────────────────────────────────┤
  │ Votre budget "Loisirs" a été     │
  │ dépassé ce mois.                 │
  │                                  │
  │ Budget: 200€                     │
  │ Dépensé: 235€                    │
  │ Dépassement: +35€ (17%)          │
  │                                  │
  │ [Voir les dépenses]              │
  │ [Ajuster le budget]              │
  │ [OK, compris]                    │
  └──────────────────────────────────┘
    ↓
User chooses action:
  A) Voir les dépenses → List view
  B) Ajuster le budget → Edit modal
  C) OK → Dismiss
```

---

### Flow 7: Multi-Business Comparison

```
[Business Section]
    ↓
[Multiple Businesses Listed]
  • Freelance Design (primary)
  • Templates Figma
    ↓
[Click "Comparer mes business"]
    ↓
[Comparison View]
  ┌──────────────────────────────────────────────┐
  │ Comparaison Business                         │
  ├──────────────────────────────────────────────┤
  │          │ Freelance    │ Templates          │
  ├──────────┼──────────────┼────────────────────┤
  │ CA mois  │ 3500€        │ 800€               │
  │ Charges  │ 800€         │ 200€               │
  │ Bénéfice │ 2700€        │ 600€               │
  │ Marge    │ 77%          │ 75%                │
  │ Temps    │ 80h          │ 20h                │
  │ €/heure  │ 34€/h        │ 30€/h              │
  ├──────────┴──────────────┴────────────────────┤
  │ 💡 Insight: Votre freelance est plus rentable│
  │    mais les templates sont plus scalables    │
  └──────────────────────────────────────────────┘
```

---

## User Stories

### Epic 1: Personal Finance Management

**US-1.1**: En tant qu'utilisateur, je veux ajouter mes revenus personnels pour suivre mes entrées d'argent.
**Acceptance Criteria:**
- Formulaire avec montant, source, date
- Catégorisation automatique suggérée
- Affichage dans le dashboard

**US-1.2**: En tant qu'utilisateur, je veux catégoriser mes dépenses personnelles pour voir où va mon argent.
**Acceptance Criteria:**
- 10+ catégories prédéfinies
- Création de catégories custom
- Graphique de répartition

**US-1.3**: En tant qu'utilisateur, je veux définir un budget par catégorie pour contrôler mes dépenses.
**Acceptance Criteria:**
- Définition d'un montant max par catégorie
- Barre de progression en temps réel
- Alerte à 80% et 100%

**US-1.4**: En tant qu'utilisateur, je veux voir mes abonnements récurrents pour identifier les dépenses inutiles.
**Acceptance Criteria:**
- Détection automatique des récurrences
- Liste avec prochaine échéance
- Option "Annuler" avec rappel

---

### Epic 2: Business Tracking

**US-2.1**: En tant que freelance, je veux créer mon business pour séparer mes finances pro.
**Acceptance Criteria:**
- Formulaire simple (nom, statut, date)
- Taux cotisations auto-rempli
- Dashboard business créé

**US-2.2**: En tant qu'entrepreneur, je veux suivre mon CA par client pour identifier mes meilleurs clients.
**Acceptance Criteria:**
- Association transaction → client
- Vue par client (CA total, nb factures)
- Graphique de répartition

**US-2.3**: En tant qu'indépendant, je veux que mes cotisations URSSAF soient calculées automatiquement.
**Acceptance Criteria:**
- Calcul basé sur CA et statut juridique
- Provision affichée dans le dashboard
- Explication du calcul accessible

**US-2.4**: En tant que multi-entrepreneur, je veux comparer mes différents business.
**Acceptance Criteria:**
- Vue tableau comparatif
- Métriques: CA, charges, marge, €/heure
- Insights automatiques

---

### Epic 3: Investment Capacity

**US-3.1**: En tant qu'utilisateur, je veux connaître ma capacité d'investissement mensuelle.
**Acceptance Criteria:**
- Calcul automatique basé sur revenus/dépenses
- Affichage clair dans le dashboard
- Détail du calcul accessible

**US-3.2**: En tant qu'épargnant, je veux répartir mon épargne selon mes objectifs.
**Acceptance Criteria:**
- Définition de % par support
- Total = 100% validation
- Montants calculés automatiquement

**US-3.3**: En tant qu'utilisateur, je veux définir des objectifs d'épargne avec deadline.
**Acceptance Criteria:**
- Formulaire: nom, montant, date cible
- Calcul mensuel nécessaire
- Progression affichée

---

### Epic 4: Net Worth Tracking

**US-4.1**: En tant qu'utilisateur, je veux enregistrer mes actifs pour suivre mon patrimoine.
**Acceptance Criteria:**
- Ajout épargne, investissements, immobilier
- Valeur totale calculée
- Graphique d'évolution

**US-4.2**: En tant qu'utilisateur, je veux voir l'évolution de mon patrimoine net.
**Acceptance Criteria:**
- Graphique sur 12 mois
- Décomposition par type d'actif
- Variation en % et €

---

## Error States & Edge Cases

### Error 1: Transaction Submission Failed
**Trigger**: Network error during save
**Behavior**:
- Toast error: "Impossible d'ajouter la transaction"
- Form data preserved
- Retry button
- Offline queue (future)

### Error 2: Budget Exceeded
**Trigger**: Transaction exceeds budget
**Behavior**:
- Modal warning (voir Flow 6)
- Option to adjust budget
- Transaction still saved

### Error 3: Invalid Business Data
**Trigger**: Negative revenue, invalid tax rate
**Behavior**:
- Inline validation error
- Suggestion of valid values
- Block submission until fixed

### Error 4: Empty State - No Transactions
**Trigger**: User just created account
**Behavior**:
- Helpful illustration
- "Ajoutez votre première transaction"
- Example transaction button

### Edge Case 1: Deleted Business with Transactions
**Behavior**:
- Warning modal before deletion
- Option to reassign transactions
- Soft delete (archived) instead

### Edge Case 2: Changing Business Status (ME → SASU)
**Behavior**:
- Confirmation modal
- Recalculation of tax rates
- Historical data preserved

---

**Last Updated**: 2025-12-25
