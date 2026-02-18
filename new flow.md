```mermaid
flowchart LR
   A[Customer Network]
   B[Internet]
   C[NIE]
   C2[DIBS]
   D[SAP Internet Router pair]
   E[PE Routers]
   F[Load Balancer]
   G[CGS]
   H[Customer VM]

   A --> B
   B --> C & C2
   C --> D
   C2 --> D
   D --> E --> F --> G --> H

```
