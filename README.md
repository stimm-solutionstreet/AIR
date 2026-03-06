# AIR
This is a location for editing Mermaid files for the ConOps and other documents.

Systems of Record → Enterprise Data Platform → Reporting and Analytics 
```mermaid
flowchart LR

SOR["Systems of Record"]
EDP["Enterprise Data Platform"]
REP["Reporting and Analytics"]

SOR --> EDP
EDP --> REP
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
graph LR
    Raw[Source Data] --> Bronze
    
    subgraph Lakehouse
        Bronze[(Bronze - Raw Data)]
        Silver[(Silver - Filtered/Cleaned)]
        Gold[(Gold - Aggregated/Business)]
    end
    
    Bronze -->|ETL/Transformation| Silver
    Silver -->|Aggregation| Gold
    
    Gold --> BI[BI Dashboards]
    %% Gold --> ML[ML Models]
    
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
