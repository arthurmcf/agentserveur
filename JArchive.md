# JArchive 

## Modèle économique

```mermaid
graph TD
Q1{Règles simples ?}-->|OUI| F
F(Formulaires<br>x Offre low cost x) --> C(Connecteurs API) & P(Publisher)
Q1 -->|NON| Q2{OnPremise<br> ou SaaS ?}
Q2 -->|SaaS| R1(???) --> C
Q2 -->|Serveur| M(Mapping XML) --> P
```

## Vulgarisation technique

```mermaid
sequenceDiagram  
 Participant JA as JArchive via MCF
 Participant MCF as MyCompanyFiles
 Participant GED as GED du cabinet
 Participant PROD as Production (comptable,sociale,etc)
 Participant OCR as Autres outils
 loop GEDs alimentées par différentes sources
 PROD-->>GED: Alimente une des GED du cabinet
 OCR -->>GED: Alimente une des GED du cabinet
 end
 rect rgb
 loop JArchive alimentée par les GEDs du cabinet 
 GED-->>MCF: Récupère les derniers évènementes de la GED 
 MCF->>MCF: Vérifie si les fichiers correspondent à une règle de mapping
 MCF-->>GED: Récupère les fichiers et les métadonnées voulues
 GED-->>MCF: 
 MCF-->>JA: Envoie les fichiers avec les métadonnées associées
 JA-->>MCF: Supprime le fichier de MCF
 end
 end
```

- [ ] 