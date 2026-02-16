```mermaid
flowchart LR #create a flow diagram
   subgraph Customer_Side
       A[Customer Network]
   end
   subgraph Internet_Zone
       B[Internet]
       C[NIE / DIBS]
   end
   subgraph SAP_Environment
       D[SAP Internet Router]
       E[VPN Router]
       F[Load Balancer]
       G[CGS]
       H[Customer VM]
   end
   A --> B --> C --> D --> E --> F --> G --> H

```
