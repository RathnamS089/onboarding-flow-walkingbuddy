# onboarding-flow-walkingbuddy
```mermaid
graph TD
    A[Start: Sign Up] --> B{Identity Gate}
    B -->|Phone OTP| C[Verification: ID Scan]
    C -->|Approved| D[Profiling: Set Pace/Availability]
    D --> E[Permission: Enable Location/Fuzzy]
    E --> F[Activation: Accept Code of Conduct]
    F --> G{State: LIVE}
    G --> H[Redirect to Home Screen]

    subgraph Backend_Actions
    B_Action[Auth Service: JWT/Postgres]
    C_Action[Verification Service: is_verified=true]
    D_Action[Profile Service: User_Preferences]
    E_Action[Location Service: Redis GEO Index]
    F_Action[Event Trigger: set reliability_score]
    end

    B --- B_Action
    C --- C_Action
    D --- D_Action
    E --- E_Action
    F --- F_Action

    style G fill:#f96,stroke:#333,stroke-width:2px
    style H fill:#9f9,stroke:#333,stroke-width:2px ```




# Walking Buddy: Onboarding Architecture

## 1. Onboarding Flow Summary
| Phase | User Action | Backend Architecture | State |
| :--- | :--- | :--- | :--- |
| **Identity Gate** | Sign up/in via Phone OTP | **Auth Service**: JWT Auth + PostgreSQL `Users` table check | PENDING |
| **Verification** | Perform ID scan | **Verification Microservice**: 3rd-party API trigger; DB `is_verified` update | VETTED |
| **Profiling** | Select Pace & Availability | **Profile Service**: Write to `User_Preferences` (PostgreSQL) | VETTED |
| **Permission** | Grant Location Access | **Location Service**: Initialize client settings; **Redis GEO** push | VETTED |
| **Activation** | Accept Code of Conduct | **Event Trigger**: Set `reliability_score` (1.0); Redirect to Home | LIVE |

