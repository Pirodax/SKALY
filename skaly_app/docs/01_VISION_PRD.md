# SKALY - Vision & Product Requirements Document

## Vision

**SKALY** est une plateforme de gestion financière tout-en-un conçue spécifiquement pour les indépendants, freelances et micro-entrepreneurs. Contrairement aux applications généralistes comme Finary ou Bankin', SKALY comprend la réalité des personnes qui jonglent entre revenus personnels et revenus d'activité, et qui gèrent parfois plusieurs business en parallèle.

## Mission

Donner aux indépendants une vision claire et complète de leur santé financière en séparant intelligemment leur vie personnelle et professionnelle, tout en les aidant à maximiser leur capacité d'investissement et à construire leur patrimoine.

## Problème Adressé

Les indépendants font face à des défis financiers uniques que les apps classiques ne résolvent pas:

### 1. Confusion Personnel/Professionnel
**Problème**: Difficile de savoir si son business est vraiment rentable quand tout est mélangé sur le même compte.
**Solution SKALY**: Séparation automatique des flux personnels et professionnels avec catégorisation intelligente.

### 2. Multi-Activités
**Problème**: Un freelance peut avoir du consulting + une boutique Etsy + des revenus YouTube. Aucune app ne permet de tracker ça séparément.
**Solution SKALY**: Support natif multi-business avec P&L séparé par activité.

### 3. Capacité d'Investissement Floue
**Problème**: Entre les charges pro, les impôts à provisionner et les dépenses perso, impossible de savoir combien on peut réellement mettre de côté.
**Solution SKALY**: Calcul automatique de la capacité d'investissement après toutes les provisions.

### 4. Vision Patrimoine Inexistante
**Problème**: Les indépendants construisent leur patrimoine différemment (moins de CDI = moins d'accès au crédit = plus d'épargne liquide) mais aucun outil ne les accompagne.
**Solution SKALY**: Vue consolidée du patrimoine avec projections adaptées aux indépendants.

## Target Audience

### Persona Primaire: Marie - Freelance Designer
- **Âge**: 32 ans
- **Statut**: Micro-entreprise depuis 3 ans
- **Revenus**: 3000-5000€/mois (variable)
- **Activités**: Design graphique (70%) + vente de templates Figma (30%)
- **Pain Points**:
  - Ne sait jamais combien elle peut vraiment se payer
  - Stresse à chaque échéance URSSAF
  - Hésite à investir par peur de manquer de trésorerie
- **Goals**:
  - Constituer un apport immobilier
  - Avoir un matelas de sécurité de 6 mois
  - Investir régulièrement sans stress

### Persona Secondaire: Thomas - Multi-Entrepreneur
- **Âge**: 28 ans
- **Statut**: SASU + Micro-entreprise
- **Revenus**: 6000-12000€/mois (très variable)
- **Activités**: Dev SaaS (SASU) + Consulting (ME) + Formations en ligne
- **Pain Points**:
  - Gère 3 comptabilités différentes
  - Ne sait pas quelle activité est vraiment rentable
  - Perd du temps à consolider manuellement
- **Goals**:
  - Vue globale de toutes ses activités
  - Optimiser ses revenus en identifiant les activités les plus rentables
  - Automatiser au maximum

### Persona Tertiaire: Sophie - Transition Salarié → Freelance
- **Âge**: 35 ans
- **Statut**: Salariée à mi-temps + Freelance
- **Revenus**: 1800€ salaire + 1500-3000€ freelance
- **Activités**: Consulting RH (freelance) + Salariat (mi-temps)
- **Pain Points**:
  - Difficile de comparer avec son ancien salaire
  - Incertitude sur la viabilité du freelance
  - Ne sait pas quand passer 100% freelance
- **Goals**:
  - Sécuriser la transition
  - Valider que le freelance est viable
  - Maintenir son niveau de vie

## Core Features

### MVP (Phase 1)

#### 1. Overview Dashboard
- Solde personnel mensuel
- Solde business global
- Capacité d'investissement du mois
- Patrimoine total
- Graphique d'évolution sur 12 mois
- Alertes intelligentes
- Transactions récentes

