<p align="center">
  <a href="https://ag.tavardt.com/fr/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# OWASP LLM01 Guardrails Template et Blueprint de Sécurité pour IA

[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/fr/evaluateur-robustesse-llm/)
[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

***

Middleware open source et blueprint de sécurité dédié à l'atténuation des injections de prompt (OWASP LLM01), de l'extraction des instructions système et des jailbreaks adverses, développé par **TAVARDT**, en conformité rigoureuse avec les recommandations IA de la CNIL, le RGPD et l'AI Act européen.

Les grands modèles de langage traitent indifféremment les consignes de sécurité et les données saisies par l'utilisateur au sein du même espace de contexte. En l'absence de filtrage applicatif en entrée et de contrôle de sortie par jetons canaris, les agents autonomes et les chaînes RAG demeurent vulnérables au détournement de contexte et à la fuite d'identifiants.

***

## Fonctionnalités Clés et Architecture Défensive

* **Assainissement Déterministe en Entrée**: Analyse les invites utilisateur via des expressions régulières compilées afin d'intercepter les contournements de consignes et l'altération de balises avant transmission au modèle.
* **Vérification Cryptographique par Jeton Canari**: Injecte un jeton imprévisible (`TAVARDT_CANARY_<hex16>`) via le module `secrets` pour identifier et stopper toute session tentant d'extraire les directives système.
* **Couverture Complète des Risques Applicatifs**: Neutralise les menaces catégorisées sous OWASP-LLM01 (Injection de Prompt), OWASP-LLM06 (Divulgation d'Informations Sensibles) et OWASP-LLM07 (Conception de Plugins Non Sécurisée).
* **Zéro Dépendance Externe**: Fonctionne exclusivement avec la bibliothèque standard Python, garantissant un délai d'exécution inférieur à 2 millisecondes sans appel réseau distant.
* **Architecture d'Intégration Modulaire**: Encapsule aisément les interactions avec n'importe quel fournisseur de modèle (OpenAI, Anthropic, Google Gemini, modèles Llama hébergés) dans les agents et systèmes RAG.

***

## Vecteurs de Risque et Impact Opérationnel

Exploiter des modèles fondamentaux en environnement opérationnel sans barrières de sécurité expose les ressources informatiques et les données confidentielles à des attaques ciblées. La solution neutralise quatre vecteurs majeurs :

* **Injection Directe de Prompt (OWASP LLM01)**: Commandes hostiles ordonnant l'abandon des instructions initiales (`ignore previous instructions`) pour contraindre le modèle à des actions non autorisées.
* **Exfiltration d'Instructions Système (OWASP LLM06)**: Requêtes conçues pour inciter le modèle à reproduire ses règles métier et ses directives de sécurité internes.
* **Contournement de Délimiteurs et Jailbreak par Jeu de Rôle**: Manipulation de balises markdown ou XML pour forcer le basculement du modèle en mode développeur ou sans filtre.
* **Injection Indirecte via Banques Documentaires**: Instructions malveillantes dissimulées dans des documents tiers ingérés par des bases vectorielles au sein de flux RAG.

***

## Installation et Utilisation CLI

Intégrez le middleware de sécurité dans votre code Python ou évaluez son fonctionnement avec l'exemple de démarrage rapide fourni :

```bash
# Cloner le dépôt officiel
git clone https://github.com/TAVARDT-France/owasp-llm-guardrails-template.git

# Accéder au répertoire du projet
cd owasp-llm-guardrails-template

# Exécuter le script de validation rapide
python examples/quickstart.py
```

### Python Integration Example

Protégez vos appels d'inférence avec des contrôles déterministes en entrée et en sortie :

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Ingress Validation (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Input blocked due to suspicious adversarial pattern: {error}")

# 2. Inject Canary Token into System Prompt
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Egress Validation (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Critical alert: System prompt leak detected via canary token!")
```

***

## Foire Aux Questions Techniques

### Quels vecteurs de vulnérabilité le modèle de guardrails OWASP LLM neutralise-t-il dans les flux d'IA ?

Le modèle déploie des protections applicatives déterministes contre les injections directes de prompt, les tentatives de jailbreak et l'exfiltration des consignes système. En associant un filtrage d'entrée par expressions régulières et une vérification de sortie par jeton canari, le composant sécurise les interactions sans exiger de réentraînement du modèle.

### En quoi le contrôle dynamique par jeton canari empêche-t-il la fuite des instructions système ?

La classe LLMGuardrail crée un jeton cryptographique imprévisible et l'insère au cœur des instructions système avec une consigne stricte de non-divulgation. Lors de la phase de sortie, le validateur inspecte les textes générés et bloque immédiatement toute réponse contenant ce jeton confidentiel.

### Pourquoi un assainissement déterministe en entrée est-il nécessaire si le modèle intègre déjà des garde-fous natifs ?

Les filtres de sécurité intégrés aux modèles peuvent être déjoués par des formulations adverses complexes, des encodages ou des scénarios d'usurpation de rôle. Le filtrage lexical par expressions régulières neutralise les modèles d'attaque connus avant l'ingestion des jetons dans le contexte, réduisant les risques et les coûts de calcul.

### Comment ce modèle de guardrails s'articule-t-il avec les agents autonomes et les architectures RAG ?

Le composant sécurise l'inférence en assainissant à la fois les fragments documentaires récupérés et les requêtes utilisateur avant l'assemblage de l'invite finale. Ce cloisonnement empêche les injections indirectes cachées dans les bases vectorielles d'altérer le raisonnement de l'agent ou d'exécuter des outils non autorisés.

### Quel est l'impact de la couche de validation sur les performances de calcul et la latence globale ?

Le module Python fonctionne entièrement en mémoire locale grâce à des expressions régulières précompilées et des comparaisons natives de chaînes, exécutant le contrôle en moins de 2 millisecondes. Ne sollicitant aucune API distante ni modèle évaluateur secondaire, la solution n'ajoute aucune latence réseau aux applications en production.

***

## Liens Utiles

* **Portail Institutionnel:** https://ag.tavardt.com/fr/
* **Suite de Diagnostic:** https://tools.tavardt.com/fr/
* **Contact Technique:** https://ag.tavardt.com/fr/contact/

***

### Licence
Distribué sous la Licence MIT. Consultez [LICENSE](LICENSE) pour obtenir tous les détails.

***

© 2026 TAVARDT. Tous droits réservés.
