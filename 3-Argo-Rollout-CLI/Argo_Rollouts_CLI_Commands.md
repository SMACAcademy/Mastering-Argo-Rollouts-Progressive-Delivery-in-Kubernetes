
# Argo Rollouts CLI Commands

A comprehensive guide to using the Argo Rollouts CLI for managing Kubernetes Rollouts.

## List Rollouts

- **List all rollouts in a namespace:**

  ```shell
  kubectl argo rollouts list rollouts -n <namespace>
  ```

- **List rollouts across all namespaces:**

  ```shell
  kubectl argo rollouts list rollouts --all-namespaces
  ```

## Get Rollout Details

- **Get detailed information about a specific rollout:**

  ```shell
  kubectl argo rollouts get rollout <rollout-name> -n <namespace>
  ```

## Update a Rollout

- **Apply changes to a rollout:**

  ```shell
  kubectl apply -f <rollout-file.yaml>
  ```

- **Update the image of a rollout:**

  ```shell
  kubectl argo rollouts set image <rollout-name> <container-name>=<new-image> -n <namespace>
  ```

## Pause and Resume Rollout

- **Pause a rollout:**

  ```shell
  kubectl argo rollouts pause <rollout-name> -n <namespace>
  ```

- **Resume a rollout:**

  ```shell
  kubectl argo rollouts resume <rollout-name> -n <namespace>
  ```

## Promote a Rollout

- **Manually promote a rollout:**

  ```shell
  kubectl argo rollouts promote <rollout-name> -n <namespace>
  ```

## Abort a Rollout

- **Abort a rollout update:**

  ```shell
  kubectl argo rollouts abort <rollout-name> -n <namespace>
  ```

## Delete a Rollout

- **Delete a rollout:**

  ```shell
  kubectl delete rollout <rollout-name> -n <namespace>
  ```

Replace `<namespace>`, `<rollout-name>`, `<rollout-file.yaml>`, `<container-name>`, and `<new-image>` with your specific details as needed.
