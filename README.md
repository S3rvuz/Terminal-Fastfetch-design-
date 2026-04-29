# Terminal-Fastfetch-design-


# Windows Terminal Setup: Oh My Posh + JetBrainsMono + Fastfetch

## 1. Oh My Posh installieren

PowerShell als Administrator öffnen:

```powershell
winget install JanDeDobbeleer.OhMyPosh -s winget
```

Terminal neu starten und prüfen:

```powershell
oh-my-posh --version
```

---

## 2. JetBrainsMono Nerd Font installieren

```powershell
oh-my-posh font install JetBrainsMono
```

Danach im Windows Terminal einstellen:

```text
Windows Terminal → Einstellungen → Profil auswählen → Darstellung → Schriftart
```

Schriftart wählen:

```text
JetBrainsMono Nerd Font Mono
```

Speichern und Terminal neu starten.

---

## 3. PowerShell-Profil verstehen

PowerShell lädt beim Start automatisch eine Profil-Datei.

Profil anzeigen:

```powershell
$PROFILE
```

Benutzerprofil für alle PowerShell-Hosts anzeigen:

```powershell
$PROFILE.CurrentUserAllHosts
```

Globales Profil anzeigen:

```powershell
$PROFILE.AllUsersCurrentHost
```

Merken:

```text
PowerShell → $PROFILE
CMD → Registry AutoRun
Windows Terminal → nur Oberfläche, nutzt intern PowerShell oder CMD
```

---

## 4. PowerShell-Profil erstellen

Empfohlen: Benutzerprofil für alle Hosts.

```powershell
New-Item -ItemType Directory -Path (Split-Path $PROFILE.CurrentUserAllHosts) -Force
New-Item -Path $PROFILE.CurrentUserAllHosts -Type File -Force
notepad $PROFILE.CurrentUserAllHosts
```

In die Datei eintragen:

```powershell
oh-my-posh init pwsh | Invoke-Expression
```

Speichern, Terminal neu starten.

Falls Skripte blockiert werden:

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Mit J oder Y bestätigen.

---

## 5. Fastfetch installieren

```powershell
winget install fastfetch
```

Prüfen:

```powershell
fastfetch
```

---

## 6. Fastfetch-Konfiguration erstellen

Zielstruktur:

```text
C:\Users\<BENUTZER>\.config\fastfetch\
├── config.jsonc
└── ascii.txt
```

Befehle:

```powershell
mkdir "$HOME\.config" -Force
attrib +h "$HOME\.config"

mkdir "$HOME\.config\fastfetch" -Force

New-Item -Path "$HOME\.config\fastfetch\config.jsonc" -ItemType File -Force
New-Item -Path "$HOME\.config\fastfetch\ascii.txt" -ItemType File -Force
```

Dateien öffnen:

```powershell
notepad "$HOME\.config\fastfetch\config.jsonc"
notepad "$HOME\.config\fastfetch\ascii.txt"
```

Fastfetch mit Config testen:

```powershell
fastfetch --config "$HOME\.config\fastfetch\config.jsonc"
```

---

## 7. Benutzername in Fastfetch ändern

In der config.jsonc das title-Modul suchen.

Beispiel:

```jsonc
{
  "type": "title",
  "format": "Marvin@{host-name}"
}
```

Merken:

```text
{user-name} = echter Windows-Benutzer
Marvin = selbst gesetzter Anzeigename
{host-name} = PC-Name
```

---

## 8. Fastfetch automatisch in PowerShell starten

Profil öffnen:

```powershell
notepad $PROFILE.CurrentUserAllHosts
```

Eintragen:

```powershell
oh-my-posh init pwsh | Invoke-Expression
fastfetch --config "$HOME\.config\fastfetch\config.jsonc"
```

Speichern, Terminal neu starten.

Besser immer $HOME benutzen statt fester Benutzerpfade wie:

```text
C:\Users\Marvin Gertler\...
```

---

## 9. Fastfetch automatisch in CMD starten

CMD nutzt kein PowerShell-Profil. CMD nutzt Registry AutoRun.

Ohne eigene Config:

```cmd
reg add "HKCU\Software\Microsoft\Command Processor" /v AutoRun /t REG_SZ /d "fastfetch" /f
```

Mit eigener Config:

```cmd
reg add "HKCU\Software\Microsoft\Command Processor" /v AutoRun /t REG_SZ /d "fastfetch --config \"%USERPROFILE%\.config\fastfetch\config.jsonc\"" /f
```

Prüfen:

```cmd
reg query "HKCU\Software\Microsoft\Command Processor" /v AutoRun
```

Entfernen:

```cmd
reg delete "HKCU\Software\Microsoft\Command Processor" /v AutoRun /f
```

---

## 10. Windows Defender blockiert Profil-Datei

Falls Windows meldet, dass PowerShell Zugriff auf Dokumente blockiert:

```text
Windows-Sicherheit
→ Viren- & Bedrohungsschutz
→ Ransomware-Schutz verwalten
→ App durch überwachten Ordnerzugriff zulassen
```

Erlauben:

```text
powershell.exe
pwsh.exe
WindowsTerminal.exe
```

---

# Kurzfassung

```powershell
winget install JanDeDobbeleer.OhMyPosh -s winget
oh-my-posh font install JetBrainsMono
winget install fastfetch

New-Item -ItemType Directory -Path (Split-Path $PROFILE.CurrentUserAllHosts) -Force
New-Item -Path $PROFILE.CurrentUserAllHosts -Type File -Force

mkdir "$HOME\.config" -Force
attrib +h "$HOME\.config"
mkdir "$HOME\.config\fastfetch" -Force

New-Item "$HOME\.config\fastfetch\config.jsonc" -ItemType File -Force
New-Item "$HOME\.config\fastfetch\ascii.txt" -ItemType File -Force

notepad $PROFILE.CurrentUserAllHosts
```

In das Profil:

```powershell
oh-my-posh init pwsh | Invoke-Expression
fastfetch --config "$HOME\.config\fastfetch\config.jsonc"
```

Merksatz:

```text
Oh My Posh macht den Prompt schön.
Nerd Font zeigt Symbole richtig an.
Fastfetch zeigt Systeminfos.
PowerShell startet über $PROFILE.
CMD startet über Registry AutoRun.
```
