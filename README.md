# F1 Companion

Application web d'une seule page : calendrier de la saison F1 séance par séance,
converti à ton fuseau **et** à celui du circuit, classements pilotes et écuries,
et lancement en un tap du diffuseur de ton choix — gratuit ou sur abonnement,
dans le pays que tu veux.

**Ce n'est pas un lecteur vidéo.** L'app ne capture, ne rediffuse et ne réencode
aucun flux. Elle n'embarque aucun VPN, aucun proxy, aucune usurpation de
localisation, et ne stocke aucun identifiant. Elle ouvre le site officiel du
diffuseur, où la lecture se fait dans ses conditions.

👉 **[Ouvrir l'app](https://kanjalex-dev.github.io/F1-Companion/)**

---

## Ce que ça fait

| Onglet | Contenu |
|---|---|
| **En direct** | Séance en cours ou prochaine, compte à rebours à la seconde, heure locale + heure circuit, raccourci vers les diffuseurs |
| **Calendrier** | 23 manches, dépliables séance par séance, week-ends Sprint signalés, vainqueur affiché sur les manches passées |
| **Classements** | Championnat pilotes, championnat écuries, palmarès des manches disputées |
| **Réglages** | Export calendrier, filtre pays, préférence de langue, abonnements, correction des liens, lien de réglages permanent |

Un badge **Gratuit** apparaît sur chaque séance couverte par au moins une chaîne
en clair, avant même d'ouvrir la fiche.

### Le verdict

Ouvrir une séance donne d'abord une réponse en une phrase à la seule question qui
compte — *est-ce que je peux regarder ça ?* — en croisant les abonnements
déclarés et le pays sélectionné. Quatre états : couvert par un abonnement,
disponible gratuitement, abonnement supplémentaire requis, aucune source connue.

Les sources se filtrent ensuite en **Tout / Gratuit / Mes abos**, avec le compte
de chacun. Sans ça, une course affichait vingt cartes dont une seule concernait
l'utilisateur.

### Export vers Calendrier Apple

Réglages → *Calendrier Apple* produit un fichier `.ics` (RFC 5545) des séances
choisies, avec une alerte configurable avant chaque départ. Une fois importé,
iOS gère les notifications lui-même, sans que l'app soit ouverte — c'est ce qui
remplace les notifications locales perdues en quittant l'application native.

Les heures sont en UTC dans le fichier : l'appareil les convertit, y compris
après un changement de fuseau. Chaque séance porte un identifiant stable, donc
un réimport met les événements à jour au lieu de les dupliquer.

## Pourquoi l'héberger sur GitHub Pages plutôt qu'ailleurs

Le fichier embarque le calendrier et les classements, donc il fonctionne hors
ligne — en avion, en roaming. Mais il tente aussi un rafraîchissement depuis
l'API [Jolpica-F1](https://github.com/jolpica/jolpica-f1) au chargement.

Sur un domaine classique comme `github.io`, cet appel **passe** : le calendrier
et les horaires se mettent à jour tout seuls. Publié comme artefact claude.ai,
l'appel est bloqué par la politique de sécurité et l'app reste sur ses données
figées. Même fichier, deux comportements — d'où l'intérêt de cet hébergement.

## Installation sur l'écran d'accueil

**iPhone (Safari)** — Partager → « Sur l'écran d'accueil ». L'app s'ouvre en
plein écran, sans barre d'adresse.

**Mac** — Safari : Fichier → Ajouter au Dock. Chrome : menu → Diffuser,
enregistrer et partager → Installer.

Astuce : dans Réglages, copie ton **lien de réglages** et ajoute *celui-là*
plutôt que l'URL nue. Il encode ton pays, ta langue, tes abonnements et tes
corrections de liens dans l'URL — ta configuration revient donc même en
navigation privée et sur un autre appareil.

## Confidentialité

Aucun appel à un service tiers hors l'API calendrier, aucun cookie, aucun
traceur, aucune analytique. Les préférences restent dans `localStorage` du
navigateur. Derrière un VPN, le comportement est identique — il n'y a rien à
masquer côté app puisqu'elle ne parle à personne.

---

## Données et fiabilité

| Donnée | Source | Statut |
|---|---|---|
| Calendrier 2026 (23 manches) | API Jolpica-F1 | Relevé le 02/09/2026, rafraîchi automatiquement sur Pages |
| Classements pilotes | API Jolpica-F1 | Après la manche 12, figé |
| Classements écuries | **Recalculé** depuis les points pilotes | Voir note ci-dessous |
| Diffuseurs par pays | [Liste Wikipédia des diffuseurs F1](https://en.wikipedia.org/wiki/List_of_Formula_One_broadcasters) | Consultée le 02/09/2026 |
| Liens vers les players | Chemins habituels des plateformes | **Non vérifiés** — voir ci-dessous |

### Points à connaître avant de s'y fier

**Classement écuries recalculé.** L'API sert le classement constructeurs à une
manche antérieure au classement pilotes. Plutôt que d'afficher deux tables qui
ne concordent pas, les totaux écuries sont additionnés depuis les points de
leurs pilotes. Exact sauf changement de pilote en cours de saison — l'écurie RB
aligne trois pilotes cette année, sa ligne est donc approximative.

**Liens directs non vérifiés.** Les sites de diffuseurs bloquent l'accès
automatisé, il n'a pas été possible de tester que chaque URL tombe bien sur le
player. Chaque source porte donc un badge *Non vérifié*, garde un bouton
*Accueil* en repli, et se corrige depuis l'app : déplie la source, colle la
bonne URL, elle remplace la valeur par défaut et suit ton lien de réglages.

**Droits de diffusion.** Ils changent chaque saison. Les entrées marquées
*À vérifier* dans l'app n'ont pas été confirmées pour la saison en cours —
notamment ORF, RTL Allemagne et Mediaset Espagne.

**Anomalie de données connue.** L'API renvoie la manche 16 sous l'intitulé
« Bahrain Grand Prix in Malaysia », au circuit de Sepang. L'app affiche
« Grand Prix de Malaisie » avec un avertissement. Le calendrier compte 23
manches, pas 24 — à confirmer auprès du calendrier officiel.

## Maintenance

Tout tient dans `index.html`, sans dépendance ni build. Les blocs à éditer :

- `ROUNDS` — le calendrier (écrasé automatiquement par l'API quand l'appel passe)
- `DRIVERS`, `RESULTS` — classements et vainqueurs, à rafraîchir à la main
- `SOURCES` — les diffuseurs : pays, langue, gratuité, couverture, géoblocage
- `LIVE_URLS` — les liens vers les players, corrigeables aussi depuis l'app

Polices via Google Fonts, tout le reste inline.

## Licence

Usage personnel. Les marques, noms de chaînes et le calendrier F1 appartiennent
à leurs détenteurs respectifs ; ce dépôt ne contient que du code et des liens
vers des pages publiques.
