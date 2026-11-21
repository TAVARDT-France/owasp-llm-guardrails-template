# OWASP LLM01 Guardrails Template & Guide de Sécurité IA | TAVARDT France

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![OWASP LLM01](https://img.shields.io/badge/OWASP-LLM01%20Hardened-ff0055.svg)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![Language](https://img.shields.io/badge/Language-Français-green.svg)](README.md)
[![AI Diagnostics](https://img.shields.io/badge/TAVARDT-AI%20Evaluator-5b3cf5.svg)](https://tools.tavardt.com/fr/evaluateur-robustesse-llm/)

Blueprint déclaratif et middleware open source pour la mitigation de l'injection de prompt (OWASP LLM01), de la fuite de system prompt et des attaques contradictoires sur les agents d'IA, conforme au RGPD et à l'AI Act européen.

Développé et maintenu par l'équipe d'ingénierie en intelligence artificielle de **[TAVARDT France](https://ag.tavardt.com/fr/super-intelligence/)**.

---

## 1. Vue d'Ensemble de la Sécurité pour l'IA et les LLMs

Ce dépôt fournit une bibliothèque modulaire et légère pour déployer des **garde-fous (guardrails)** déterministes sur des agents autonomes, des architectures RAG et des applications d'entreprise intégrant des modèles de fondation (OpenAI, Anthropic, Gemini, Llama, Mistral).

### La Nécessité des Garde-Fous Applicatifs
Les grands modèles de langage traitent indifféremment les consignes système et les données soumises par l'utilisateur. Sans validation stricte en entrée et en sortie, les applications d'IA s'exposent au détournement d'instructions (jailbreak), à l'exfiltration de données confidentielles et aux violations de conformité.

---

## 2. Vecteurs de Menace Neutralisés (OWASP Top 10 for LLMs)

| Couche de Défense | Menace Neutralisée | Mécanisme de Protection |
| :--- | :--- | :--- |
| **Input Sanitizer** | Injection Directe de Prompt (Jailbreak) | Détection heuristique des motifs de contournement (`ignore les instructions précédentes`, balises XML falsifiées). |
| **Canary Token Verifier** | Fuite du Prompt Système | Surveillance de jetons canaris inviolables dans la sortie pour bloquer l'exfiltration des règles internes. |
| **RAG Boundary Guard** | Injection Indirecte de Prompt | Confinement et isolation sémantique des documents tiers injectés dans la base vectorielle. |
| **Structured Output Filter** | Exécution d'Actions Non Autorisées | Validation rigoureuse des schémas JSON avant l'appel d'APIs ou de connecteurs d'outils MCP. |

---

## 3. Audit et Diagnostic Préalable de Robustesse du Modèle

Avant de déployer des agents conversationnels en production, évaluez leur niveau de résistance aux attaques avec l'outil de diagnostic TAVARDT :

* **Audit de Robustesse IA :** [Évaluateur de Robustesse de LLMs TAVARDT France](https://tools.tavardt.com/fr/evaluateur-robustesse-llm/)
* **Catalogue des Outils de Diagnostic :** [TAVARDT Tools France](https://tools.tavardt.com/fr/)

---

## 4. Installation et Guide d'Intégration Rapide

### Clonage & Exécution

```bash
git clone https://github.com/TAVARDT-France/owasp-llm-guardrails-template.git
cd owasp-llm-guardrails-template
python examples/quickstart.py
```

### Exemple d'Intégration en Python

```python
from guardrails.validator import LLMGuardrail

guard = LLMGuardrail()

# 1. Validation en Entrée (Input Guardrail)
is_safe, error = guard.validate_input(user_input)
if not is_safe:
    raise ValueError(f"Entrée bloquée suite à un motif contradictoire suspect: {error}")

# 2. Injection du Jeton Canari dans le Prompt Système
hardened_system_prompt = guard.get_canary_injection_prompt(system_prompt)

# 3. Validation en Sortie (Output Guardrail)
is_valid, leak_error = guard.validate_output(completion)
if not is_valid:
    raise SecurityError("Alerte critique : Fuite du prompt système détectée via le jeton canari !")
```

---

## 5. Conseil Stratégique en Sécurité de l'IA & Agents Autonomes

Pour les entreprises nécessitant un accompagnement en sécurité de l'IA, le blindage d'agents autonomes ou la conformité à l'AI Act :

* **Portail Entreprise :** [TAVARDT France](https://ag.tavardt.com/fr/super-intelligence/)
* **Contact et Échange Technique :** [Prendre Rendez-vous avec l'Ingénierie](https://ag.tavardt.com/fr/contact/)

---

## Licence

Distribué sous licence open-source MIT. Consultez le fichier [`LICENSE`](LICENSE) pour plus d'informations.  
Copyright (c) 2026 TAVARDT France.