#### 2. Personal Finance Management
- Catégorisation des revenus personnels
- Suivi des dépenses personnelles
- Gestion des abonnements récurrents
- Budget par catégorie avec alertes
- Indicateurs: revenus moyens, dépenses moyennes, taux d'épargne

#### 3. Business Tracking (Single Business)
- Informations générales du business
- Suivi CA et revenus
- Dépenses business
- Calcul automatique des cotisations URSSAF
- Provision impôt sur le revenu
- Tableau de bord avec métriques clés (CA, charges, bénéfice net)

#### 4. Investment Capacity Calculator
- Calcul automatique basé sur les revenus et dépenses
- Affichage visuel de la capacité disponible
- Répartition par objectifs d'investissement
- Alertes si sous-épargne ou sur-épargne

#### 5. Basic Net Worth Tracking
- Épargne liquide (comptes bancaires)
- Investissements basiques (PEA, Assurance-vie)
- Calcul patrimoine net (actifs - passifs)
- Graphique d'évolution

### Phase 2 Features

#### 6. Multi-Business Support
- Création de plusieurs "business"
- P&L séparé par business
- Comparaison entre business
- Consolidation automatique

#### 7. Advanced Investment Features
- Objectifs d'épargne avec deadlines
- Simulation de projections
- Recommandations de répartition
- Suivi performance par support

#### 8. Enhanced Net Worth
- Immobilier (résidence principale + locatif)
- Véhicules avec décote
- Objets de valeur
- Parts de société
- Crédits et dettes détaillés

#### 9. Recurring Transactions & Smart Categorization
- Détection automatique des transactions récurrentes
- Suggestions de catégorisation
- Règles personnalisées
- Import automatique (via connexion bancaire)

### Phase 3 Features

#### 10. Bank Connection Integration
- Connexion automatique aux comptes bancaires
- Synchronisation des transactions
- Mise à jour en temps réel

#### 11. Advanced Analytics
- Tendances et insights
- Comparaison avec moyennes par secteur
- Prédictions de revenus
- Analyse de rentabilité par client/projet

#### 12. Tax Optimization
- Simulation de différents statuts juridiques
- Recommandations d'optimisation fiscale
- Export pour comptable
- Rappels fiscaux (déclarations, échéances)

#### 13. Collaborative Features
- Partage avec comptable (read-only)
- Export vers outils comptables
- API pour intégrations

## Différenciation vs Concurrence

| Fonctionnalité | Finary | Bankin' | SKALY |
|----------------|--------|---------|-------|
| Suivi comptes perso | ✅ | ✅ | ✅ |
| Séparation perso/business | ❌ | ❌ | ✅ |
| Multi-business | ❌ | ❌ | ✅ |
| Calcul cotisations auto | ❌ | ❌ | ✅ |
| Capacité investissement | Basique | ❌ | ✅ Avancé |
| Projections patrimoine | ✅ | ❌ | ✅ |
| Ciblé indépendants | ❌ | ❌ | ✅ |
| Support multi-activités | ❌ | ❌ | ✅ |
| Calcul provisions fiscales | ❌ | ❌ | ✅ |

## Success Metrics

### User Acquisition
- 1000 utilisateurs inscrits dans les 3 premiers mois (MVP)
- 10% de conversion visiteur → inscription
- CAC < 20€

### Engagement
- 60% des utilisateurs actifs hebdomadairement
- 3+ sessions par semaine en moyenne
- Temps moyen par session: 5-10 minutes
- 80% des utilisateurs ajoutent au moins 1 business

### Retention
- 70% de rétention à J+7
- 50% de rétention à M+1
- 30% de rétention à M+3

### Conversion (Freemium → Pro)
- 5% de conversion dans les 30 premiers jours
- 15% de conversion à M+3
- Churn rate < 5% mensuel

### Product Metrics
- 90% des transactions catégorisées (auto ou manuel)
- Temps moyen pour catégoriser une transaction: < 10s
- 80% des utilisateurs configurent leur capacité d'investissement
- NPS > 40

## Business Model

### Freemium Model

