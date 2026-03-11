- 
* View system status: **rpm-ostree status**
* Displays current deployments, layered packages, and pending updates.
* Update the system: **rpm-ostree upgrade**
* Layer a package: **rpm-ostree install <package-name>**
Remove a layered package: **rpm-ostree uninstall <package-name>**
Removes a previously layered RPM.
Rollback to previous version: **rpm-ostree rollback**
Reverts the OS to the state it was in before the last change.
Rebase to Fedora 43: **rpm-ostree rebase fedora:fedora/43/x86_64/silverblue**
Switches the base system to a different version or branch. [2, 5, 9, 10, 11] 

Application Management (flatpak)
* Search for an app: **flatpak search <name>**
* Install an app: **flatpak install flathub <app-id>**
* Example: **flatpak install flathub org.mozilla.firefox.**
* Update all apps: **flatpak update**
* Remove an app: **flatpak uninstall <app-id>**
* List installed apps: **flatpak list**

Development & Containers (toolbox)
Since you shouldn't install development tools on the base system, use containers. [1, 19] 

* Create a new container: **toolbox create**
* Creates a Fedora-based container where you can use standard dnf commands.
* Enter the container: **toolbox enter**
* Opens a shell inside the container where you have full read/write access to install tools.
* Run a command inside: **toolbox run <command>**
* Executes a specific command without entering the shell manually.
* List containers: **toolbox list.**

General Navigation & Maintenance
Standard Linux commands work for managing your /home directory. [9, 24] 

* List files: **ls -l.**
* Change directory: cd <path>.
* Check disk space: **df -h.**
* Check memory usage: **free -m.**
* Run with admin privileges: **sudo <command>.**

Are you looking for specific commands to rebase from an older version of Silverblue, or do you need help setting up a development environment?

