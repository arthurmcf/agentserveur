{:toc}

# Tests du Publisher et Downloader

| Module    | Test  à effectuer                                |
| :-------- | ------------------------------------------------ |
| Publisher | ALL : Ajout d'un mot clé entraine la publication |
|           | GED : Case à cocher entraine la publication      |
|           | Files : Suppression supprime de MC               |

Publisher : 

- GED : Case à cocher entraine publisher
- Files : Couper/coller entraine publication et suppression
- Files : Copier/Coller entraine publication
- All : Déplacement entraine publication
- GED : Modification métadonnées entraine publication
- Files : Ajout d'un mot clé entraine la publication
- All : Nouveau client entraine rescan complet du client
- 

Downloader :

- Téléchargement

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

## V







[^test]: 
