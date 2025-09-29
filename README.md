<div align="center">
  <img src="https://media.giphy.com/media/iIqmM5tTjmpOB9mpbn/giphy.gif" width="600" height="300"/>
</div>

# FastShop

Gestion d’un **panier d’achat** avec récupération des produits depuis une API **PocketBase** (auto-hébergée).  
Interface en **Svelte + Vite**, logique en **JavaScript**.

---

## Aperçu

![demo](./static/fastshop-demo.gif)
<!-- Remplace le chemin ci-dessus par TON gif (le même que ta capture). 
     Par ex. mets ton .gif dans /static et garde ce nom, ou change le lien. -->

---

## Fonctionnalités

- Liste de produits (titre, image, description, prix) via **fetch** depuis PocketBase
- **Ajout au panier** depuis la liste
- **Quantités dynamiques** (augmenter/diminuer/supprimer)
- **Total du panier** recalculé en temps réel
- **Persistance** du panier via `localStorage`
- Filtrage basique par catégories (optionnel)

---

## Stack & Outils

 <img src="https://github.com/devicons/devicon/blob/master/icons/svelte/svelte-original.svg" title="SveltLogo" alt="JavaScript" width="40" height="40"/>&nbsp;
 <img src="https://github.com/devicons/devicon/blob/master/icons/vitejs/vitejs-original.svg" title="SveltLogo" alt="JavaScript" width="40" height="40"/>&nbsp;
 <img src="https://github.com/devicons/devicon/blob/master/icons/javascript/javascript-original.svg" title="JavaScript" alt="JavaScript" width="40" height="40"/>&nbsp;
<img src="https://logowik.com/content/uploads/images/pocketbase-backend7491.logowik.com.webp" title="JavaScript" alt="JavaScript" width="40" height="40"/>&nbsp;
- 💾 **localStorage** (panier côté client)

---

## Prérequis

- **Node.js** 18+ recommandé
- Un serveur **PocketBase** en local (ou distant) avec une collection `products`
  - Champs suggérés : `title` (text), `image` (url/file), `description` (text), `price` (number)

---

## Installation

```bash
# Cloner le projet
git clone https://github.com/<ton-user>/<ton-repo>.git
cd <ton-repo>

# Installer les dépendances
npm install

# Lancer en dev
npm run dev
