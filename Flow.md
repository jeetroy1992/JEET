```mermaid
flowchart LR
   subgraph Customer_Side
       A[Customer Network]
   end
   subgraph Internet_Zone
       B[Internet]
       C[NIE / DIBS]
   end
   subgraph SAP_Environment
       D[SAP Internet Router]
       E[PE(VPN/WAN Router]
       F[Load Balancer]
       G[CGS]
       H[Customer VM]
   end
   A --> B --> C --> D --> E --> F --> G --> H

```