#### Free Tier
- 1 business maximum
- Fonctionnalités de base:
  - Overview dashboard
  - Personal finance tracking
  - Single business tracking
  - Basic investment capacity
  - Basic net worth
- Limite: 100 transactions/mois
- Export limité (PDF uniquement)

#### Pro Tier - 9€/mois
- Multi-business illimité
- Fonctionnalités avancées:
  - Projections et simulations
  - Objectifs d'épargne avec suivi
  - Analytics avancés
  - Alertes personnalisées illimitées
- Export illimité (CSV, PDF, Excel)
- Support prioritaire
- Transactions illimitées

#### Business Tier - 19€/mois
- Tout le Pro +
- Intégration comptable
- API access
- Partage avec comptable
- Multi-utilisateurs (pour associés)
- Support dédié
- Personnalisation avancée

### Revenue Projections

**Hypothèses conservatrices:**
- 1000 users après 6 mois
- 10% conversion Free → Pro (100 users × 9€ = 900€/mois)
- 2% conversion Free → Business (20 users × 19€ = 380€/mois)
- **MRR après 6 mois: ~1280€**

**Objectif 12 mois:**
- 5000 users
- 15% conversion Pro (750 × 9€ = 6750€)
- 5% conversion Business (250 × 19€ = 4750€)
- **MRR après 12 mois: ~11500€**

### Alternative Revenue Streams (Future)
- Affiliate partnerships (banques, assurances)
- Premium templates et workflows
- Formation/coaching financier

## Design Principles

### 1. Clarity Over Complexity
- Vue d'ensemble immédiate
- Hiérarchie visuelle claire
- Pas de jargon technique

### 2. Trust Through Transparency
- Formules de calcul visibles
- Explications claires des chiffres
- Pas de "boîte noire"

### 3. Actionable Insights
- Pas que des chiffres, des recommandations
- Alertes contextuelles
- Prochaines actions suggérées

### 4. Mobile-First
- Responsive design
- Touch-friendly
- Rapide sur mobile

### 5. Progressive Disclosure
- Essentials first
- Advanced features accessible mais pas imposées
- Onboarding progressif

## Technical Constraints

### Performance
- Page load < 2s
- Time to Interactive < 3s
- Lighthouse score > 90

### Security
- Row Level Security (RLS) sur toutes les tables
- Encryption at rest (Supabase)
- HTTPS only
- GDPR compliant

### Scalability
- Support 100k utilisateurs
- 10M transactions
- Real-time updates (< 1s latency)

### Browser Support
- Chrome, Firefox, Safari (2 dernières versions)
- iOS Safari
- Android Chrome
- No IE support

## Out of Scope (MVP)

- Connexion bancaire automatique (Phase 3)
- Multi-devises
- Comptabilité complète (bilan, compte de résultat officiel)
- Facturation
- Gestion de projet / time tracking
- Application mobile native (PWA d'abord)
- AI/ML features
- Social features

## Risks & Mitigations

### Risk 1: Complexité perçue
**Mitigation**: Onboarding progressif, tooltips, exemples pré-remplis

### Risk 2: Concurrence
**Mitigation**: Focus niche (indépendants), différenciation claire, communauté

### Risk 3: Adoption
**Mitigation**: Marketing de contenu, SEO, partenariats avec communautés freelance

### Risk 4: Précision des calculs
**Mitigation**: Validation par experts-comptables, disclaimers légaux, mode "estimation"

### Risk 5: Sécurité des données
**Mitigation**: Supabase (SOC 2 compliant), audits sécurité, encryption, bug bounty

## Next Steps

1. ✅ Finaliser la documentation
2. ⏳ Implémenter le data model (Supabase)
3. ⏳ Développer l'authentification
4. ⏳ Implémenter l'Overview Dashboard
5. ⏳ Implémenter Personal Finance section
6. ⏳ Implémenter Single Business tracking
7. ⏳ Implémenter Investment Capacity calculator
8. ⏳ Testing & refinement
9. ⏳ Beta launch

---

**Last Updated**: 2025-12-25
**Status**: In Development
**Version**: 0.1.0
