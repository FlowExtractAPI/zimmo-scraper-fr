# 🏠 Zimmo Scraper

**Extrayez les annonces immobilières belges de zimmo.be.**

Collez simplement une URL de recherche zimmo.be directement depuis votre navigateur. Le scraper récupère la fiche complète de chaque annonce et stocke la réponse brute de l'API — vous obtenez ainsi chaque champ exposé par zimmo.be : prix, certificats EPC, statut d'inondation, informations d'urbanisme, revenu cadastral, contacts d'agence, images, documents et bien plus encore.

---

## 🌍 Pris en charge

| Plateforme | Domaine | Langues |
|------------|---------|---------|
| Zimmo Belgique | zimmo.be | Néerlandais (`/nl/zoeken/`), Français (`/fr/rechercher/`) |

> ⚠️ **Les URL internationales** (`/international/...`) ne sont pas encore prises en charge. Elles seront ignorées et signalées dans le dataset avec une notice explicative.

---

## 🚀 Démarrage rapide

### Basique : 10 résultats depuis une seule recherche

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/fr/rechercher/?search=eyJmaWx0ZXIiOnsic3RhdHVzIjp7ImluIjpbIkZPUl9TQUxFIl19LCJwbGFjZUlkIjp7ImluIjpbMTExOH19fX0=" }
  ],
  "maxResults": 10
}
```

### Plusieurs URL — différentes régions ou ensembles de filtres

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/nl/zoeken/?search=eyJmaWhhx0...." },
    { "url": "https://www.zimmo.be/fr/rechercher/?search=eyJmaW2g4..." }
  ],
  "maxResults": 50,
  "proxyConfiguration": {
    "useApifyProxy": true
  }
}
```

### Résultats illimités (limités par le budget si défini)

```json
{
  "startUrls": [
    { "url": "https://www.zimmo.be/fr/rechercher/?search=eyJmaWx0ZXIiOnsic3Rh....." }
  ],
  "maxResults": 0
}
```

---

## 📋 Paramètres d'entrée

| Paramètre | Type | Défaut | Description |
|-----------|------|--------|-------------|
| `startUrls` | array | | Une ou plusieurs URL de recherche zimmo.be. Copiez directement depuis votre navigateur après avoir appliqué vos filtres. |
| `maxResults` | integer | `10` | Nombre maximum d'annonces à extraire **par URL**. Définissez `0` pour illimité. |
| `proxyConfiguration` | object | Sans proxy | Paramètres du proxy Apify. Recommandé pour les grandes extractions afin d'éviter le rate limiting. |

### Comment obtenir une URL de départ ?

