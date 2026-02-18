flowchart LR
%% ===========================
%%     STYLE DEFINITIONS
%% ===========================
classDef customer fill:#cce5ff,stroke:#004085,stroke-width:1.5px,color:#000,font-weight:bold;
classDef internet fill:#e2e3e5,stroke:#6c757d,stroke-width:1.5px,color:#000,font-weight:bold;
classDef sap fill:#d4edda,stroke:#155724,stroke-width:1.5px,color:#000,font-weight:bold;
classDef endvm fill:#fff3cd,stroke:#856404,stroke-width:1.5px,color:#000,font-weight:bold;
classDef title fill:#343a40,color:#fff,stroke:#343a40;

%% ===========================
%%     FLOWCHART CONTENT
%% ===========================

%% Title Node
T([SAP Network Ingress Flow]):::title

%% Customer Side
subgraph Customer_Side[Customer Side]
    A[Customer Network]:::customer
end

%% Internet Zone
subgraph Internet_Zone[Internet Zone]
    B[Internet]:::internet
    C[NIE / DIBS]:::internet
end

%% SAP Environment
subgraph SAP_Environment[SAP Environment]
    D[SAP Internet Router Pair]:::sap
    E[PE Routers]:::sap
    F[Load Balancer]:::sap
    G[CGS]:::sap
    H[Customer VM]:::endvm
end

%% Flow Connections
T --> A
A --> B --> C --> D --> E --> F --> G --> H
