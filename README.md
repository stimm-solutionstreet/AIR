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
ConOps Section 5.2: Operational Systems (Systems of Record)
```mermaid
%%{init: { "flowchart": { "subGraphTitleMargin": { "top": 0, "bottom": 10 } } } }%%

flowchart LR

subgraph HR_Domain["HR & Payroll Domain"]
    HRUsers[HR Users]
    Dayforce[Dayforce<br/>HR & Payroll<br/>System of Record]
end

subgraph Finance_Domain["Finance Domain"]
    FinUsers[Finance Users]
    D365F[Dynamics 365 Finance<br/>Financial System of Record]
end

subgraph Engagement_Domain["Customer & Grant Engagement"]
    GrantUsers[Program & Grant Users]
    D365CE[Dynamics 365 Customer Engagement<br/>Engagement System of Record]
end

HRUsers --> Dayforce
FinUsers --> D365F
GrantUsers --> D365CE

Dayforce --- APIs1[APIs for Integration]
D365F --- APIs2[APIs for Integration]
D365CE --- APIs3[APIs for Integration]
```
ConOps Section 5.3: Integration and Synchronization Layer
```mermaid
flowchart LR
    %% Styles
    classDef sor fill:#e8f0fe,stroke:#4a6fa5,stroke-width:1.5px,color:#111;
    classDef integ fill:#fff4d6,stroke:#b58900,stroke-width:1.5px,color:#111;
    classDef control fill:#eef7e7,stroke:#5b8c5a,stroke-width:1.5px,color:#111;
    classDef note fill:#f7f7f7,stroke:#888,stroke-dasharray: 4 3,color:#111;

    %% Systems of Record
    subgraph SOR["Operational Systems (Systems of Record)"]
        CE["Dynamics 365 CE<br/>Customer / Grant Engagement"]
        FIN["Dynamics 365 Finance<br/>Financial System of Record"]
        DAY["Dayforce<br/>HR & Payroll"]
    end

    %% Integration Layer
    subgraph INT["Integration and Synchronization Layer"]
        DW["Native Dual-Write<br/>Selected Shared Entities"]
        API["Governed API Integration<br/>Payroll Journal Transmission"]
        ORCH["Integration Orchestration & Validation"]
        MON["Monitoring, Logging,<br/>Alerts, Failure Handling"]
    end

    %% Main sync paths
    CE <--> |Near-real-time sync<br/>customers, projects, awards,<br/>selected master / operational entities| DW
    DW <--> FIN

    DAY --> |Balanced payroll journals| API
    API --> FIN

    %% Integration controls
    DW --> ORCH
    API --> ORCH
    ORCH --> MON

    %% Guardrails / principles
    BR1["Business rules remain inside<br/>systems of record"]:::note
    BR2["No embedded payroll or financial<br/>calculation logic in integration layer"]:::note
    BR3["Managed inbound / outbound<br/>interfaces only"]:::note

    ORCH --- BR1
    ORCH --- BR2
    MON --- BR3

    %% Classes
    class CE,FIN,DAY sor;
    class DW,API integ;
    class ORCH,MON control;
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

ConOps Section 6.2: Data Flow
```mermaid
%%{init: { "flowchart": { "subGraphTitleMargin": { "top": 0, "bottom": 10 } } } }%%

flowchart LR
    %% Styles
    classDef ops fill:#EAF2FF,stroke:#4E79A7,stroke-width:1.5px,color:#111;
    classDef integ fill:#FFF3D6,stroke:#C58A00,stroke-width:1.5px,color:#111;
    classDef data fill:#E8F6EC,stroke:#4F8A5B,stroke-width:1.5px,color:#111;
    classDef ext fill:#F4EAFE,stroke:#8A63B8,stroke-width:1.5px,color:#111;
    classDef note fill:#F7F7F7,stroke:#888,stroke-dasharray: 4 3,color:#111;

    %% Operational systems
    subgraph OPS["Systems of Record"]
        DAY["Dayforce<br/>HR & Payroll"]:::ops
        FIN["Dynamics 365 Finance<br/>Finance & Grant Accounting"]:::ops
        CE["Dynamics 365 CE<br/>Customer / Grant Engagement"]:::ops
    end

    %% Integration / sync
    subgraph INT["Operational Synchronization"]
        DW["Dual-write<br/>selected shared entities"]:::integ
        PAY["Governed payroll integration<br/>journals / payroll-related financial data"]:::integ
    end

    %% Data platform
    subgraph EDP["Enterprise Data Platform"]
        ING["Controlled ingestion pipelines<br/>batch / incremental / API"]:::data
        LAKE["Microsoft Fabric Lakehouse<br/>governed analytical copies<br/>historical retention"]:::data
    end

    %% Analytics
    subgraph ANA["Reporting and Analytics"]
        BI["Power BI / governed semantic models<br/>cross-domain reporting & analytics"]:::data
    end

    %% External operational interfaces
    subgraph EXT["External Operational Integrations"]
        BANK["Banks / financial institutions"]:::ext
        VEND["Vendors / partner portals"]:::ext
        REG["Regulatory / grantor systems"]:::ext
    end

    %% Operational synchronization flows
    CE <--> DW
    DW <--> FIN
    DAY --> PAY --> FIN

    %% Analytical ingestion flows
    DAY --> ING
    FIN --> ING
    CE --> ING
    ING --> LAKE --> BI

    %% External operational flows
    FIN <--> BANK
    FIN <--> VEND
    FIN <--> REG

    %% Notes / constraints
    N1["Operational sync supports business processes,<br/>not analytics"]:::note
    N2["Reporting runs against the analytics platform,<br/>not operational systems"]:::note
    N3["Analytical workloads are separated to protect<br/>transaction performance"]:::note

    PAY -..- N1
    BI -..- N2
    LAKE -..- N3
```