1. Rendez-vous sur [zimmo.be](https://www.zimmo.be) et appliquez vos filtres (type, localisation, prix, EPC, etc.)
2. Copiez l'URL complète depuis votre navigateur
3. Collez-la dans `startUrls`

---

## 📤 Sortie

Chaque élément du dataset est la **réponse brute de l'API de détail** pour une annonce, avec deux champs ajoutés :

| Champ | Description |
|-------|-------------|
| `sourceUrl` | L'URL de recherche dans laquelle cette annonce a été trouvée |
| `scrapedAt` | Horodatage ISO du moment où elle a été extraite |

### Exemple d'enregistrement de sortie (condensé)

```json
{
  "id": "e36e00c9-907d-4f3c-b666-6d9e21793a96",
  "estate": {
    "type": "HOUSE",
    "subType": "ONE_FAMILY_HOUSE",
    "status": "FOR_SALE",
    "price": { "unit": "EUR", "value": 375000 },
    "certificate": {
      "epcCertificate": {
        "energyLabel": "B",
        "epcValue": { "unit": "kWh/m²", "value": 147 }
      }
    },
    "flooding": { "floodingValues": ["..."] },
    "location": {
      "street": "Duiventorenstraat",
      "streetNumber": "10",
      "postalCode": "8310",
      "locality": { "fr": "Assebroek" },
      "coordinates": { "latitude": 51.187863, "longitude": 3.24166 }
    }
  },
  "dealer": {
    "name": "Waeyler",
    "email": "contact@exemple.be",
    "phoneNumber": "050373137"
  },
  "sourceUrl": "https://www.zimmo.be/fr/rechercher/?search=...",
  "scrapedAt": "2026-04-13T23:37:57.746Z"
}
```

---

## 📊 Vues du dataset

Cinq vues préconfigurées sont disponibles dans l'interface dataset d'Apify :

| Vue | Objectif |
|-----|----------|
| **Aperçu** | Résumé rapide : ID, type, prix, état, chambres/salles de bain, surface, localisation, image |
| **Détaillé** | Données complètes : certificats, urbanisme, inondation, agencement, contacts d'agence |
| **Contacts** | Nom de l'agence, e-mail, téléphone, mobile, site web et localisation par annonce |
| **Prix** | Prix, revenu cadastral, surfaces, vendeur, date de disponibilité pour l'analyse de marché |
| **Énergie & Juridique** | Certificat EPC, certificat électrique, valeurs d'inondation, statut d'urbanisme, scores P/G |

---

## 💰 Tarification

Facturé par résultat immobilier enregistré avec succès dans le dataset.

| Niveau | Prix par résultat |
|--------|-------------------|
| FREE | 0,008 $ |
| BRONZE | 0,004 $ |
| SILVER | 0,0035 $ |
| GOLD | 0,003 $ |

Si vous configurez un **budget**, le scraper calcule automatiquement combien de résultats il peut se permettre avant de démarrer et plafonne `maxResults` en conséquence — aucun appel API gaspillé.

---

## ⚡ Performances

- **~200 ms de délai** entre les appels de détail par annonce (crawl respectueux)
- **~500 ms de délai** entre les pages de recherche
- **100 annonces** ≈ 1–2 minutes selon le réseau et le proxy
- Les résultats sont poussés et facturés par lots de 10

### Grandes extractions

Pour les extractions dépassant quelques centaines de résultats, l'activation du proxy Apify est fortement recommandée :

```json
"proxyConfiguration": {
  "useApifyProxy": true
}
```

---

## ⚠️ Remarques importantes

- Seules les annonces belges sont prises en charge. Les URL internationales sont automatiquement détectées et ignorées.
- Les utilisateurs doivent se conformer aux Conditions d'utilisation de zimmo.be et aux lois applicables en matière de protection des données (RGPD).

**Le créateur de cet actor n'est pas responsable de l'utilisation faite des données extraites.**

---

## 💬 Support & Contact

- 🌐 **Site web** : [flowextractapi.com](https://flowextractapi.com)
- 📧 **E-mail** : [flowextractapi@outlook.com](mailto:flowextractapi@outlook.com)
- 🙋 **Profil Apify** : [FlowExtract API](https://apify.com/dz_omar?fpr=smcx63)
- 💬 **GitHub** : [FlowExtractAPI](https://github.com/FlowExtractAPI)
- 💼 **LinkedIn** : [flowextract-api](https://www.linkedin.com/in/flowextract-api/)
- 🐦 **Twitter** : [@FlowExtractAPI](https://x.com/@FlowExtractAPI)
- 📱 **Facebook** : [flowextractapi](https://www.facebook.com/flowextractapi)

---

## 🌟 Autres actors de FlowExtract API

**[PropertyFinder Scraper](https://apify.com/dz_omar/propertyfinder-scraper?fpr=smcx63)** — EAU, Arabie Saoudite, Bahreïn, Égypte, Qatar

**[Idealista Scraper](https://apify.com/dz_omar/idealista-scraper?fpr=smcx63)** — Immobilier espagnol

**[YouTube Transcript & Metadata Extractor](https://apify.com/dz_omar/youtube-transcript-metadata?fpr=smcx63)**

**[AI Contact Intelligence Extractor](https://apify.com/dz_omar/ai-contact-intelligence?fpr=smcx63)**

---

**Prêt à extraire les données de zimmo.be ?** [Commencez maintenant avec Zimmo Scraper !](https://apify.com/dz_omar/zimmo-scraper?fpr=smcx63)
