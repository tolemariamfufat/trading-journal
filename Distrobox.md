1. Creating and Entering
Action,Command
Create (Fedora),distrobox create --name fedora-box --image fedora:40
Create (Ubuntu),distrobox create --name ubuntu-box --image ubuntu:22.04
Enter,distrobox enter fedora-box
Exit,exit (type this while inside the container)
2. Checking Status
distrobox list
**List downloaded images (Blueprints):** 
    ```bash
    podman images
    ```
### 3. Removal & Cleanup
To delete a container and free up space.

*   **Step 1: Stop the container** (Turn it off)
    ```bash
    distrobox stop name-of-box
    ```
*   **Step 2: Remove the container** (Delete the "room")
    ```bash
    distrobox rm name-of-box
    ```
*   **The "Fast" Way** (Stops and removes in one command):
    ```bash
    distrobox rm -f name-of-box
    ```
*   **Delete the Image** (Delete the "blueprint" to save disk space):
    ```bash
    podman rmi image-name:latest
    ```

### 4. Advanced "Power User" Commands
*   **Export an App:** (Run this **inside** the container to add an app to your KDE menu)
    ```bash
    distrobox-export --app app-name
    ```
*   **Upgrade everything:** (Updates all your containers at once)
    ```bash
    distrobox upgrade --all
    ```

---

### 💡 Quick Tip for your Notes:
Remember that **Files** (Documents, Downloads) stay forever, but **Apps** installed via `apk`, `dnf`, or `apt` are deleted when you run the `distrobox rm` command.

Since you're all set with the basics, what's your next move? Setting up a Fedora box for your trading tools, or maybe trying out an Ubuntu container?