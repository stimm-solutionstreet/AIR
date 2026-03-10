# AIR
This is a location for editing Mermaid files for the ConOps and other documents.

Systems of Record → Enterprise Data Platform → Reporting and Analytics 
```mermaid
%%{init: { "flowchart": { "subGraphTitleMargin": { "top": 60, "bottom": 60 } } } }%%

flowchart LR

SOR["Systems of Record"]
EDP["Enterprise Data Platform"]
REP["Reporting and Analytics"]

SOR --> EDP
EDP --> REP
```
High Level D365 + Fabric reference diagram
```mermaid
flowchart TB

%% Operational Systems
subgraph Operational["Operational Systems<br/>(Systems of Record)"]

%% invisible spacing node to work around Mermaid layout bug see: https://stackoverflow.com/questions/74098079/how-to-prevent-overwritting-elements-when-line-breaks-in-subgraph-titles-in-mer
            I1~~~D365
            class I1 disable;
            classDef disable display:none;

DF[Dayforce<br/>HR & Payroll]
D365[Dynamics 365<br/>Finance + GovCon365]
CE[Dynamics 365 CE / Dataverse<br/>Grants & CRM]
end

%% Integration
subgraph Integration["Data Integration"]
API[APIs / Integration Services]
DW[Dual Write / Data Sync]
PIPE[Data Pipelines / Fabric Data Factory]
end

%% Data Platform
subgraph DataPlatform["Enterprise Data Platform"]
LH[Fabric Lakehouse<br/>OneLake Storage]
WH[Fabric Warehouse / Semantic Models]
end

%% Analytics
subgraph Analytics["Reporting & Analytics"]
PBI[Power BI]
DS[Advanced Analytics / Data Science]
end

%% Operational integration
DF --> API
D365 --> DW
CE --> DW

DW --> API

%% Data ingestion
API --> PIPE
PIPE --> LH

%% Analytics layer
LH --> WH
WH --> PBI
LH --> DS
```

High level target state diagram.
```mermaid
flowchart LR

  subgraph Identity["Identity & Security Layer"]
    Entra["Microsoft Entra ID (SSO)"]
  end

  subgraph Operational["Operational Systems (Systems of Record)"]
    DF["Dayforce<br/>HR & Payroll"]

    subgraph D365["Microsoft Dynamics 365 Platform"]
      CE["Dynamics 365 CE<br/>Customer / Grant Engagement"]
      FIN["Dynamics 365 Finance<br/>(XTIVIA GovCon365)<br/>Financial System of Record"]
    end

  end

  subgraph Documents["Document Management"]
    SP["SharePoint<br/>(D365 Document Store)"]
    DFDOC["Dayforce Documents"]
  end

  subgraph Data["Enterprise Data Platform"]
    FAB["Microsoft Fabric<br/>Lakehouse"]
  end

  subgraph Analytics["Analytics & Reporting"]
    PBI["Power BI"]
  end

  %% Identity
Entra -. "SSO" .-> FIN
Entra -. "SSO" .-> CE
Entra -. "SSO" .-> DF
Entra -. "SSO" .-> FAB
Entra -. "SSO" .-> PBI

  %% Operational sync relationships
  DF <--> FIN
  CE <--> FIN

  %% Documents
  FIN --- SP
  DF --- DFDOC

  %% Data ingestion
  DF --> FAB
  CE --> FAB
  FIN --> FAB
  
  %% Primary reporting path
  FAB --> PBI

  %% Optional operational reporting (direct-to-SOR)
  DF -. "Operational dashboards (limited)" .-> PBI
  CE -. "Operational dashboards (limited)" .-> PBI
  FIN -. "Operational dashboards (limited)" .-> PBI
```
Medalion Architecture
```mermaid
flowchart LR

DF[Dayforce<br/>HR & Payroll]
D365[D365 Finance]
CE[D365 CE / Dataverse]

DF --> Bronze
D365 --> Bronze
CE --> Bronze

subgraph Lakehouse["Fabric Lakehouse (OneLake)"]
    Bronze[(Bronze<br/>Raw/Landing Data)]
    Silver[(Silver<br/>Cleaned/Curated Data)]
    Gold[(Gold<br/>Semantic/Reporting Data)]
end

Bronze -->|Transform/Standardize| Silver
Silver -->|Aggregate/Model| Gold
Gold --> BI[Power BI Dashboards]
Gold -.-> DS[Advanced Analytics/Data Science]

style DS stroke-dasharray: 5, 5;
style Bronze fill:#A97142,stroke:#333
style Silver fill:#BCC6CC,stroke:#333
style Gold fill:#FFD700,stroke:#333
```

