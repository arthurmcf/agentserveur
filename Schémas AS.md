[toc]

# Fonctionnement technique AS



## Storage Service ( MàJ du cache)

```mermaid
sequenceDiagram  
 Participant MCF as API MyCompanyFiles
 Participant AS as Agent Serveur (AS)
 loop Storage Service lancé 1 x par heure
 AS->>MCF: WS  StorageList <br> Récupère les clients avec la liste des attributs utilisés dans le fichier de mapping
 MCF->>AS: 
 AS-->>AS:  Met à Jour le cache
 end
```

## Publisher



~~~mermaid
sequenceDiagram  
 Participant MCF as API MyCompanyFiles
 Participant PL as Publisher (AS)
 Participant GED as GED du cabinet
 loop Vérifie toutes les minutes les nouveaux évènements (mapping : "sleep")
 PL-->>GED: Récupère la liste des derniers évènements
 GED-->>PL: 
   PL-->>PL: Enlève de la liste les évènement correspondants à des clients absents du registre
   PL-->>PL: Ajoute dans la fil d'attente les évènement vérifiant 1+ règles de mapping
   Note left of PL: Add In Queue - (avec handler de l'action)
 end
loop Traitement de la fil d'attente - Cas Upload
  PL-->>GED: Récupère  le fichier de la fil d'attente 
   GED-->>PL: 
  PL-->>PL: Calcul le MD5 du fichier local
  PL->>MCF: WS FileInfo <br> Vérifie si le fichier existe sur MCF et récupère son MD5
  MCF->>PL: 
  PL-->>PL: Si fichier existe pas ou MD5 != du fichier local
 PL->>MCF: WS FileUpload <br> Envoie le fichier à l'emplacement voulu
  Note left of PL: Uploading
  PL-->>PL: Vérification si action en cas de succès et l'exécute
 end
 loop MàJ registe toutes les heures
 PL->>MCF: WS  StorageList <br> Récupère les clients avec la liste des attributs utilisés dans le fichier de mapping
 MCF->>PL: 
 PL-->>PL:  MàJ le registe
 end
~~~

## Downloader

```mermaid
sequenceDiagram  
 Participant MCF as API MyCompanyFiles
 participant AS as Downloader
 Participant GED as Serveur du cabinet
 loop Vérifie toutes les x minutes les nouveaux évènements
 AS->>MCF: API FileSync v2 <br> Récupère les n derniers évènements depuis dernier LogId (NextLogId)
 MCF->>AS: 
 AS-->>AS: Vérifie si correspondent à une règle de mapping
 AS-->>AS: Vérfie si le storage est dans le cache
 AS-->>AS: Ajoute l'action dans la fil d'attente
   Note left of AS: Add in Queue : DOWNLOAD
 AS-->>AS:  MàJ le LogId par celui du dernier évènement du précédent FilSync<br mais empêche un nouveau FileSync si file d'attente atteint limite
 end
loop Traitement de la fil d'attente
 AS-->>AS: Regroupement en paquet 1>n<50 fichiers d'un total de 10mo si possible (non zippé)
 AS->>MCF: API FileDownload <br> Télécharge les fichiers du paquet en .ZIP
 MCF->>AS: 
 AS-->>AS: Supprime les fichiers de la fil d'attente
 AS-->>AS: Vérifie que les fichiers demandés sont bien présents (Erreur File not present in ZIP)
 AS-->>AS: Dézip les fichiers
 AS-->>GED: Déplace les fichiers sur le serveur du cabinet
 Note left of AS: SUCCESSFUL DOWNLOAD
 AS-->>AS: Vérifie si action en cas de succès et l'exécute <br> Exemple DELETE
 AS->>MCF: API FileDelete <br> Supprime le fichier sur MCF  
  Note left of AS: SUCCESSFUL DELETE
 end
```


## Lien GI 

```mermaid
sequenceDiagram
 Participant GI as GI du cabinet
 participant AS as Lien GI
 Participant MCF as API MyCompanyFiles
 loop Toutes les x minutes
 	AS->>GI: Vérifie les nouveaux évènements
 	GI->>AS: 
    AS-->>AS: Vérifie si des évènements correspondent à une règle de mapping
 alt Une règle de mapping est détecté
    AS->>GI: Récupère l'ensemble des informations correspondant à ce client
    GI->>AS: 
    AS-->>AS: Vérifie si le client existe dans le registre
 else Client à créer et absent du registre
 	AS->>MCF: API StorageAdd <br> Création du client avec l'ensemble des éléments présent sur la GI
 	MCF->>AS: 
 else Client à modifier et présent dans le registre MCF
 	AS->>MCF: API StorageEdit <br> Modification du client avec l'ensemble des éléments présent sur la GI <br> Mode Sync = Ecrase les données par celles envoyées <br> Mode Add = Ajout et modification, mais pas suppression de champ.
 	MCF->>AS: 
 else Client à supprimer et présent le registre MCF
  	AS->>MCF: API StorageDelete <br> Suppression et desaffactions du client
  	MCF->>AS: 
 end
 end
 
 loop MàJ toutes les 24h la liste des clients sur MCF
 	AS->>MCF: WS StorageList <br> Récupère la liste des couples StorageID/Storage.name/CodeCLient 
 	MCF->>AS: 
 end
```
