# renovate-config

Preset [Renovate](https://docs.renovatebot.com/) partagé par les projets `simoncamilotti`.
Les règles de fusion, les regroupements et les blocages de versions vivent ici une seule fois
et profitent à tous les projets.

## Utilisation

Dans chaque projet, `renovate.json` à la racine :

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>simoncamilotti/renovate-config"]
}
```

Renovate tourne via l'application GitHub [Renovate](https://github.com/apps/renovate) de Mend,
installée sur toute l'organisation. Ce dépôt est public : l'application lit le preset sans
droit particulier.

L'organisation est réglée sur **Scan Only** dans le portail Mend : Renovate analyse tous les
dépôts sans rien y créer. Le preset déclare `"mode": "full"` : seuls les dépôts qui l'étendent
reçoivent des PR. Un dépôt sans `renovate.json` reste donc silencieux.

## Règles

| Sujet | Règle |
|---|---|
| Base | `config:recommended`, commits au format Conventional Commits |
| Planning | Le lundi avant 9 h (Europe/Paris) ; 5 PR ouvertes au plus, 2 par heure |
| Délai avant adoption | Une version n'est proposée que 3 jours après sa publication (`minimumReleaseAge`). Le même délai est configuré dans pnpm |
| Correctifs et mineures | Regroupés dans une seule PR `all non-major`, fusionnée automatiquement après une CI verte |
| Majeures | Une PR par famille (Nx, NestJS, Prisma, React, Playwright, TypeScript et ESLint, Vite et Vitest), label `major-update`, revue manuelle |
| Bibliothèques à risque | NestJS (`core`, `common`), Prisma, React, React Router, Next : même les mineures passent en revue manuelle |
| Alertes de vulnérabilité | Label `security`, fusion automatique |
| Images Docker | Épinglées par digest |
| Actions GitHub | Épinglées par SHA, sauf `simoncamilotti/shared-workflows`, appelé par son tag `@v1` |
| Images de docker-compose | Une majeure rappelle de sauvegarder les données locales avant la fusion |

### Fusion automatique sans protection de branche

Au plan Free, les dépôts privés n'ont ni protection de branche ni vérifications obligatoires.
La fusion automatique de GitHub fusionnerait donc sans attendre la CI. `platformAutomerge: false`
laisse Renovate fusionner lui-même, seulement quand toutes les vérifications sont vertes.

## Ajouter une règle

- Une règle utile à un seul projet reste dans le `renovate.json` de ce projet.
- Un blocage de version (`allowedVersions`) porte une `description` : la cause, et la condition
  pour le lever.
- Vérifier le preset avant de pousser :

```sh
npx --yes --package renovate -- renovate-config-validator --strict default.json
```

Les projets lisent la branche `main` : une modification s'applique à tous au prochain passage
de Renovate.
