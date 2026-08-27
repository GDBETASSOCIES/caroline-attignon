# Déploiement du portfolio sur GitHub Pages

Marche à suivre pour mettre le site en ligne. Les modifications de code sont
déjà faites et commitées en local ; il reste des actions à réaliser sur
GitHub, décrites ici étape par étape.

---

## État actuel

| Élément | État |
|---|---|
| Dépôt GitHub | `GDBETASSOCIES/caroline-attignon` (public) |
| Branche par défaut | `main` |
| GitHub Pages | **non activé** |
| Commit local prêt à pousser | `b54aae4` — *Préparer la publication sur GitHub Pages* |
| Droit d'écriture | **bloqué** — voir étape 2 |

Le site est un template Bootstrap statique (HTML/CSS/JS) sans étape de build :
GitHub Pages peut le servir directement depuis la racine du dépôt.

---

## Étape 1 — Créer le formulaire Formspree

GitHub Pages ne sert que des fichiers statiques : il n'exécute pas PHP. Le
fichier `forms/contact.php` ne peut donc pas fonctionner en ligne. Le
formulaire a été rebasculé sur **Formspree**, un service gratuit qui reçoit
les soumissions et les transfère par e-mail.

1. Aller sur **https://formspree.io** et créer un compte gratuit
   (utiliser de préférence `a.caroline@gdbetassocies.com`, l'adresse affichée
   sur la page contact, pour recevoir les messages directement).
2. Cliquer sur **+ New Form**.
   - *Form name* : `Contact Caroline Attignon`
   - *Send to* : l'adresse qui doit recevoir les messages
3. Formspree affiche une URL de la forme :
   `https://formspree.io/f/xxxxxxxx`
   La partie `xxxxxxxx` est **l'identifiant du formulaire**.
4. Ouvrir `contact.html`, ligne 112, et remplacer `VOTRE_ID_FORMSPREE` par cet
   identifiant :

   ```html
   <!-- avant -->
   <form action="https://formspree.io/f/VOTRE_ID_FORMSPREE" method="post" class="php-email-form" ...>

   <!-- après (exemple) -->
   <form action="https://formspree.io/f/xrgkabcd" method="post" class="php-email-form" ...>
   ```

5. Commiter la modification :

   ```bash
   git add contact.html
   git commit -m "Renseigner l'identifiant Formspree du formulaire de contact"
   ```

> **Note** — Le premier message envoyé déclenche un e-mail de confirmation de
> Formspree qu'il faut valider une fois. Le plan gratuit autorise 50 messages
> par mois.

---

## Étape 2 — Rétablir le droit d'écriture sur le dépôt

Le push est actuellement refusé :

```
remote: Permission to GDBETASSOCIES/caroline-attignon.git denied to MysterKedo.
fatal: unable to access '...': The requested URL returned error: 403
```

