
# Argo Rollouts Kubectl Plugin Installation Instructions

To install the Argo Rollouts kubectl plugin, follow these steps. Ensure you have `kubectl` installed on your system before proceeding.

## 1. Download the Argo Rollouts Plugin
Download the latest version of the Argo Rollouts plugin from the [Argo Rollouts releases page on GitHub](https://github.com/argoproj/argo-rollouts/releases). Select the version appropriate for your operating system.

## 2. Make the Plugin Executable
After downloading, make the binary executable. On Unix-like systems (Linux, MacOS), use the command:
```bash
chmod +x /path/to/kubectl-argo-rollouts
```
Replace `/path/to/kubectl-argo-rollouts` with the actual path to the downloaded file.

## 3. Move the Plugin to a Directory in Your PATH
Move the `kubectl-argo-rollouts` binary to a directory in your system's PATH, like `/usr/local/bin` or `~/bin`. For example:
```bash
mv /path/to/kubectl-argo-rollouts /usr/local/bin
```

## 4. Verify the Installation
Verify the installation with:
```bash
kubectl argo rollouts version
```

## 5. Use the Plugin
Use the plugin with commands such as:
```bash
kubectl argo rollouts list rollouts
```

## 6. Updating the Plugin
To update, download the new version and repeat the process.

## 7. Additional Information
For advanced usage, refer to the [official Argo Rollouts documentation](https://argoproj.github.io/argo-rollouts/).
