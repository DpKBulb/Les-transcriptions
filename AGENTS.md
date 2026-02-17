# AGENTS.md - Quartz v4 Générateur de Sites Statiques

## Vue d'ensemble du projet

Quartz v4 est un générateur de sites statiques pour les coffres-forts Obsidian et les jardins numériques, construit avec TypeScript, Preact et l'écosystème Unified/Remark.

## Commandes de build et de développement

```bash
# Installer les dépendances
npm install

# Construire et servir localement
npx quartz build --serve

# Construire pour la production
npx quartz build

# Vérification des types uniquement
npx tsc --noEmit

# Exécuter tous les tests
npm test

# Exécuter un fichier de test unique
tsx ./quartz/util/path.test.ts
tsx ./quartz/depgraph.test.ts

# Vérifier le formatage et les types
npm run check

# Formater le code avec Prettier
npm run format
```

## Directives de style de code

### Configuration TypeScript

- **Cible** : ESNext avec modules ESNext
- **Mode strict** : Activé (strict: true)
- **JSX** : Utilise Preact (`jsx: "react-jsx"`, `jsxImportSource: "preact"`)
- **Importations par défaut synthétiques** : Activées
- **Compilation incrémentale** : Activée

### Formatage (Prettier)

- Pas de points-virgules (`semi: false`)
- Indentation de 2 espaces (`tabWidth: 2`)
- Virgules finales (`trailingComma: "all"`)
- Largeur de ligne de 100 caractères (`printWidth: 100`)
- Propriétés de citation selon les besoins (`quoteProps: "as-needed"`)

### Importations

- Utiliser les modules ES avec extensions `.js` explicites dans les importations
- Grouper les importations : dépendances externes d'abord, puis modules internes
- Utiliser `import type` pour les importations de types uniquement
- Les importations internes utilisent des chemins relatifs (par exemple, `../../util/path`)

```typescript
// Bon
import { slug as slugAnchor } from "github-slugger"
import type { Element as HastElement } from "hast"
import rfdc from "rfdc"
import { clone } from "../../util/path.js"
```

### Conventions de nommage

- **Fonctions/variables** : camelCase (par exemple, `slugifyFilePath`, `getFullSlug`)
- **Types/Interfaces** : PascalCase (par exemple, `QuartzConfig`, `FullSlug`)
- **Composants** : PascalCase (par exemple, `QuartzComponent`)
- **Gardes de type** : préfixe `is` (par exemple, `isFullSlug`, `isFilePath`)
- **Assistants privés** : préfixe `_` (par exemple, `_getFileExtension`, `_rebaseHtmlElement`)
- **Types marqués** : Noms descriptifs avec suffixe `Like` (par exemple, `SlugLike<T>`)

### Modèles de types

Utiliser des types marqués pour le typage nominal :

```typescript
type SlugLike<T> = string & { __brand: T }
export type FilePath = SlugLike<"filepath">
```

Gardes de type avec restriction appropriée :

```typescript
export function isFullSlug(s: string): s is FullSlug {
  const validStart = !(s.startsWith(".") || s.startsWith("/"))
  const validEnding = !s.endsWith("/")
  return validStart && validEnding && !containsForbiddenCharacters(s)
}
```

### Gestion des erreurs

- Préférer les gardes de type et les assertions aux exceptions
- Utiliser des types marqués pour prévenir les états invalides à la compilation
- Valider les entrées avec des vérifications explicites plutôt que try/catch

### Tests

- Utilise le testeur intégré de Node.js (`node:test` et `node:assert`)
- Les tests sont co-localisés avec les fichiers source (par exemple, `path.test.ts` à côté de `path.ts`)
- Utiliser `describe` pour les suites de tests et `test` pour les tests individuels
- Organiser les tests avec des blocs describe imbriqués pour plus de clarté

```typescript
import test, { describe } from "node:test"
import assert from "node:assert"

describe("path utilities", () => {
  test("slugifyFilePath handles basic cases", () => {
    assert.strictEqual(result, expected)
  })
})
```

### Organisation du code

- **quartz/components/** : Composants UI Preact
- **quartz/plugins/** : Transformeurs, émetteurs et filtres
- **quartz/util/** : Fonctions utilitaires et assistants
- **quartz/i18n/** : Internationalisation et locales
- **quartz/processors/** : Processeurs de pipeline de build

### Dépendances clés

- **Preact** : Composants JSX
- **Unified/Remark/Rehype** : Traitement Markdown
- **HAST** : Manipulation de l'AST HTML
- **ESBuild** : Bundling et compilation SASS
- **tsx** : Exécution TypeScript pour les tests

### Conventions spéciales

- Certains fichiers doivent être isomorphiques (fonctionner dans le navigateur et Node) - éviter les API spécifiques à Node
- Utiliser les commentaires `// @ts-ignore` avec parcimonie pour importer des scripts inline
- Quartz utilise une architecture basée sur des plugins avec des transformeurs, des filtres et des émetteurs
- Les utilitaires de chemin utilisent des types de slug personnalisés pour la gestion des URL avec sécurité de type

## Organisation du contenu (source/content/)

Le dossier `source/content/` contient les coffres-forts Obsidian utilisés pour la prise de notes des sessions de jeux de rôle (JDR). Chaque table de jeu possède sa propre organisation.

### La Table de Méléagant (Donjons et Dragons PbtA homemade)

Table de Donjons et Dragons utilisant un système Powered by the Apocalypse (PbtA) maison. Organisation typique :

```
source/content/
└── La Table de Méléagant/
    ├── Personnages Joueurs (PJ)/     # Fiches et background des personnages joueurs
    ├── Personnages Non-Joueurs (PNJ)/ # PNJs rencontrés et importants
    ├── Lieux/                         # Description des lieux visités et connus
    ├── Sessions/                      # Résumés et transcriptions des sessions
    ├── Factions/                      # Groupes, guildes et organisations
    ├── Objets et Trésors/            # Équipements, artefacts et butins
    ├── Quêtes et Missions/           # Objectifs et quêtes en cours/terminées
    └── Règles et Mécaniques/         # Documentation du système PbtA maison
```

### Wildsea

Table axée sur le narratif dans l'univers de Wildsea. Organisation sommaire actuelle (en cours de structuration) :

```
source/content/
└── Wildsea/
    ├── Personnages/                   # PJ et PNJs
    ├── Sessions/                      # Comptes-rendus des sessions
    ├── Lieux/                         # Îles, navires et points d'intérêt
    └── Lore et Univers/              # Éléments du monde et background
```

**Note** : L'organisation de Wildsea évoluera vers une structure similaire à celle de la Table de Méléagant à terme.
