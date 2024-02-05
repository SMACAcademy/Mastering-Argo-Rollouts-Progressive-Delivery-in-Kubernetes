# Argo Rollouts Operational Commands

This document lists the operational commands for managing deployments with Argo Rollouts, including commands for deployment, monitoring, and management.

## Deployment and Update Commands

1. **Create (Apply Rollout Configuration)**
   ```bash
   kubectl apply -f <rollout-definition.yaml>
   ```

2. **Promote (Advance to the Next Step)**
   ```bash
   kubectl argo rollouts promote <rollout-name>
   ```

3. **Promote Full (Complete the Rollout)**
   ```bash
   kubectl argo rollouts promote <rollout-name> --full
   ```

4. **Monitor (Watch Rollout Status)**
   ```bash
   kubectl argo rollouts get rollout <rollout-name> --watch
   ```

## Management and Control Commands

5. **Abort (Stop and Revert to Previous Stable Version)**
   ```bash
   kubectl argo rollouts abort <rollout-name>
   ```

6. **Retry (Restart the Rollout from Last Failure Point)**
   ```bash
   kubectl argo rollouts retry <rollout-name>
   ```

7. **Pause (Temporarily Stop the Rollout)**
   ```bash
   kubectl argo rollouts pause <rollout-name>
   ```

8. **Resume (Continue a Paused Rollout)**
   ```bash
   kubectl argo rollouts resume <rollout-name>
   ```

9. **Set Image (Update the Container Image in a Rollout)**
   ```bash
   kubectl argo rollouts set image <rollout-name> <container-name>=<new-image:tag>
   ```

10. **Undo (Rollback to a Previous Revision)**
    ```bash
    kubectl argo rollouts undo <rollout-name>
    ```

11. **List (List all Rollouts in a Namespace)**
    ```bash
    kubectl argo rollouts list rollouts --namespace <namespace>
    ```

12. **History (View the Revision History of a Rollout)**
    ```bash
    kubectl argo rollouts history <rollout-name>
    ```

Each command plays a critical role in the deployment process, providing the tools necessary to implement sophisticated deployment strategies with precision and control.
