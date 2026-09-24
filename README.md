# Akaz Hub UI 2.8

> **Akaz Hub UI** — bibliothèque d'interface Luau orientée Roblox, pensée pour créer rapidement des hubs modernes, sombres, responsives et fortement personnalisables.
>
> **Branding actuel :** `Akaz Hub` / `by as95`
>
> **Version documentée :** `2.8`

---

## Table des matières

1. [Présentation](#présentation)
2. [Ce que contient la bibliothèque](#ce-que-contient-la-bibliothèque)
3. [Installation](#installation)
4. [Premier script](#premier-script)
5. [CreateWindow](#createwindow)
6. [Options de la fenêtre](#options-de-la-fenêtre)
7. [Architecture d'une fenêtre](#architecture-dune-fenêtre)
8. [Tabs](#tabs)
9. [SubTabs](#subtabs)
10. [Éléments UI](#éléments-ui)
11. [Toggle](#toggle)
12. [Button](#button)
13. [Section](#section)
14. [Divider](#divider)
15. [Label](#label)
16. [Paragraph](#paragraph)
17. [Keybind](#keybind)
18. [Input](#input)
19. [Dropdown](#dropdown)
20. [MultiDropdown](#multidropdown)
21. [Slider](#slider)
22. [ColorPicker](#colorpicker)
23. [AddComponents](#addcomponents)
24. [Flags](#flags)
25. [State réactive](#state-réactive)
26. [Thèmes](#thèmes)
27. [Icônes](#icônes)
28. [Logo Akaz](#logo-akaz)
29. [Discord](#discord)
30. [Loader](#loader)
31. [Animations](#animations)
32. [Minimisation](#minimisation)
33. [Responsive / mobile](#responsive--mobile)
34. [Notifications](#notifications)
35. [Configuration et AutoSave](#configuration-et-autosave)
36. [Profil joueur](#profil-joueur)
37. [Performance](#performance)
38. [Music Player](#music-player)
39. [Tag System](#tag-system)
40. [Fonctions admin](#fonctions-admin)
41. [Destruction / nettoyage](#destruction--nettoyage)
42. [Exemples complets](#exemples-complets)
43. [Bonnes pratiques](#bonnes-pratiques)
44. [Limites connues](#limites-connues)
45. [Structure technique](#structure-technique)
46. [Changelog résumé](#changelog-résumé)
47. [FAQ](#faq)

---

# Présentation

**Akaz Hub UI** est une bibliothèque Luau qui centralise les primitives nécessaires à la construction d'un hub Roblox : fenêtre principale, barre de navigation, tabs, sous-tabs, contrôles, thèmes, persistence, notifications, profil, performance, musique, système de tags, scaling mobile et animations.

La philosophie actuelle de la version 2.8 est :

- interface sombre et nette ;
- accent visuel monochrome/gris ;
- icônes provenant d'un icon pack Roblox ;
- branding `Akaz Hub` avec sous-titre `by as95` ;
- contrôles tactiles et souris ;
- scaling automatique sur mobile ;
- loader animé ;
- minimisation animée ;
- effet de pression sur les boutons ;
- persistence des flags dans des fichiers JSON quand les APIs de fichiers de l'environnement sont disponibles ;
- système de thème réactif ;
- nettoyage des connexions lors de la destruction des fenêtres.

La bibliothèque retourne un objet `Library`, puis les objets `Window`, `Tab` et `SubTab` servent de niveaux d'abstraction pour construire l'interface.

---

# Ce que contient la bibliothèque

La couche publique principale expose notamment :

```text
Library
 ├─ CreateWindow()
 ├─ SetTheme()
 ├─ GetTheme()
 ├─ GetIcons()
 ├─ GetIcon()
 ├─ Get()
 ├─ Set()
 ├─ GetFlag()
 ├─ SetFlag()
 ├─ SetState()
 ├─ GetState()
 ├─ BindState()
 ├─ GetConfig()
 ├─ LoadConfigData()
 ├─ SaveConfig()
 ├─ LoadConfig()
 ├─ ListConfigs()
 ├─ DeleteConfig()
 ├─ Notify()
 ├─ Notification()
 ├─ AdminDisconnect()
 ├─ JoinPlayer()
 ├─ IsAdmin()
 └─ DestroyAll()
```

Une fenêtre possède ensuite :

```text
Window
 ├─ AddTab()
 ├─ Set()
 ├─ Get()
 ├─ SetState()
 ├─ GetState()
 ├─ BindState()
 ├─ GetConfig()
 ├─ SaveConfig()
 ├─ LoadConfig()
 ├─ QueueAutoSave()
 ├─ SetVisible()
 ├─ Toggle()
 ├─ SetUIVisible()
 ├─ ToggleUI()
 ├─ SetScale()
 ├─ GetScale()
 ├─ SetProfileVisible()
 ├─ ToggleProfile()
 ├─ SetLogo()
 ├─ Notify()
 └─ Destroy()
```

Un `Tab` contient des `SubTab` :

```text
Tab
 └─ AddSubTab()
```

Une `SubTab` peut ensuite créer les composants :

```text
AddToggle
AddButton
AddSection
AddDivider
AddLabel
AddParagraph
AddKeybind
AddInput
AddDropdown
AddMultiDropdown
AddSlider
AddColorPicker
AddComponents
```

---

# Installation

Le fichier est un module Luau qui termine par :

```lua
return Library
```

Le mode de chargement dépend donc de l'environnement qui exécute le script.

### Exemple avec une URL qui renvoie le code Luau

```lua
local Library = loadstring(game:HttpGet("URL_DE_TA_LIBRARY"))()
```

Puis :

```lua
local Window = Library:CreateWindow({
    Name = "Akaz Hub",
})
```

### Exemple de chargement local

Si ton environnement permet de lire un fichier local :

```lua
local source = readfile("Akaz_Hub_UI_2.8.luau")
local Library = loadstring(source)()
```

> `loadstring`, `readfile`, `writefile`, `getcustomasset`, etc. sont dépendants de l'environnement d'exécution. La bibliothèque vérifie plusieurs de ces APIs avant de les utiliser.

---

# Premier script

Exemple minimal :

```lua
local Library = loadstring(game:HttpGet("URL_DE_TA_LIBRARY"))()

local Window = Library:CreateWindow({
    Name = "Akaz Hub",
})

local Main = Window:AddTab({
    Name = "Main",
    Icon = "house",
    Subtitle = "Main controls",
})

local General = Main:AddSubTab("General")

General:AddToggle({
    Name = "Example Toggle",
    Description = "Active ou désactive une option.",
    Flag = "ExampleToggle",
    Default = false,
    Callback = function(value)
        print("Toggle:", value)
    end,
})
```

---

# CreateWindow

La fonction centrale est :

```lua
local Window = Library:CreateWindow(opts)
```

`opts` est une table optionnelle.

La fenêtre utilise par défaut une taille de :

```lua
UDim2.fromOffset(700, 490)
```

et une position centrée :

```lua
UDim2.fromScale(0.5, 0.5)
```

Le nom du `ScreenGui` par défaut est :

```text
AkazHubUI
```

Le conteneur principal est nommé :

```text
AkazContainer
```

---

# Options de la fenêtre

## `Name`

Nom principal du hub.

```lua
Name = "Akaz Hub"
```

Ce texte apparaît dans le branding de la sidebar.

Valeur par défaut de la version actuelle :

```text
Akaz Hub
```

## `BrandSubtitle`

Sous-titre du branding.

```lua
BrandSubtitle = "by as95"
```

Valeur actuelle par défaut :

```text
by as95
```

---

## `Logo`

Définit le logo.

```lua
Logo = "rbxassetid://91555718992391"
```

Le logo Akaz intégré par défaut utilise :

```text
91555718992391
```

Il est utilisé dans plusieurs endroits de l'interface, notamment le branding et les copies mobiles/watermark lorsqu'elles sont présentes.

## `LogoZoom`

Contrôle le zoom visuel du logo.

```lua
LogoZoom = 1.2
```

La valeur est bornée par la bibliothèque entre `1` et `6`.

## `LogoGlow`

Active ou désactive le contour/effet lumineux associé au logo.

```lua
LogoGlow = true
```

Pour le désactiver :

```lua
LogoGlow = false
```

---

## `Size`

Taille initiale de la fenêtre.

```lua
Size = UDim2.fromOffset(720, 500)
```

## `Position`

Position initiale.

```lua
Position = UDim2.fromScale(0.5, 0.5)
```

## `GuiName`

Nom du `ScreenGui`.

```lua
GuiName = "MyAkazUI"
```

Par défaut :

```text
AkazHubUI
```

## `Parent`

Permet de fournir manuellement le parent du `ScreenGui`.

```lua
Parent = someInstance
```

Sans parent explicite, la bibliothèque essaie un host UI disponible, puis `PlayerGui` en fallback.

## `DisplayOrder`

Ordre d'affichage du `ScreenGui`.

```lua
DisplayOrder = 50
```

Valeur par défaut : `10`.

## `ReplaceExisting`

Contrôle la suppression d'un `ScreenGui` portant le même nom avant la création.

```lua
ReplaceExisting = true
```

Par défaut, le remplacement est activé.

Pour empêcher cette suppression :

```lua
ReplaceExisting = false
```

## `Visible`

Permet de créer la fenêtre puis de la laisser désactivée.

```lua
Visible = false
```

---

# Loader

Le loader est activé par défaut.

```lua
LoadingAnimation = true
```

Le texte principal par défaut est :

```text
AKAZ
```

Le loader peut être personnalisé avec les options suivantes.

## `LoadingAnimation`

```lua
LoadingAnimation = false
```

Désactive complètement l'animation de démarrage.

## `LoadingDuration`

Durée logique du loader.

```lua
LoadingDuration = 1.2
```

La valeur est bornée entre `0.4` et `8` secondes.

## `LoadingText`

Texte central.

```lua
LoadingText = "AKAZ"
```

## `LoadingSubtitle`

Sous-texte.

```lua
LoadingSubtitle = "HUB"
```

## `LoadingFooter`

Footer du loader.

```lua
LoadingFooter = "AKAZ HUB"
```

## `LoadingOverlayTransparency`

Transparence du fond du loader.

```lua
LoadingOverlayTransparency = 0.35
```

## `LoadingBlur`

Active le blur pendant le chargement.

```lua
LoadingBlur = true
```

Pour le désactiver :

```lua
LoadingBlur = false
```

Le script utilise un `BlurEffect` nommé :

```text
AkazLoadingBlur
```

Il supprime également un éventuel blur Akaz laissé par une exécution précédente avant de créer le nouveau loader.

## `LoadingSound`

SoundId optionnel joué pendant le loader.

```lua
LoadingSound = "rbxassetid://123456789"
```

## `LoadingSoundStart`

Position de départ du son :

```lua
LoadingSoundStart = 0
```

## `LoadingSoundVolume`

Volume cible :

```lua
LoadingSoundVolume = 0.45
```

---

# Architecture d'une fenêtre

Une fenêtre Akaz est organisée en plusieurs couches :

```text
ScreenGui
└── AkazContainer
    ├── Main
    │   ├── Sidebar / Brand
    │   ├── Content
    │   └── autres éléments
    └── Hotbar
```

Des panneaux additionnels peuvent vivre directement sous le `ScreenGui` :

```text
Profile
Performance
MusicPlayer
AdminPanel
Notifications
MobileToggle
```

Cette séparation est importante pour comprendre le système de scaling et de visibilité.

---

# Tabs

Créer un tab :

```lua
local MainTab = Window:AddTab({
    Name = "Main",
    Icon = "house",
    Subtitle = "General controls",
})
```

### `Name`

Nom affiché dans la hotbar et dans le header.

### `Icon`

Nom d'icône, ID Roblox ou texte.

Exemples :

```lua
Icon = "house"
```

```lua
Icon = "settings"
```

```lua
Icon = "layout-dashboard"
```

```lua
Icon = "rbxassetid://123456789"
```

Si aucune icône n'est résolue, la bibliothèque peut utiliser la première lettre du nom du tab comme fallback texte.

### `Subtitle`

Sous-titre du header du tab.

```lua
Subtitle = "Visual settings"
```

---

# SubTabs

Créer une sous-tab :

```lua
local General = MainTab:AddSubTab("General")
```

Une sous-tab devient une pill dans la barre secondaire du tab.

La barre des sous-tabs dispose d'un système de scroll horizontal lorsque le nombre de pills dépasse l'espace disponible.

Les boutons `‹` et `›` permettent également de faire défiler la barre.

---

# Éléments UI

Tous les composants prennent une table `opts`, sauf les méthodes simplifiées comme :

```lua
SubTab:AddSubTab("General")
SubTab:AddDivider()
```

Quand un composant possède un `Flag`, sa valeur peut être lue/écrite et persistée.

---

# Toggle

```lua
local Toggle = General:AddToggle({
    Name = "Auto Farm",
    Description = "Active le système automatique.",
    Flag = "AutoFarm",
    Default = false,
    Callback = function(enabled)
        print("Auto Farm:", enabled)
    end,
})
```

### Options

| Option | Type | Description |
|---|---|---|
| `Name` | string | Nom affiché |
| `Description` | string? | Texte secondaire |
| `Flag` | string? | Identifiant de sauvegarde |
| `Default` | boolean | Valeur initiale |
| `Callback` | function? | Appel lors du changement |

### Modifier depuis le code

```lua
Toggle:Set(true)
```

### Lire

```lua
local state = Toggle:Get()
```

---

# Button

```lua
General:AddButton({
    Name = "Execute",
    Callback = function()
        print("Clicked")
    end,
})
```

### Bouton primaire

```lua
General:AddButton({
    Name = "Execute",
    Primary = true,
    Callback = function()
        print("Primary button")
    end,
})
```

`Style = "primary"` est également accepté :

```lua
Style = "primary"
```

Le bouton possède l'effet de pression global de la version 2.8 lorsqu'il est reconnu comme `GuiButton`.

---

# Section

```lua
General:AddSection({
    Name = "Combat",
})
```

Version raccourcie :

```lua
General:AddSection("Combat")
```

La section produit un titre, une barre et un petit accent visuel.

---

# Divider

```lua
General:AddDivider()
```

Ajoute une ligne de séparation horizontale.

---

# Label

```lua
local Label = General:AddLabel("Ready")
```

Ou :

```lua
local Label = General:AddLabel({
    Text = "Ready",
})
```

### API du handle

```lua
Label:Set("Loading...")
```

```lua
local text = Label:Get()
```

```lua
local instance = Label.Instance
```

---

# Paragraph

```lua
local Paragraph = General:AddParagraph({
    Title = "Information",
    Text = "Texte long avec retour à la ligne automatique.",
})
```

Ou avec `Content` :

```lua
Text = "..."
```

ou

```lua
Content = "..."
```

### API

```lua
Paragraph:Set("Nouveau texte")
```

```lua
print(Paragraph:Get())
```

Le body utilise `TextWrapped` et une hauteur automatique.

---

# Keybind

```lua
General:AddKeybind({
    Name = "Toggle UI",
    Description = "Raccourci pour afficher/masquer le hub.",
    Flag = "ToggleKey",
    Default = Enum.KeyCode.RightShift,
    OnKeyChanged = function(key)
        print("New key:", key)
    end,
    OnPress = function(key)
        print("Pressed:", key)
    end,
})
```

`Callback` et `Pressed` peuvent également servir de callbacks de pression selon l'utilisation.

### Retirer le raccourci

```lua
Keybind:Set(nil)
```

### Annuler pendant l'écoute

Lorsqu'un nouveau raccourci est assigné, `Escape` permet d'annuler l'affectation et de remettre la valeur à `None`.

---

# Input

```lua
General:AddInput({
    Name = "Username",
    Description = "Nom à utiliser.",
    Flag = "Username",
    Default = "",
    Placeholder = "Enter username...",
    Callback = function(text, enterPressed)
        print(text, enterPressed)
    end,
})
```

Le callback est déclenché à la perte du focus.

---

# Dropdown

```lua
local Mode = General:AddDropdown({
    Name = "Mode",
    Description = "Choisir un mode.",
    Flag = "Mode",
    Options = {"Legit", "Rage", "Custom"},
    Default = "Legit",
    Searchable = true,
    MaxVisible = 5,
    Callback = function(value)
        print("Mode:", value)
    end,
})
```

### Options importantes

`Options` : tableau des choix.

`Default` : valeur initiale.

`Searchable` : affiche une zone de recherche.

`MaxVisible` : nombre maximal d'entrées visibles avant scroll.

### API de handle

```lua
Mode:Set("Custom")
```

```lua
print(Mode:Get())
```

```lua
Mode:SetOptions({"A", "B", "C"})
```

```lua
Mode:Refresh()
```

---

# MultiDropdown

```lua
local Targets = General:AddMultiDropdown({
    Name = "Targets",
    Description = "Sélection multiple.",
    Flag = "Targets",
    Options = {"Players", "NPCs", "Bosses"},
    Default = {"Players"},
    Searchable = true,
    MaxVisible = 5,
    Callback = function(values)
        for _, value in ipairs(values) do
            print(value)
        end
    end,
})
```

### Lecture

```lua
local values = Targets:Get()
```

### Écriture

```lua
Targets:Set({"NPCs", "Bosses"})
```

### Modifier les options

```lua
Targets:SetOptions({
    "Players",
    "NPCs",
    "Bosses",
    "Pets",
})
```

---

# Slider

```lua
local Speed = General:AddSlider({
    Name = "Speed",
    Flag = "Speed",
    Min = 0,
    Max = 100,
    Default = 25,
    Suffix = "%",
    Callback = function(value)
        print("Speed:", value)
    end,
})
```

### Fonctionnement actuel

La valeur est limitée entre `Min` et `Max`, puis arrondie avec :

```lua
math.floor(value + 0.5)
```

Cela signifie que le slider actuel est **entier**.

### Exemple

```lua
Speed:Set(60)
```

```lua
print(Speed:Get())
```

---

# ColorPicker

```lua
local AccentColor = General:AddColorPicker({
    Name = "Accent",
    Description = "Couleur personnalisée.",
    Flag = "AccentColor",
    Default = "#FFFFFF",
    Callback = function(color)
        print(color)
    end,
})
```

Le composant accepte un `Color3` :

```lua
Default = Color3.fromRGB(255, 255, 255)
```

ou une chaîne hexadécimale :

```lua
Default = "#FFFFFF"
```

### API

```lua
AccentColor:Set(Color3.fromRGB(255, 0, 0))
```

```lua
local color = AccentColor:Get()
```

```lua
local hex = AccentColor:GetHex()
```

Le picker fournit :

- zone saturation/valeur ;
- barre de teinte ;
- champ hexadécimal ;
- mise à jour visuelle en direct.

---

# AddComponents

Pour construire rapidement une page depuis une liste déclarative :

```lua
General:AddComponents({
    {
        Type = "section",
        Name = "General",
    },
    {
        Type = "toggle",
        Name = "Enabled",
        Flag = "Enabled",
        Default = true,
        Callback = function(v)
            print(v)
        end,
    },
    {
        Type = "slider",
        Name = "Speed",
        Flag = "Speed",
        Min = 0,
        Max = 100,
        Default = 50,
    },
    {
        Type = "dropdown",
        Name = "Mode",
        Options = {"A", "B"},
        Default = "A",
    },
})
```

Les `Type` actuellement routés par la bibliothèque sont :

```text
toggle
slider
dropdown
multidropdown
button
input
keybind
color
colorpicker
paragraph
divider
section
```

Le retour est un tableau de handles/instances correspondant aux composants créés.

---

# Flags

Les flags donnent un nom stable à la valeur d'un composant.

Exemple :

```lua
Flag = "AutoFarm"
```

Puis :

```lua
Window:Set("AutoFarm", true)
```

et :

```lua
local enabled = Window:Get("AutoFarm", false)
```

Au niveau global :

```lua
Library:SetFlag("AutoFarm", true)
```

```lua
local enabled = Library:GetFlag("AutoFarm", false)
```

### Pourquoi utiliser des flags ?

Ils servent notamment à :

- piloter les composants depuis un autre endroit du script ;
- sauvegarder les valeurs ;
- recharger les valeurs ;
- créer une configuration persistante.

### Attention aux noms

Les flags doivent être choisis avec des noms suffisamment explicites et uniques à l'intérieur de ton interface.

Exemple recommandé :

```text
Combat_AutoFarm
Combat_Range
Visuals_ESP
Visuals_Box
Settings_Theme
```

---

# State réactive

Le `State` est distinct des `Flags`.

### Écriture

```lua
Library:SetState("CurrentTarget", "Boss")
```

### Lecture

```lua
local target = Library:GetState("CurrentTarget", "None")
```

### Binding

```lua
Library:BindState("CurrentTarget", function(value)
    print("Target changed:", value)
end)
```

Le callback est appelé immédiatement si un état existe déjà.

### Via Window

```lua
Window:SetState("CurrentTarget", "Boss")
```

```lua
Window:GetState("CurrentTarget", "None")
```

```lua
Window:BindState("CurrentTarget", function(value)
    print(value)
end)
```

---

# Thèmes

La bibliothèque contient trois thèmes intégrés :

```text
Dark
Light
OLED
```

### Dark

Thème principal de la bibliothèque : fond sombre, surfaces grises et accent clair.

### Light

Variante claire avec surfaces gris très clair, texte sombre et accent foncé.

### OLED

Variante avec noir très profond et contraste renforcé.

### Changer le thème

```lua
Library:SetTheme("Dark")
```

```lua
Library:SetTheme("Light")
```

```lua
Library:SetTheme("OLED")
```

### Vérifier le thème actuel

```lua
print(Library:GetTheme())
```

### Thème personnalisé

`SetTheme` accepte aussi une table. Les clés de couleur connues de la palette sont notamment :

```text
WindowBg
CardBg
Border
Element
ElementHover
Badge
BadgeIdle
NavActive
NavHover
PillActive
White
TextGray
TextDim
KnobOff
KnobOn
TrackBg
Placeholder
HotbarBg
HotbarBorder
HotbarActive
HotbarHover
HotbarDot
Accent
AccentDim
AccentText
KnobAccent
```

Exemple :

```lua
Library:SetTheme({
    WindowBg = Color3.fromRGB(12, 12, 12),
    CardBg = Color3.fromRGB(18, 18, 18),
    Border = Color3.fromRGB(40, 40, 40),
    Accent = Color3.fromRGB(255, 255, 255),
    AccentDim = Color3.fromRGB(60, 60, 60),
    AccentText = Color3.fromRGB(10, 10, 10),
})
```

Les composants qui utilisent les attributs de thème sont repeints lorsque le thème change.

---

# Icônes

La version 2.8 embarque une table `ICONS` issue du pack fourni :

```text
https://raw.githubusercontent.com/xxpwnxxx420lord/Scripts/refs/heads/main/iconpack.lua
```

Les icônes sont des assets Roblox et sont affichées comme images, avec :

```lua
ScaleType = Enum.ScaleType.Fit
ResampleMode = Enum.ResamplerMode.Default
```

Les images sont également associées aux attributs de thème nécessaires pour permettre leur recoloration.

### Exemples de noms d'icônes

```text
house
layout-dashboard
layout-grid
layout-list
sliders-horizontal
refresh-cw
skip-back
skip-forward
circle-alert
triangle-alert
user-round
users-round
gamepad-2
flame
sparkles
info
check
chevron-right
chevron-left
menu
search
settings
shield
target
crosshair
sword
swords
eye
eye-off
music
play
volume
wifi
terminal
code
folder
file
clipboard
download
trash
maximize
minimize
```

De nombreux alias sont également disponibles :

```text
home -> house
dashboard -> layout-dashboard
gear -> settings
combat -> swords
aim -> crosshair
lightning -> bolt
visuals -> eye
player -> user
users -> users-round-like asset mapping
favorite -> star
music -> music
```

### Lire les icônes depuis la bibliothèque

```lua
local icons = Library:GetIcons()
```

Obtenir une seule icône :

```lua
local settingsIcon = Library:GetIcon("settings")
```

Puis dans un tab :

```lua
Window:AddTab({
    Name = "Settings",
    Icon = settingsIcon,
})
```

Ou directement par nom :

```lua
Window:AddTab({
    Name = "Settings",
    Icon = "settings",
})
```

---

# Logo Akaz

Le logo Roblox fourni à la bibliothèque est :

```lua
local AKAZ_LOGO_URL = "rbxassetid://91555718992391"
```

Le logo par défaut est donc :

```lua
local DEFAULT_LOGO = AKAZ_LOGO_URL
```

### Changer le logo après création

```lua
Window:SetLogo("rbxassetid://123456789")
```

Le système met à jour les copies de logo qui sont enregistrées par la fenêtre, ainsi que les fallbacks lorsqu'ils existent.

### Pourquoi privilégier `rbxassetid://` ?

Un `ImageLabel` Roblox n'est pas conçu pour recevoir une URL web quelconque comme source d'image native. La version actuelle évite donc de dépendre directement du PNG GitHub pour le logo Roblox principal.

---

# Discord

Le logo Discord actuel est :

```lua
local AKAZ_DISCORD_LOGO_URL = "rbxassetid://133801393445445"
```

Le bouton utilise l'invitation :

```text
https://discord.gg/PCep5XRFS4
```

### Fonctionnement visuel

Le Discord se trouve sous le bloc d'identité du joueur et n'est pas censé être fusionné avec la ligne du pseudo/nametag.

### Fonctionnement du clic

La bibliothèque essaie plusieurs APIs de presse-papier :

```text
setclipboard
toclipboard
set_clipboard
writeclipboard
```

Après un clic, le label passe temporairement à :

```text
Invite copied
```

ou :

```text
Copy unavailable
```

puis revient à `Discord`.

---

# Animations

La version 2.8 utilise plusieurs tweens et animations :

- apparition du loader ;
- sortie du loader ;
- travelling glow de la fenêtre ;
- shimmer du branding ;
- animation des notifications ;
- animation des toggles ;
- animation des dropdowns ;
- animation du color picker ;
- animation des boutons au survol ;
- effet de pression des boutons ;
- minimisation et restauration de la fenêtre ;
- apparition/disparition de plusieurs panneaux.

---

# Effet de pression des boutons

La version 2.8 inclut un effet de pression global destiné aux contrôles interactifs.

Le principe est visuellement :

```text
Normal
  ↓
Press
  ↓
petite compression
  ↓
retour fluide
```

L'effet est prévu pour les `GuiButton`, notamment :

```text
TextButton
ImageButton
```

Le but est que les boutons ne donnent pas l'impression de réagir brutalement à un clic.

Il peut donc accompagner :

- boutons de fermeture ;
- bouton de minimisation ;
- tabs ;
- sous-tabs ;
- boutons d'action ;
- Discord ;
- boutons du Music Player ;
- contrôles mobiles.

---

# Minimisation

La fenêtre n'est pas limitée à un simple :

```lua
Visible = false
```

La version animée possède une transition de minimisation.

Conceptuellement :

```text
Fenêtre ouverte
      ↓
clic minimiser
      ↓
réduction + déplacement
      ↓
pill / bouton réduit
```

Puis l'action inverse :

```text
Pill
  ↓
clic
  ↓
agrandissement + retour à la position
  ↓
fenêtre
```

La position initiale de la fenêtre est conservée pour permettre de retrouver l'emplacement après restauration.

---

# Responsive / mobile

La bibliothèque détecte automatiquement les plateformes tactiles.

Elle regarde notamment :

- plateforme iOS/Android ;
- présence du touch ;
- présence du clavier comme signal complémentaire.

Le comportement peut être forcé :

```lua
Mobile = true
```

ou :

```lua
Mobile = false
```

### Scaling

La taille de la fenêtre est adaptée au viewport lorsque le mode mobile est actif.

Une `UIScale` est appliquée :

- au conteneur principal ;
- au profil ;
- aux performances ;
- au Music Player ;
- au burger/minimisé ;
- au panneau admin.

### `Scale`

Tu peux définir un facteur de scaling initial :

```lua
Scale = 0.9
```

La valeur est ensuite limitée par `SetScale` entre `0.5` et `1.5`.

### API

```lua
Window:SetScale(0.85)
```

```lua
print(Window:GetScale())
```

---

# Notifications

Une notification peut être créée avec :

```lua
Library:Notify({
    Title = "Success",
    Content = "Action completed.",
    Type = "Success",
    Duration = 4,
})
```

Ou directement via la fenêtre :

```lua
Window:Notify({
    Title = "Info",
    Content = "Information.",
    Type = "Info",
})
```

`Library:Notification()` est un alias de `Library:Notify()`.

### Types intégrés

Les styles de notification documentés dans le code sont :

```text
Info
Success
Warning
Error
```

Chaque style possède son icône et sa couleur dédiées.

### Options

| Option | Type | Défaut |
|---|---|---|
| `Title` | string | nom du style |
| `Content` | string | `Notification` |
| `Description` | string | fallback du contenu |
| `Message` | string | fallback du contenu |
| `Type` | string | `Info` |
| `Duration` | number | `4` |

---

# Configuration et AutoSave

La configuration utilise des fichiers JSON lorsque les APIs nécessaires existent.

Le dossier par défaut est :

```text
AkazHub/configs
```

### Sauvegarder

```lua
Library:SaveConfig("main")
```

### Charger

```lua
Library:LoadConfig("main")
```

### Lister

```lua
local configs = Library:ListConfigs()
```

### Supprimer

```lua
Library:DeleteConfig("main")
```

### Lire sans écrire

```lua
local data = Library:GetConfig()
```

### Charger des données déjà en mémoire

```lua
Library:LoadConfigData(data)
```

---

# Configuration par fenêtre

Une fenêtre conserve aussi son propre nom de configuration.

```lua
local Window = Library:CreateWindow({
    Name = "Akaz Hub",
    ConfigName = "main",
})
```

Si `ConfigName` n'est pas fourni, la bibliothèque génère un nom à partir de `Name` ou du `PlaceId`.

L'AutoLoad est activé par défaut :

```lua
AutoLoad = true
```

Pour le désactiver :

```lua
AutoLoad = false
```

### Autosave

Les composants flaggés programment une sauvegarde différée lors des modifications lorsque les APIs de fichiers sont disponibles.

L'objectif est d'éviter d'écrire un fichier à chaque micro-changement lorsque l'utilisateur déplace un slider ou modifie plusieurs valeurs rapidement.

---

# Profil joueur

La bibliothèque possède un panneau de profil pouvant être activé depuis le code.

Options disponibles dans `CreateWindow` :

```lua
ProfileKey = Enum.KeyCode.K
ProfileWidth = 312
ProfileBottomMargin = 18
ProfileTitle = "PLAYER PROFILE"
```

### API

```lua
Window:SetProfileVisible(true)
```

```lua
Window:SetProfileVisible(false)
```

```lua
Window:ToggleProfile()
```

Le panneau utilise le joueur local et charge également la miniature de l'avatar de manière asynchrone.

---

# Performance

La fenêtre possède un panneau de performances avec collecte d'échantillons FPS.

Options :

```lua
PerformanceWidth = 266
PerformanceHeight = 294
PerformanceTitle = "LIVE PERFORMANCE"
```

Une connexion `RenderStepped` est utilisée pour calculer un FPS moyen sur des intervalles courts et alimenter le graphe.

### Bon usage

Le panneau est essentiellement destiné à fournir une lecture visuelle en direct des performances de l'interface/jeu. Il ne constitue pas une mesure benchmark scientifique.

---

# Music Player

Un lecteur audio intégré peut être construit par la bibliothèque.

Dossier par défaut :

```text
AkazMusic
```

Le `Sound` interne porte le nom :

```text
AkazMusicPlayer
```

### Option de dossier

```lua
MusicFolder = "MyMusic"
```

Le lecteur peut scanner les fichiers disponibles dans ce dossier lorsqu'un environnement de fichier local le permet.

La structure du lecteur comprend :

- lecture/pause ;
- précédent/suivant ;
- volume ;
- barre de progression ;
- recherche/seeking ;
- titre courant ;
- durée ;
- rafraîchissement de la liste.

Les assets audio Roblox peuvent être soumis aux permissions du jeu ; un `SoundId` valide n'implique pas nécessairement que l'audio sera effectivement lisible dans toutes les expériences.

---

# Tag System

La bibliothèque embarque un `TagSystem` partagé.

Lors de la création d'une fenêtre, le système est démarré automatiquement dans le code actuel.

Il est capable de :

- conserver un registre des utilisateurs actifs ;
- afficher des tags au-dessus de certains joueurs ;
- ajouter/supprimer des tags ;
- attacher des outlines aux personnages d'autres joueurs ;
- écouter les changements de snapshot via `OnUsersUpdated` ;
- arrêter le service lorsqu'il ne reste plus de fenêtre.

### Écouter les mises à jour

```lua
Library.TagSystem:OnUsersUpdated(function(userInfo, active)
    print("Users updated")
end)
```

### Supprimer un listener

```lua
local function listener(userInfo, active)
    print("Update")
end

Library.TagSystem:OnUsersUpdated(listener)
Library.TagSystem:RemoveListener(listener)
```

Le système possède un polling interne dans le code actuel.

---

# Fonctions admin

La bibliothèque expose également :

```lua
Library:IsAdmin()
```

pour savoir si l'utilisateur local est présent dans la liste admin utilisée par le client.

Il existe aussi :

```lua
Library:AdminDisconnect(userId)
```

et :

```lua
Library:JoinPlayer(placeId, jobId)
```

### `AdminDisconnect`

Cette fonction construit une requête HTTP vers le service admin configuré par le script et renvoie :

```text
true
```

en cas de succès ou :

```text
false, "error"
```

en cas d'échec.

La réponse `403` est convertie en :

```text
not authorized
```

### `JoinPlayer`

Cette méthode tente un :

```lua
TeleportService:TeleportToPlaceInstance(...)
```

avec les informations `placeId` et `jobId` fournies.

---

# Destruction / nettoyage

## Détruire une fenêtre

```lua
Window:Destroy()
```

La destruction nettoie notamment :

- le `ScreenGui` ;
- les connexions suivies ;
- le FPS ;
- les panneaux ;
- le Music Player ;
- les ressources du loader ;
- le blur `AkazLoadingBlur` ;
- les éléments associés à la fenêtre.

## Détruire toutes les fenêtres

```lua
Library:DestroyAll()
```

Cette méthode détruit les fenêtres connues de la bibliothèque, nettoie les listes internes et arrête le `TagSystem`.

---

# API de visibilité

### `SetVisible`

```lua
Window:SetVisible(true)
```

### `Toggle`

```lua
Window:Toggle()
```

### `SetUIVisible`

Cette méthode gère la visibilité de l'ensemble de l'UI et des panneaux latéraux associés.

```lua
Window:SetUIVisible(false)
```

Puis :

```lua
Window:SetUIVisible(true)
```

### `ToggleUI`

```lua
Window:ToggleUI()
```

---

# Référencement rapide des API

## Library

```lua
Library:CreateWindow(opts)
Library:SetState(key, value)
Library:GetState(key, default)
Library:BindState(key, callback)
Library:Get(flag, default)
Library:Set(flag, value)
Library:SetTheme(theme)
Library:GetTheme()
Library:GetIcons()
Library:GetIcon(name)
Library:GetFlag(flag, default)
Library:SetFlag(flag, value)
Library:GetConfig()
Library:LoadConfigData(data)
Library:SaveConfig(name)
Library:LoadConfig(name)
Library:ListConfigs()
Library:DeleteConfig(name)
Library:Notify(opts)
Library:Notification(opts)
Library:AdminDisconnect(userId)
Library:JoinPlayer(placeId, jobId)
Library:IsAdmin()
Library:DestroyAll()
```

## Window

```lua
Window:AddTab(opts)
Window:SetState(key, value)
Window:GetState(key, default)
Window:BindState(key, callback)
Window:Get(flag, default)
Window:Set(flag, value)
Window:GetConfig()
Window:SaveConfig(name)
Window:LoadConfig(name)
Window:QueueAutoSave()
Window:SetVisible(value)
Window:Toggle()
Window:SetUIVisible(value)
Window:ToggleUI()
Window:SetScale(value)
Window:GetScale()
Window:SetProfileVisible(value)
Window:ToggleProfile()
Window:SetLogo(id)
Window:Notify(opts)
Window:Destroy()
```

## Tab

```lua
Tab:AddSubTab(name)
```

## SubTab

```lua
SubTab:AddToggle(opts)
SubTab:AddButton(opts)
SubTab:AddSection(opts)
SubTab:AddDivider()
SubTab:AddLabel(opts)
SubTab:AddParagraph(opts)
SubTab:AddKeybind(opts)
SubTab:AddInput(opts)
SubTab:AddDropdown(opts)
SubTab:AddMultiDropdown(opts)
SubTab:AddSlider(opts)
SubTab:AddColorPicker(opts)
SubTab:AddComponents(list)
```

---

# Exemple complet — hub moderne

```lua
local Library = loadstring(game:HttpGet("URL_DE_TA_LIBRARY"))()

local Window = Library:CreateWindow({
    Name = "Akaz Hub",
    BrandSubtitle = "by as95",
    Logo = "rbxassetid://91555718992391",
    LogoZoom = 1.0,

    Size = UDim2.fromOffset(700, 490),
    Position = UDim2.fromScale(0.5, 0.5),

    LoadingAnimation = true,
    LoadingText = "AKAZ",
    LoadingSubtitle = "HUB",
    LoadingFooter = "AKAZ HUB",
    LoadingDuration = 1.2,
    LoadingBlur = true,

    DisplayOrder = 10,
    ReplaceExisting = true,
    AutoLoad = true,
    ConfigName = "main",
})

local Main = Window:AddTab({
    Name = "Main",
    Icon = "house",
    Subtitle = "General",
})

local General = Main:AddSubTab("General")

General:AddSection("Main")

General:AddToggle({
    Name = "Enabled",
    Description = "Enable the main system.",
    Flag = "Main_Enabled",
    Default = true,
    Callback = function(value)
        print("Enabled =", value)
    end,
})

General:AddSlider({
    Name = "Speed",
    Flag = "Main_Speed",
    Min = 0,
    Max = 100,
    Default = 50,
    Suffix = "%",
    Callback = function(value)
        print("Speed =", value)
    end,
})

General:AddDropdown({
    Name = "Mode",
    Flag = "Main_Mode",
    Options = {"Normal", "Fast", "Custom"},
    Default = "Normal",
    Searchable = true,
    Callback = function(value)
        print("Mode =", value)
    end,
})

General:AddButton({
    Name = "Run",
    Primary = true,
    Callback = function()
        Window:Notify({
            Title = "Success",
            Content = "The action was executed.",
            Type = "Success",
            Duration = 3,
        })
    end,
})
```

---

# Exemple — plusieurs tabs

```lua
local Combat = Window:AddTab({
    Name = "Combat",
    Icon = "swords",
    Subtitle = "Combat controls",
})

local CombatMain = Combat:AddSubTab("Main")

CombatMain:AddToggle({
    Name = "Enabled",
    Flag = "Combat_Enabled",
})

CombatMain:AddSlider({
    Name = "Range",
    Flag = "Combat_Range",
    Min = 1,
    Max = 100,
    Default = 20,
})

local Visuals = Window:AddTab({
    Name = "Visuals",
    Icon = "eye",
    Subtitle = "Visual settings",
})

local ESP = Visuals:AddSubTab("ESP")

ESP:AddToggle({
    Name = "ESP",
    Flag = "Visuals_ESP",
})

ESP:AddColorPicker({
    Name = "Color",
    Flag = "Visuals_Color",
    Default = "#FFFFFF",
})

local Settings = Window:AddTab({
    Name = "Settings",
    Icon = "settings",
    Subtitle = "UI settings",
})

local UI = Settings:AddSubTab("UI")

UI:AddButton({
    Name = "Dark",
    Callback = function()
        Library:SetTheme("Dark")
    end,
})

UI:AddButton({
    Name = "Light",
    Callback = function()
        Library:SetTheme("Light")
    end,
})

UI:AddButton({
    Name = "OLED",
    Callback = function()
        Library:SetTheme("OLED")
    end,
})
```

---

# Exemple — contrôle externe avec flags

Le script qui utilise la bibliothèque peut faire :

```lua
local Window = ...

if Window:Get("Combat_Enabled", false) then
    print("Combat actif")
end
```

Changer une valeur :

```lua
Window:Set("Combat_Range", 75)
```

Cela permet de séparer le code logique du code UI.

---

# Exemple — système State

```lua
Library:SetState("RuntimeMode", "Gaming")

Library:BindState("RuntimeMode", function(mode)
    print("Current mode:", mode)
end)
```

Puis depuis n'importe quel endroit :

```lua
Library:SetState("RuntimeMode", "Coding")
```

---

# Bonnes pratiques

## 1. Utiliser des flags préfixés

Préférer :

```text
Combat_Aimbot
Combat_Range
Visuals_ESP
Visuals_Color
Movement_Speed
Settings_Theme
```

plutôt que :

```text
Enabled
Speed
Color
Mode
```

Cela réduit fortement les collisions de noms dans les gros hubs.

## 2. Garder les callbacks légers

Éviter de mettre de gros traitements bloquants directement dans :

```lua
Callback = function(value)
```

Préférer :

```lua
Callback = function(value)
    task.spawn(function()
        -- logique plus lourde
    end)
end
```

## 3. Utiliser `Description`

Une description courte permet de garder l'UI compréhensible sans ajouter un grand nombre de paragraphs.

## 4. Utiliser un thème global

Au lieu de modifier manuellement les couleurs d'un composant après chaque changement :

```lua
Library:SetTheme("OLED")
```

Utilise les propriétés thémables prévues par la bibliothèque.

## 5. Préférer les noms d'icônes

Exemple :

```lua
Icon = "settings"
```

plutôt que de recopier des IDs partout dans ton script.

## 6. Détruire proprement

Lorsque ton hub est remplacé :

```lua
Library:DestroyAll()
```

Cela évite de laisser plusieurs fenêtres, callbacks ou panneaux actifs.

---

# Limites connues

Cette documentation décrit **le comportement réellement présent dans la version 2.8 documentée**, et non une roadmap.

### Slider entier

Le slider actuel arrondit la valeur à l'entier le plus proche. Il n'expose pas encore de système `Decimals`/`Step` configurable.

### Keybind

Le système de keybind actuel écoute les touches clavier et les boutons souris de base pendant l'affectation, mais il ne fournit pas une API complète de modes `Hold/Toggle` configurables.

### ColorPicker

Le picker gère RGB/HSV et hex, mais ne fournit pas de canal alpha séparé.

### AddButton / AddSection / AddDivider

Ces méthodes peuvent retourner directement une instance Roblox tandis que certains autres composants retournent un handle Lua.

### APIs d'environnement

Les fonctions liées aux fichiers, clipboard, assets custom, HTTP ou certaines fonctions executor ne sont pas garanties dans tous les environnements.

### Audio

Les `SoundId` Roblox peuvent être limités par les permissions du jeu.

### Network / Tag System

Les fonctionnalités qui utilisent le service de présence ou les fonctions admin dépendent de leur backend et des autorisations côté serveur.

---

# Structure technique

Les grandes couches du fichier actuel sont organisées autour de :

```text
Services Roblox
    ↓
Palette / Themes
    ↓
Icon Library
    ↓
Helpers GUI
    ↓
Asset / image resolution
    ↓
Tag System
    ↓
Library state + flags
    ↓
Configuration
    ↓
Music Player
    ↓
CreateWindow
    ↓
Window methods
    ↓
Tab / SubTab
    ↓
UI components
```

### Services Roblox utilisés

La bibliothèque récupère notamment :

```text
TweenService
UserInputService
GuiService
Players
Stats
RunService
AssetService
TextService
HttpService
SoundService
Workspace
```

### Helpers d'assets

Le code possède un système de normalisation d'assets qui reconnaît notamment :

```text
rbxassetid://
rbxthumb://
rbxasset://
rbxgameasset://
```

et il possède également une résolution séparée pour les URLs externes lorsque l'environnement fournit les APIs nécessaires pour télécharger et convertir les fichiers.

---

# Système de nettoyage des connexions

La bibliothèque dispose d'un mécanisme de suivi des connexions via une fonction interne `trackConn`.

Les connexions de plusieurs composants interactifs peuvent ainsi être associées à une fenêtre puis nettoyées lors de `Window:Destroy()`.

Cela concerne notamment des composants comme :

- sliders ;
- color pickers ;
- keybinds ;
- scaling ;
- panneaux et contrôles secondaires.

L'objectif est de réduire les connexions qui continueraient à tourner après destruction de l'UI.

---

# Sécurité et robustesse des callbacks

Les callbacks des composants passent par un helper interne qui vérifie qu'ils sont des fonctions avant exécution.

Exemple conceptuel :

```lua
if typeof(callback) == "function" then
    task.spawn(callback, value)
end
```

Cela permet aux composants de ne pas essayer d'exécuter une valeur qui ne serait pas callable.

---

# Persistence du ScreenGui

La fenêtre possède un garde d'ancestry qui tente de restaurer le `ScreenGui` lorsqu'il est détaché du DataModel sans que la fenêtre ait été volontairement détruite.

Cela vise notamment les situations où un environnement retire temporairement la GUI lors d'un respawn.

La logique distingue :

```text
ScreenGui détaché involontairement
        ↓
réattachement
```

de :

```text
Window:Destroy()
        ↓
aucune restauration
```

---

# Auto scaling et viewport

Sur mobile, la bibliothèque calcule un facteur à partir de :

```text
ViewportSize.X
ViewportSize.Y
Safe inset
Container width
Container height
```

Le facteur est limité afin d'éviter de transformer l'interface en très petit élément illisible.

Le code évite aussi d'upscaler inutilement le conteneur sur mobile afin de conserver un rendu propre des éléments image.

---

# FAQ

## Pourquoi mon logo GitHub ne s'affiche pas ?

Un `ImageLabel` Roblox ne peut pas toujours rendre directement une URL HTTPS. Le chemin recommandé pour le logo principal est un asset Roblox :

```lua
Logo = "rbxassetid://91555718992391"
```

## Comment mettre mon propre logo ?

```lua
Window:SetLogo("rbxassetid://TON_ID")
```

## Comment changer le thème ?

```lua
Library:SetTheme("OLED")
```

## Comment cacher toute l'interface ?

```lua
Window:SetUIVisible(false)
```

## Comment la réafficher ?

```lua
Window:SetUIVisible(true)
```

## Comment changer l'échelle ?

```lua
Window:SetScale(0.85)
```

## Comment connaître la valeur d'un Toggle ?

```lua
local value = Window:Get("MyFlag", false)
```

## Comment modifier la valeur ?

```lua
Window:Set("MyFlag", true)
```

## Comment afficher une notification ?

```lua
Window:Notify({
    Title = "Info",
    Content = "Hello",
    Type = "Info",
})
```

## Comment supprimer la totalité du hub ?

```lua
Library:DestroyAll()
```

## Le loader peut-il être désactivé ?

Oui :

```lua
LoadingAnimation = false
```

## Le blur du loader peut-il être désactivé ?

Oui :

```lua
LoadingBlur = false
```

## Comment créer un tab avec une icône du pack ?

```lua
Window:AddTab({
    Name = "Settings",
    Icon = "settings",
})
```

## Comment ajouter beaucoup de sous-tabs ?

Tu peux simplement créer plusieurs `AddSubTab`. La barre secondaire est prévue pour défiler horizontalement lorsqu'elle déborde.

---

# Changelog résumé

## 2.8

### Branding

- `Akaz Hub` comme nom de marque principal.
- `by as95` comme sous-titre.
- logo Akaz Roblox asset : `91555718992391`.
- Discord asset : `133801393445445`.
- Discord séparé du bloc de nom du joueur.

### Loader

- texte de chargement principal `AKAZ`.
- surface logique agrandie pour améliorer le rendu du texte.
- `TextSize` fixe au lieu de dépendre entièrement de `TextScaled` dans un petit cadre.
- blur de chargement nommé `AkazLoadingBlur`.
- nettoyage d'un blur stale lors d'une nouvelle exécution.
- nettoyage du blur lors de la destruction.

### UI

- accent monochrome/gris.
- icon pack embarqué.
- icônes images en `Fit`.
- support des noms d'icônes du pack.
- thème et recoloration des icônes via attributs.

### Interaction

- effet de pression global des boutons.
- minimisation animée.
- restauration animée.
- scaling mobile.
- hotbar de tabs avec scroll horizontal.

### Persistence

- dossier de configs : `AkazHub/configs`.
- AutoLoad configurable.
- AutoSave différé.
- métadonnées de config avec version et thème/scale au niveau fenêtre.

### Robustesse

- `SoundService` disponible globalement pour le loader.
- suivi de connexions de plusieurs composants.
- nettoyage lors de `Window:Destroy()`.
- nettoyage du loader.
- restauration du `ScreenGui` en cas de détachement involontaire.

---

# Référence rapide — copier/coller

```lua
local Library = loadstring(game:HttpGet("URL_DE_TA_LIBRARY"))()

local Window = Library:CreateWindow({
    Name = "Akaz Hub",
    BrandSubtitle = "by as95",
    Logo = "rbxassetid://91555718992391",
    Size = UDim2.fromOffset(700, 490),
    LoadingAnimation = true,
    LoadingText = "AKAZ",
    LoadingSubtitle = "HUB",
    LoadingFooter = "AKAZ HUB",
    LoadingBlur = true,
    ConfigName = "main",
})

local Tab = Window:AddTab({
    Name = "Main",
    Icon = "house",
    Subtitle = "Main controls",
})

local Sub = Tab:AddSubTab("General")

Sub:AddSection("General")

Sub:AddToggle({
    Name = "Enabled",
    Flag = "Enabled",
    Default = false,
    Callback = function(v)
        print("Enabled", v)
    end,
})

Sub:AddButton({
    Name = "Execute",
    Primary = true,
    Callback = function()
        Window:Notify({
            Title = "Success",
            Content = "Executed.",
            Type = "Success",
            Duration = 3,
        })
    end,
})

Sub:AddDropdown({
    Name = "Mode",
    Flag = "Mode",
    Options = {"A", "B", "C"},
    Default = "A",
    Searchable = true,
})

Sub:AddMultiDropdown({
    Name = "Targets",
    Flag = "Targets",
    Options = {"Players", "NPCs", "Bosses"},
    Default = {"Players"},
})

Sub:AddSlider({
    Name = "Speed",
    Flag = "Speed",
    Min = 0,
    Max = 100,
    Default = 50,
    Suffix = "%",
})

Sub:AddInput({
    Name = "Username",
    Flag = "Username",
    Placeholder = "Username...",
})

Sub:AddKeybind({
    Name = "Hotkey",
    Flag = "Hotkey",
    Default = Enum.KeyCode.RightShift,
})

Sub:AddColorPicker({
    Name = "Color",
    Flag = "Color",
    Default = "#FFFFFF",
})

Sub:AddParagraph({
    Title = "Info",
    Text = "Akaz Hub UI 2.8",
})

-- UI control
Window:SetScale(0.9)
Window:SetUIVisible(true)

-- Theme
Library:SetTheme("OLED")

-- Config
Window:SaveConfig("main")

-- Cleanup
-- Library:DestroyAll()
```

---

# Conclusion

**Akaz Hub UI 2.8** est conçu comme une bibliothèque de UI complète plutôt qu'un simple ensemble de boutons. Le point d'entrée est `CreateWindow`, puis l'interface se construit progressivement `Window → Tab → SubTab → Component`.

Pour un projet important, la combinaison recommandée est :

```text
CreateWindow
    +
Tabs / SubTabs
    +
Flags
    +
State
    +
Theme
    +
Notifications
    +
Config
    +
Responsive scaling
```

Le résultat est une structure où la couche visuelle reste séparée de la logique du script utilisateur, tout en conservant une API Luau compacte.

---

## Identité actuelle

```text
Akaz Hub
by as95

Version: 2.8
Logo: rbxassetid://91555718992391
Discord logo: rbxassetid://133801393445445
Discord: https://discord.gg/PCep5XRFS4
Config folder: AkazHub/configs
Music folder: AkazMusic
```

