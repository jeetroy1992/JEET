<<<<<<< HEAD
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
       E[VPN Router]
       F[Load Balancer]
       G[CGS]
       H[Customer VM]
   end
   A --> B --> C --> D --> E --> F --> G --> H
```
=======
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
       D[SAP Internet Router pair]
       E[PE Routers]
       F[Load Balancer]
       G[CGS .254]
       H[Customer VM]
   end
   A --> B --> C --> D --> E --> F --> G --> H

```





>>>>>>> b0960fee17d18ba85f8abc3cbb8961dcc4f3debf
