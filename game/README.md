# 💎 GEM RUSH — arène 2D multijoueur (3–4 joueurs)

Un jeu d'arène 2D ultra-coloré, jouable **à 3 ou 4 en ligne** directement depuis le
navigateur, **sans installation ni serveur** : tout passe en **peer-to-peer (WebRTC)**
grâce à [PeerJS](https://peerjs.com/), ce qui le rend parfait pour un hébergement
statique **GitHub Pages**.

Inspiré des jeux `.io` et des party games : ramasse un maximum de gemmes, fonce (dash)
dans tes adversaires pour leur voler des points, attrape des bonus, et sois en tête à la
fin du chrono !

---

## 🎮 Comment jouer

| Action | Clavier | Tactile |
|---|---|---|
| Se déplacer | `ZQSD` ou flèches | joystick (moitié gauche de l'écran) |
| Dash 💨 | `Espace` | bouton **DASH** |

- **Ramasse les gemmes** ⭐ : bleu = 1 pt, vert = 3, or = 6, rouge = 12.
- **Dash** dans un adversaire pour lui **voler des gemmes** (sauf s'il a un bouclier).
- **Bonus** : 🧲 aimant (attire les gemmes), 🛡 bouclier (protège du dash), ⚡ vitesse.
- Manche de **90 secondes** — le meilleur score gagne. 🏆

### Lancer une partie
1. Un joueur clique **« Créer une partie »** → il obtient un **code à 4 lettres**.
2. Il partage le code (ou le lien d'invitation « Copier »).
3. Les autres ouvrent la **même page**, entrent le code, cliquent **« Rejoindre »**.
4. L'hôte clique **« Lancer la partie »** (2 à 4 joueurs).

---

## 🛡️ Sécurités réseau (robustesse)

Le jeu est **host-authoritative** : l'hôte fait tourner la simulation, les autres
envoient leurs commandes et reçoivent l'état. Plusieurs protections gèrent les coupures
et la latence :

- **Reconnexion automatique** avec *backoff exponentiel* (jusqu'à 8 tentatives) si un
  joueur perd le lien avec l'hôte.
- **Heartbeat / ping** toutes les secondes : mesure la latence et détecte les
  connexions silencieuses (seuils *instable* puis *hors ligne*).
- **Reprise de slot** : un joueur qui se reconnecte récupère **son score et sa couleur**
  (identifié par son pseudo).
- **Interpolation des snapshots** + **prédiction locale** du joueur : mouvement fluide
  même avec du jitter réseau.
- **Indicateur de qualité réseau** en jeu (vert / jaune *instable* / rouge *hors ligne*
  + ms de ping).
- **Overlay « reconnexion »** non bloquant, avec sortie propre vers le menu.
- **Limitation du débit** : inputs à ~22 Hz, snapshots à ~18 Hz.
- Reconnexion automatique au **serveur de signalisation** en cas de coupure temporaire.

> Note : la migration d'hôte n'est pas gérée — si **l'hôte** quitte définitivement, la
> partie se termine et chacun revient au menu (message explicite affiché).

---

## 🚀 Héberger sur GitHub Pages

Le jeu est un **seul fichier HTML statique** (`game/index.html`). Aucune compilation.

1. Pousse ce dossier sur ton dépôt GitHub.
2. **Settings → Pages → Build and deployment → Source : _Deploy from a branch_**,
   choisis la branche (ex. `main`) et le dossier `/ (root)`.
3. Le jeu sera accessible à :
   **`https://<ton-user>.github.io/<ton-repo>/game/`**
4. Partage cette URL — chaque joueur l'ouvre, et c'est parti !

> PeerJS et les polices sont chargés via CDN : une simple connexion internet suffit,
> rien à installer côté joueur.

---

## 🧱 Détails techniques

- **Rendu** : Canvas 2D, monde fixe `1280×800` mis à l'échelle de l'écran (tout le monde
  voit toute l'arène), effets de glow, particules, screen-shake, confettis.
- **Réseau** : PeerJS (WebRTC DataChannels fiables). L'hôte réserve l'id
  `gemrush-<CODE>` ; les autres s'y connectent en dérivant l'id depuis le code.
- **Aucune dépendance de build**, aucun backend, aucune base de données.
- **Responsive** : contrôles tactiles automatiques sur mobile (joystick + bouton dash).
