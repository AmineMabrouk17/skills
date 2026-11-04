# Docs de domaine

Comment les skills d'ingénierie doivent consommer la documentation de domaine de ce repo lors de l'exploration de la base de code.

## Avant d'explorer, lisez ceci

- **`GLOSSARY.md`** à la racine du repo, ou
- **`GLOSSARY-MAP.md`** à la racine du repo s'il existe : il pointe vers un `GLOSSARY.md` par contexte. Lisez chacun de ceux qui sont pertinents pour le sujet.
- **`docs/adr/`** : lisez les ADR qui touchent la zone dans laquelle vous allez travailler. Dans les repos multi-contextes, vérifiez aussi `src/<context>/docs/adr/` pour les décisions propres à un contexte.

Si l'un de ces fichiers n'existe pas, **continuez sans rien signaler**. Ne signalez pas leur absence ; ne suggérez pas de les créer d'emblée. Le skill `/domain-modeling` (accessible via `/grill-with-docs` et `/improve-codebase-architecture`) les crée à la demande, quand des termes ou des décisions sont réellement tranchés.

## Structure des fichiers

Repo mono-contexte (la plupart des repos) :

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

Repo multi-contexte (présence de `GLOSSARY-MAP.md` à la racine) :

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← system-wide decisions
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← context-specific decisions
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## Utilisez le vocabulaire du glossaire

Quand votre sortie nomme un concept de domaine (dans un titre d'issue, une proposition de refactoring, une hypothèse, un nom de test), utilisez le terme tel que défini dans `GLOSSARY.md`. Ne dérivez pas vers des synonymes que le glossaire évite explicitement.

Si le concept dont vous avez besoin n'est pas encore dans le glossaire, c'est un signal : soit vous inventez un langage que le projet n'utilise pas (reconsidérez), soit il y a un vrai manque (notez-le pour `/domain-modeling`).

## Signalez les conflits d'ADR

Si votre sortie contredit un ADR existant, signalez-le explicitement plutôt que de le contourner en silence :

> _Contredit l'ADR-0007 (event-sourced orders), mais mérite d'être rouvert parce que…_
