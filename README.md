# Mode-loader-for-PZ
This mod allows you to bypass the long mod initialization process in Project Zomboid.
Just start .bat file and press Enter

For work need to be instaled Python

A lightweight utility for automatic Steam Workshop mod synchronization for a Project Zomboid dedicated server.

🛑 The Problem (Why do you need this?)
If you've ever hosted a dedicated server in Project Zomboid through the game client, you know the pain: server startup can take hours or even days.

Why does this happen?
The game creates an isolated steamapps folder inside the server directory (...\common\ProjectZomboid\steamapps\). When the server starts, it looks for mods there. Since it doesn't find them, it attempts to redownload and re-initialize every single mod from the Steam Workshop, completely ignoring the fact that the game client has already downloaded these exact same mods into a different folder (...\Steam\steamapps\workshop\...).

💡 The Solution
PZ Workshop Sync is a lightweight tool that acts as a "smart mirror." It doesn't mess with game files; it simply copies the already downloaded mods from the client folder to the server folder and synchronizes the .acf manifest file so the server understands that the mods are already downloaded.

As a result, server startup time is reduced from hours to just a few minutes.

⚙️ How It Works (Under the Hood)
The tool performs three main steps on every run:

1. Mod Folder Synchronization
The utility scans the client's Workshop folder (...\content\108600) and compares its contents with the server folder.

If the server is missing a folder for a specific mod, it is copied entirely (shutil.copytree).

If the folder already exists, the utility compares the modification time (mtime) and file count. By default, existing mods are not overwritten (to avoid breaking the server) unless the --overwrite flag is passed.

2. .acf File Synchronization (The Secret Sauce)
This is the most crucial step. The appworkshop_108600.acf file is a Steam manifest that records which mods and versions have already been downloaded.
The utility locates this file in the client's Steam folder and copies it to the server directory (creating a .bak backup). Thanks to this file, the game engine "believes" all mods are already present and skips the endless loading phase.

3. Safety & Logging
The script never deletes source files (it only reads from the source and writes to the target).

All actions are printed to the console and duplicated in the pz_sync.log file.

Errors (e.g., if Steam has locked a file) are caught, and the script continues processing the remaining mods instead of crashing with a fatal error.
