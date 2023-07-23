# tf2-server
Dedicated Team Fortress 2 server w/ MetaMod:Source, SourceMod and custom SourceMod plugins

*All Windows commands are done through PowerShell; do not use admin!*

## 1) Install SteamCMD

### 1.1) Windows

```PowerShell
New-Item -Path "D:\SteamCMD\" -ItemType directory
Invoke-WebRequest -Uri "https://steamcdn-a.akamaihd.net/client/installer/steamcmd.zip" -OutFile "D:\SteamCMD\steamcmd.zip"
Expand-Archive -LiteralPath "D:\SteamCMD\steamcmd.zip" -DestinationPath "D:\SteamCMD\"
```

### 1.2) Linux

```
sudo groupadd -r steam
sudo useradd -r -m -d /home/steam/ -g steam -s /bin/bash steam
sudo -u steam -i
curl -sqL "https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz" | tar zxvf -
```

## 2) Update SteamCMD

### 2.1) Windows

```
& "D:\SteamCMD\steamcmd.exe +login anonymous +quit"
```

### 2.2) Linux

```Bash
./steamcmd.sh +login anonymous +quit
```

## 3) Install Team Fortress 2 Dedicated Server

### 3.1) Windows

```PowerShell
New-Item -Path D:\TF2Server\ -ItemType directory
& "D:\SteamCMD\steamcmd.exe" +force_install_dir D:\TF2server\ +login anonymous +app_update 232250 +quit
```

### 3.2) Linux

```Bash
mkdir /home/steam/tf2server/
./steamcmd.sh +force_install_dir /home/steam/tf2server/ +login anonymous +app_update 232250 +quit
```

## 4) Configure TF2 server

Create and modify `D:\TF2Server\tf\cfg\server.cfg` on Windows or `/home/steam/tf2server/tf/cfg/server.cfg` on Linux: [server.cfg](https://raw.githubusercontent.com/gresskar/tf2-server/main/tf/cfg/server.cfg)

Create and modify `D:\TF2Server\tf\cfg\motd.txt` on Windows or `/home/steam/tf2server/tf/cfg/motd.txt` on Linux: [motd.txt](https://raw.githubusercontent.com/gresskar/tf2-server/main/tf/cfg/motd.txt)

Create and modify `D:\TF2Server\tf\cfg\mapcycle.txt` on Windows or `/home/steam/tf2server/tf/cfg/mapcycle.txt` on Linux: [mapcycle.txt](https://raw.githubusercontent.com/gresskar/tf2-server/main/tf/cfg/mapcycle.txt)

## 5) Install and configure Metamod:Source and SourceMod

[Metamod:Source](https://www.metamodsource.net/downloads.php?branch=stable)

[SourceMod](https://www.sourcemod.net/downloads.php?branch=stable)

Download, unzip and extract these to the `tf/` folder - the final directory structure should look like this:

```Bash
/home/steam/tf2server/
└── tf
    ├── addons
    │   ├── metamod
    │   ├── sourcemod
    │   ├── metamod.vdf
    │   ├── metamod_x64.vdf
    ├── cfg
    │   ├── metamod
    │   │   ├── sm_warmode_off.cfg
    │   │   ├── sm_warmode_on.cfg
    │   │   ├── sourcemod.cfg
...
```

Make yourself an in-game administrator for `/admin` access by modifying `tf/addons/sourcemod/configs/admins.cfg`:

```
Admins {
    "gresskar"
    {
        "auth"        "steam"
        "identity"    "YOUR_STEAMID3_HERE"
        "flags"       "z"
        "immunity"    "99"
    }
}
```

If you want to add in-game moderators as well, modify `tf/addons/sourcemod/configs/admins.cfg`:

```
...
    "friend"
    {
        "auth"        "steam"
        "identity"    "THEIR_STEAMID3_HERE"
        "flags"       "abcdefghijklmn"
        "immunity"    "88"
    }
...
```

To make normal players be able to vote, modify `tf/addons/sourcemod/configs/admin_groups.cfg` and make sure the "Default" group has the "k" flag:

```
Groups {
    "Default"
    {
        "flags"       "k"
        "immunity"    "1"
    }
...
```

If you have plugins that wants to interact with MySQL, modify `tf/addons/sourcemod/configs/databases.cfg` and make sure these settings are present:

```
"Databases"
{
    "driver_default"    "mysql"

    "default"
    {
        "driver"      "default"
        "host"        "localhost"
        "database"    "SQL_DBNAME"
        "user"        "SQL_USERNAME"
        "pass"        "SQL_PASSWORD"
        "port"        "3306"
    }
}
```

## 6) Run server

### 6.1) Windows

Start the server with:

```PowerShell
& "D:\TF2Server\srcds.exe" -console -game tf -secured +ip 0.0.0.0 -clientport 27005 -port 27015 +tv_port 27020 -replay +servercfgfile server.cfg +map plr_hightower +mapcyclefile mapcycle.txt
```

If you want to create a shortcut on your desktop:

```PowerShell
$WshShell = New-Object -comObject WScript.Shell
$Shortcut = $WshShell.CreateShortcut("$Home\Desktop\TF2Server.lnk")
$Shortcut.TargetPath = "D:\TF2Server\srcds.exe"
$shortcut.Arguments = "-console -game tf -secured +ip 0.0.0.0 -clientport 27005 -port 27015 +tv_port 27020 -replay +servercfgfile server.cfg +map plr_hightower +mapcyclefile mapcycle.txt"
$Shortcut.WorkingDirectory = "D:\TF2Server\"
$Shortcut.Save()
```


### 6.2) Linux

Start the server with:

```Bash
/home/steam/tf2server/srcds_run -console -game tf -secured +ip 0.0.0.0 -clientport 27005 -port 27015 +tv_port 27020 -replay +servercfgfile server.cfg +map plr_hightower +mapcyclefile mapcycle.txt
```

If you want to create a shortcut with screen you can execute later with `./tf2server.sh`:

`/home/steam/tf2server.sh`:

```
#!/bin/bash
screen -m -S tf2server /home/steam/tf2server/srcds_run -console -game tf -secured +ip 0.0.0.0 -clientport 27005 -port 27015 +tv_port 27020 -replay +servercfgfile server.cfg +map plr_hightower +mapcyclefile mapcycle.txt
```