Les identifiants GitHub enregistrés sur ce poste (dans *Gestionnaire
d'identification* Windows, via Git Credential Manager) appartiennent au compte
**MysterKedo**, qui n'a pas les droits d'écriture sur l'organisation
**GDBETASSOCIES**. La lecture fonctionne car le dépôt est public.

Choisir **une** des trois solutions.

### Solution A — Donner les droits à MysterKedo *(la plus simple)*

À faire par un propriétaire de l'organisation GDBETASSOCIES :

1. Ouvrir https://github.com/GDBETASSOCIES/caroline-attignon
2. Onglet **Settings** → menu de gauche **Collaborators and teams**
3. **Add people** → saisir `MysterKedo`
4. Choisir le rôle **Write** → **Add**
5. MysterKedo reçoit une invitation par e-mail : elle doit être **acceptée**.

Rien à changer sur le poste : le push fonctionnera ensuite directement.

### Solution B — Se reconnecter avec le compte autorisé

Si vous disposez des identifiants d'un compte membre de GDBETASSOCIES :

1. Ouvrir le **Gestionnaire d'identification** Windows
   (menu Démarrer → « Gestionnaire d'identification »)
2. **Informations d'identification Windows**
3. Trouver l'entrée `git:https://github.com` → **Supprimer**
4. Relancer un `git push` : Windows ouvrira une fenêtre de connexion GitHub.
   S'y connecter avec le compte autorisé.

### Solution C — Utiliser un jeton d'accès personnel (PAT)

1. Sur le compte autorisé : https://github.com/settings/tokens
2. **Generate new token (classic)**
   - *Note* : `Déploiement caroline-attignon`
   - *Expiration* : 90 jours (ou selon votre politique)
   - *Scopes* : cocher **`repo`**
3. **Generate token** et copier la valeur (elle ne sera plus affichée ensuite).
4. Au prochain `git push`, saisir le nom d'utilisateur GitHub et **coller le
   jeton à la place du mot de passe**.

> ⚠️ Ne jamais commiter un jeton dans le dépôt.

---

## Étape 3 — Pousser le code

Une fois les droits rétablis :

```bash
git push origin main
```

Le remote est actuellement configuré en **HTTPS** :
`https://github.com/GDBETASSOCIES/caroline-attignon.git`

Il était auparavant en SSH, mais la clé SSH de ce poste n'est plus reconnue par
GitHub (`Permission denied (publickey)`). HTTPS est donc le mode recommandé.
Pour revenir malgré tout à SSH :

```bash
git remote set-url origin git@github.com:GDBETASSOCIES/caroline-attignon.git
```

---

## Étape 4 — Activer GitHub Pages

1. Ouvrir https://github.com/GDBETASSOCIES/caroline-attignon
2. Onglet **Settings** → menu de gauche **Pages**
3. Section **Build and deployment** :
   - *Source* : **Deploy from a branch**
   - *Branch* : **`main`** — et dossier **`/ (root)`**
4. Cliquer sur **Save**

GitHub lance alors un déploiement (visible dans l'onglet **Actions**). Comptez
**1 à 3 minutes** pour la première mise en ligne.

Le site sera accessible à l'adresse :

**https://gdbetassocies.github.io/caroline-attignon/**

---

## Étape 5 — Vérifier

Une fois le déploiement terminé, contrôler :

- [ ] La page d'accueil s'affiche avec ses styles et images
- [ ] La navigation fonctionne : *À propos*, *Services*, *CV*, *Contact*
- [ ] Le formulaire de contact envoie bien un message (tester une soumission)
- [ ] L'e-mail de confirmation Formspree a été validé
- [ ] Le site s'affiche correctement sur mobile

---

## Nom de domaine personnalisé (optionnel)

Pour servir le site sur un domaine du type `caroline.gdbetassocies.fr` :

1. Chez votre hébergeur DNS, créer un enregistrement **CNAME** :
   `caroline` → `gdbetassocies.github.io`
2. Dans **Settings → Pages → Custom domain**, saisir le domaine complet et
   **Save**. GitHub crée alors un fichier `CNAME` à la racine du dépôt.
3. Une fois la validation DNS passée, cocher **Enforce HTTPS**.

La propagation DNS peut prendre jusqu'à 24 h.

---

## Points restants à traiter (hors déploiement)

Relevés en passant, à corriger quand vous le souhaitez :

- **`contact.html` ligne 104** — le lien de l'adresse e-mail est vide
  (`<a href="">`). Un clic recharge la page au lieu d'ouvrir la messagerie.
  Correction : `<a href="mailto:a.caroline@gdbetassocies.com">`.
- **`forms/contact.php`** — devenu inutile depuis le passage à Formspree.
  Le fichier peut être supprimé (il n'est de toute façon jamais exécuté par
  GitHub Pages).
- **Textes du formulaire en anglais** — le bouton affiche « Send Message » et
  le message de confirmation « Your message has been sent. Thank you! », alors
  que le reste du formulaire est en français.
- **`starter-page.html`** — page d'exemple du template, non reliée à la
  navigation. À supprimer si elle n'est pas utilisée.

---

## Modifications déjà effectuées

Commit `b54aae4` :

| Fichier | Modification |
|---|---|
| `contact.html` | Action du formulaire redirigée de `forms/contact.php` vers Formspree |
| `assets/vendor/php-email-form/validate.js` | Ajout de l'en-tête `Accept: application/json` et prise en charge de la réponse JSON de Formspree, en plus du texte `OK` que renvoyait le script PHP |
| `.nojekyll` | Ajouté — indique à GitHub Pages de servir les fichiers tels quels, sans passer par Jekyll |