Low level target state diagram.
```mermaid
flowchart LR

  subgraph Identity["Identity & Security Layer"]
    Entra["Microsoft Entra ID (SSO)"]
  end

  subgraph Operational["Operational Systems (Systems of Record)"]
    DF["Dayforce<br/>HR & Payroll"]

    subgraph D365["Microsoft Dynamics 365 Platform"]
      CE["Dynamics 365 CE<br/>Customer / Grant Engagement"]
      FIN["Dynamics 365 Finance<br/>(XTIVIA GovCon365)<br/>Financial System of Record"]
      CEDATA[("D365 CE Dataverse")]
    end

  end

  subgraph Documents["Document Management"]
    SP["SharePoint<br/>(D365 Document Store)"]
    DFDOC["Dayforce Documents"]
  end

  subgraph Data["Enterprise Data Platform"]
    FAB["Microsoft Fabric<br/>Lakehouse"]
  end

  subgraph Analytics["Analytics & Reporting"]
    PBI["Power BI"]
  end

  %% Identity
Entra -. "SSO" .-> FIN
Entra -. "SSO" .-> CE
Entra -. "SSO" .-> DF
Entra -. "SSO" .-> FAB
Entra -. "SSO" .-> PBI

  %% Operational sync relationships
  DF <--> FIN
  CE <--> FIN

  %% Documents
  FIN --- SP
  DF --- DFDOC

  %% Data ingestion
  DF --> FAB
  CE --> CEDATA
  CEDATA -- "Fabric Link" --> FAB
  FIN --> FAB
  
  %% Primary reporting path
  FAB --> PBI

  %% Optional operational reporting (direct-to-SOR)
  DF -. "Operational dashboards (limited)" .-> PBI
  CEDATA -. "Operational dashboards (limited)" .-> PBI
  FIN -. "Operational dashboards (limited)" .-> PBI
```

ConOps Section 4: Operational Concept
```mermaid
%%{init: { "flowchart": { "subGraphTitleMargin": { "top": 10, "bottom": 10 } } } }%%

flowchart TB

subgraph Users
HR[HR Users]
FIN[Finance Users]
GRANT[Grant / Program Users]
EXEC[Executives & Analysts]
end

subgraph Systems_of_Record["Operational Systems (Systems of Record)"]
DAY[Dayforce<br/>HR & Payroll]
FINANCE[D365 Finance<br/>Financial System of Record]
CE[D365 Customer Engagement<br/>Grant & Engagement]
end

subgraph Data_Platform["Enterprise Data Platform"]
FABRIC[Microsoft Fabric<br/>Lakehouse]
end

subgraph Reporting
PBI[Power BI<br/>Dashboards & Reporting]
end

HR --> DAY
FIN --> FINANCE
GRANT --> CE

DAY --> FABRIC
FINANCE --> FABRIC
CE --> FABRIC

FABRIC --> PBI
EXEC --> PBI
```

ConOps Section 6: Data Concept
```mermaid
flowchart LR

D365[D365 Finance<br/>Operational Transactions]
CE[D365 CE<br/>Engagement Data]
DF[Dayforce<br/>HR & Payroll]

ARCH[Historical Archival / Data Pipelines]

LH[Fabric Lakehouse<br/>Historical Analytical Repository]

BI[Enterprise Reporting<br/>Power BI]

D365 --> ARCH
DF --> ARCH
CE --> ARCH

ARCH --> LH
LH --> BI
```
