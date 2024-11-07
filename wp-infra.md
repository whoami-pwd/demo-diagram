```mermaid
graph TB
    subgraph "User Access Layer"
        USERS[Users DE/AT/CH]
        R53[Route 53 - Regional Routing]
    end

    subgraph "Edge & Security Layer"
        CF[CloudFront]
        LE[Lambda@Edge - Image Optimization]
        WAF[AWS WAF]
        CERT[ACM Certificates]
    end

    subgraph "Authentication Layer"
        COG[Amazon Cognito]
        subgraph "SSO Integration"
            SAML[SAML 2.0]
            OIDC[OpenID Connect]
            direction LR
            SAML --> |Healthcare Providers| IDP1[Hospital IdP]
            SAML --> |Medical Staff| IDP2[Medical Association IdP]
            OIDC --> |Patients| SOCIAL[Social Providers]
        end
    end

    subgraph "Frontend Layer"
        S3F[Next.js/Gatsby Frontend - S3]
        SSR[Server-Side Rendering]
        CDN[CDN Cache]
    end

    subgraph "Headless CMS Layer"
        ALB[Application Load Balancer]
        ASG[Auto Scaling Group]
        subgraph "Container Services"
            WP[Headless WordPress API]
            NGINX[NGINX]
            PHP[PHP-FPM]
            REST[REST API]
            GQL[WPGraphQL]
        end
        CB[Circuit Breaker - External APIs]
    end

    subgraph "Data & Cache Layer"
        RC1[Redis Session Cache]
        RC2[ElastiCache - Object Cache]
        AURORA[(Aurora Primary)]
        RR[(Regional Read Replicas)]
    end

    subgraph "Storage Layer"
        EFS[EFS - Shared Storage]
        S3M[S3 - Media Assets]
    end

    USERS-->R53
    R53-->CF
    CF-->LE
    LE-->WAF
    WAF-->COG
    COG-->S3F
    S3F-->REST
    S3F-->GQL
    REST-->WP
    GQL-->WP
    WP-->RC1
    WP-->RC2
    WP-->AURORA
    AURORA-->RR
    WP-->EFS
    WP-->S3M
    WP-->CB
    CB-->|External APIs|POLLEN[Pollen API]
```
