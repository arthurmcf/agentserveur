# Tests du Publisher et Downloader

[toc]

## Process de test

```mermaid
sequenceDiagram
Participant MCF as MyCompanyFiles
Participant S as Systeme de fichier
Participant T as Testeur
T->>S: Dépôt d'un ensemble de fichiers correspondants <br> ou non à des règles de mapping
activate T
T->>S: Modification d'un fichier ne vérifiant <br>pas une règle pour qu'il soit publié
deactivate T
Note over S: DOSSIER D'ENVOI<br>plein
S->>MCF: Publication des fichiers vérifiant les règles de mapping
Note over MCF: DOSSIER DE TRANSIT<br>plein
MCF-->>S: Confirme les publications
S->>S: Suppression des fichiers publiés
Note over S: DOSSIER D'ENVOI<br>vidé partiellement
MCF->>S: Téléchargement des pièces publiées
Note over S: DOSSIER DE RECEPTION<br>plein
S->>MCF: Suppression des pièces téléchargées 
Note over MCF: DOSSIER DE TRANSIT<br>vidé partiellement
T->>S: Vérifie que seuls les fichiers attendus sont présents
activate T
T->>S: Supprime les fichiers présents
T->>MCF: Vérifie que seuls les fichiers attendus sont présents.
T->>MCF: Supprime les fichiers présents
deactivate T
Note over MCF,T: Tests du Publisher et Downloader effectué





```

## Cas à tester

### Publisher : 

- All : 
  - Déplacement entraine publication
  - Nouveau client entraine rescan complet du client

- GED : 
  - Case à cocher entraine publisher
  - Modification métadonnées entraine publication

- Files :
  - Couper/coller entraine publication et suppression
  - Copier/Coller entraine publication
  - Ajout d'un mot clé entraine la publication


### Downloader :

- Téléchargement
- Suppression après téléchargement
- Déplacement après téléchargement
- Tag après téléchargement
