add(vscode):Permission Denied for Error updating or Uninstalling, Permission Deny

Go to C:\Users\USERNAME\AppData\Local\Temp\vscode-stable-user-x64
and to this folder, make sure in properties, is not read-only please, deselect this option, then use the uninstaller

This does not works

Run the update setup file, with a name like "CodeSetup-stable-...exe", in the following location:

C:\Users\USERNAME\AppData\Local\Temp\vscode-update-system-x64

Replace USERNAME with your username. And make sure VS Code is closed.

The same problem happened to me as well. It was because I installed VS Code in another directory other than the default location. The problem happened because after downloading the update in the Temp directory, VS Code tried to locate the previous installation in the default location to update it. As it failed, the update setup file remained with the temporary files. The problem was solved by manually installing the update.

