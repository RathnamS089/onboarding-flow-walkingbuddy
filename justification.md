
## 1. Onboarding Flow Specification

| Phase | User Action | Backend Architecture | State |
| :--- | :--- | :--- | :--- |
| **Identity Gate** | Sign up/in via Phone OTP | **Auth Service**: JWT Auth + PostgreSQL `Users` table check | PENDING |
| **Verification** | Perform ID scan | **Verification Microservice**: 3rd-party API trigger; DB `is_verified` update | VETTED |
| **Profiling** | Select Pace & Availability | **Profile Service**: Write to `User_Preferences` (PostgreSQL) | VETTED |
| **Permission** | Grant Location Access | **Location Service**: Initialize client settings; **Redis GEO** push | VETTED |
| **Activation** | Accept Code of Conduct | **Event Trigger**: Set `reliability_score` (1.0); Redirect to Home | LIVE |

---

## 2. Technical Justification

### State Management
*   **Workflow Logic**: During onboarding, we maintain a `user_status` flag. 
*   **Safety**: A user is marked `PENDING` until verification is complete, ensuring unvetted users never appear on the live map.

### Security Gatekeeping
*   **Middleware Protection**: The `is_verified` flag acts as a critical security gate.
*   **API Enforcement**: The Home Screen's `GET /nearby-users` endpoint will reject any requests from users whose status is not `LIVE` or whose `is_verified` flag is `false`.

### Redis Readiness
*   **Performance**: Upon completion of the permission step, we immediately push user metadata into **Redis GEO**.
*   **Efficiency**: This pre-indexing ensures that the moment a user reaches the Home Screen, they are instantly discoverable for matching without requiring an expensive database query.

