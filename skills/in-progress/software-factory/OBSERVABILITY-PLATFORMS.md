# Plateformes d'observabilité

> Panorama de 2026-10, sans garantie d'actualité. Servez-vous-en pour vous orienter, puis recherchez vous-même les options actuelles : produits, prix, limites et bêtas changent vite.

De quoi s'orienter pour remplir l'emplacement de la plateforme d'observabilité, ou pour décider d'une absence assumée. Les types se répartissent selon **ce que l'humain doit voir à l'intérieur d'une exécution** : son log, sa trace d'agent (chaque appel de modèle et chaque appel d'outil comme un span), ou cette trace jointe au reste du système de l'utilisateur. Le clivage entre auto-hébergé et SaaS recoupe les trois derniers types. Quand une exécution doit agir sur ce que voit la plateforme, faites de la plateforme une source de tickets : un moniteur crée le ticket, et une exécution le lit.

## Comment les runners se branchent

Chaque type reçoit des données par l'un de cinq branchements. Nommez le branchement en même temps que le produit quand vous remplissez l'emplacement.

- **OTel du harness** : le harness d'agent exporte lui-même OpenTelemetry. Claude Code et le Claude Agent SDK prennent `CLAUDE_CODE_ENABLE_TELEMETRY=1` plus des variables `OTEL_*`, l'export des prompts et de la sortie d'outil étant optionnel ; Codex CLI prend un bloc `[otel]` dans `config.toml`. ([Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/observability), [Claude Code](https://code.claude.com/docs/en/monitoring-usage), [Codex config](https://developers.openai.com/codex/config-advanced))
- **Push de la plateforme** : la plateforme de modèles envoie les traces à votre place, comme le font les Claude Managed Agents vers une destination OTLP réglée dans la Claude Console. ([Claude docs](https://platform.claude.com/docs/en/manage-claude/opentelemetry/api))
- **Wrapper SDK** : un appel dans le code du runner, comme `configure_claude_agent_sdk()` de LangSmith.
- **Plugin de hook** : un hook Claude Code lit la transcription de session à chaque Stop et l'envoie, comme dans le plugin Claude Code de Langfuse.
- **Passerelle** : l'URL de base du modèle du runner pointe vers un proxy journalisant. Zéro code dans le runner, et elle ne voit que les appels de modèle : les appels d'outil à l'intérieur du harness restent invisibles.

OpenTelemetry est le fil commun : l'OTel du harness et le push de la plateforme atterrissent dans n'importe quel backend OTLP. Les conventions sémantiques GenAI (spans `invoke_agent`, `chat`, `execute_tool`) se stabilisent encore, alors attendez-vous à ce que les noms de spans bougent. ([OTel GenAI agent spans](https://opentelemetry.io/docs/specs/semconv/gen-ai/gen-ai-agent-spans))

## Type 1 : Aucune (le produit runner ou orchestrateur en montre assez)

**De quoi il s'agit** : l'humain lit chaque exécution là où le runner ou l'orchestrateur la montre déjà : la page d'exécution de CI, le tableau de bord du sandbox, la transcription de l'agent enregistrée comme artefact d'exécution. Aucun branchement à écrire, aussi auto-hébergé que le runner, et compris dans son coût.

**Quand une factory le choisit** : peu d'exécutions par jour, un humain qui surveille, et la question est « est-ce que ça a échoué, et qu'est-ce que ça a affiché ? » La plupart des factories commencent ici, et en sortent quand elles ont besoin de recherche sur plusieurs exécutions, d'un cumul de jetons ou de coûts par exécution, ou d'une rétention plus longue que ce que garde le runner.

**Points de départ** : [logs d'exécution GitHub Actions](https://docs.github.com/en/organizations/managing-organization-settings/configuring-the-retention-period-for-github-actions-artifacts-and-logs-in-your-organization), [logs et métriques de sandbox E2B](https://e2b.dev/docs), [télémétrie de sandbox Daytona](https://www.daytona.io/docs/en/observability/otel-collection/), [traces de l'OpenAI Agents SDK](https://openai.github.io/openai-agents-python/tracing/). Les produits de sandbox relaient souvent les mêmes données en OTLP, ce qui est le chemin de sortie vers le type 2.

**À vérifier** : les jours de rétention par produit runner ; certains sandboxes gardent très peu (Daytona : 3 jours, en 2026-10).

## Type 2 : Plateforme de traçage LLM et d'agent

**De quoi il s'agit** : un backend construit autour de la trace d'agent : chaque exécution comme un arbre d'appels de modèle et d'appels d'outil, avec coût en jetons, prompts et sessions, généralement avec des evals et des jeux de données par-dessus.

**Quand une factory le choisit** : l'humain demande « pourquoi l'agent a fait ça ? » sur beaucoup d'exécutions ; une exécution rétro ou un humain compare des exécutions ; la dépense en jetons a besoin d'un cumul par exécution. Auto-héberger une solution open source garde les transcriptions à la maison.

**Traits du type** : accepte l'OTel du harness, un wrapper SDK ou un plugin de hook, souvent aussi le push de la plateforme. Facturé au volume, et les exécutions d'agent sont riches en spans (un span par appel d'outil), donc budgétez par exécution plutôt que par requête.

**Points de départ** : [Langfuse](https://langfuse.com/pricing) et [Arize Phoenix](https://phoenix.arize.com) (open source, auto-hébergeables), [LangSmith](https://www.langchain.com/pricing-langsmith), [Braintrust](https://www.braintrust.dev/pricing).

**À vérifier** : les limites de l'offre gratuite, l'unité de facturation (trace, span, unité ou Go ; Braintrust est passé au Go en 2026-03), l'offre qui débloque l'auto-hébergement, et le propriétaire actuel.

## Type 3 : Backend APM et journalisation généraliste

**De quoi il s'agit** : la pile d'observabilité existante de l'utilisateur (logs, métriques, traces distribuées pour chaque service), avec une vue agent ou LLM ajoutée.

**Quand une factory le choisit** : l'équipe en paie déjà un ; les exécutions doivent se joindre aux traces de l'application elle-même (l'Agent SDK propage `TRACEPARENT`, donc une exécution s'imbrique dans la trace de son appelant) ; ou une source de tickets de type moniteur y lit déjà.

**Traits du type** : l'OTel du harness va directement au point de terminaison OTLP du fournisseur. Facturé au Go, à l'événement ou à l'hôte, donc le volume d'une factory est une erreur d'arrondi sur une facture existante et une surprise sur une nouvelle.

**Points de départ** : [Datadog Agent Observability](https://www.datadoghq.com/product/llm-observability/), [Grafana Cloud](https://grafana.com/pricing/), [Honeycomb](https://www.honeycomb.io/pricing), [SigNoz](https://signoz.io/pricing/) (open source).

**À vérifier** : si la vue agent est GA ou en preview, quels harness ont un guide d'installation, et les volumes de l'offre gratuite.

## Type 4 : Passerelle LLM avec journalisation des requêtes

**De quoi il s'agit** : un proxy entre le runner et le fournisseur de modèles qui journalise chaque requête et chaque réponse, avec routage, clés, budgets et cache à côté.

**Quand une factory le choisit** : l'utilisateur veut des plafonds de dépense par exécution ou un coffre de clés unique pour beaucoup de runners, et la journalisation vient avec ; ou le harness n'a pas d'export propre. Elle ne voit que les appels de modèle, alors associez-la au type 2 ou 3 pour les appels d'outil.

**Points de départ** : [LiteLLM Proxy](https://docs.litellm.ai/docs/proxy/logging) (open source, relaie les logs vers Langfuse ou OTel), [Portkey](https://portkey.ai/pricing).

**À vérifier** : la tarification à la requête en SaaS, et si le produit est toujours activement développé (Helicone est passé en mode maintenance après une acquisition, en 2026-10).

## Approfondir les recherches

Confrontez chaque candidat aux questions qui décident de cet emplacement :

1. **Le runner ou l'orchestrateur en montre-t-il déjà assez ?** Comparez ce dont l'humain a besoin (recherche multi-exécutions, coût par exécution, rétention) avec ce que donne la page d'exécution ou le tableau de bord du sandbox. Un oui ici signifie une absence assumée.
2. **Comment le harness d'agent exporte-t-il les traces ?** Trouvez lequel des cinq branchements le harness prend en charge, puis lesquels le produit accepte. Confirmez que les appels d'outil arrivent sous forme de spans, et si les prompts et la sortie d'outil demandent une option explicite.
3. **Est-il auto-hébergeable ?** La licence, l'offre qui l'autorise, et ce sur quoi il tourne. Décisif quand les transcriptions doivent rester à la maison.
4. **Quelle est la forme du coût ?** L'unité de facturation, l'offre gratuite, et une estimation par exécution en supposant un span par appel d'outil, multipliée par les exécutions par jour.
5. **Quelle est la durée de rétention ?** Les jours conservés sur chaque offre, face à la profondeur à laquelle l'humain remonte quand une exécution se passe mal.

**Où vivent les réponses** : la page de tarifs du fournisseur (unités, offres, rétention) ; la doc d'intégration du fournisseur pour le harness concerné ; la doc de supervision du harness lui-même (ce qu'il exporte, quels drapeaux sont en bêta) ; les conventions sémantiques GenAI d'OpenTelemetry ; le dépôt du produit pour la licence et l'activité des versions ; le changelog ou le blog du fournisseur pour les changements de propriétaire.

**Ce qui se périme le plus vite** : les limites et prix de l'offre gratuite ; les drapeaux bêta et preview (export de traces Claude Code, push de la plateforme Console, vues agent des fournisseurs) ; le propriétaire et le statut de maintenance ; les unités de facturation ; les jours de rétention ; les noms de spans GenAI.
