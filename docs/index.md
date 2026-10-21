# Ponder GUI Creator - Overview

Welcome to the **Ponder GUI Creator** documentation. This tool is a powerful, web-based WYSIWYG (What You See Is What You Get) editor designed to accelerate the creation of NeoForge graphical user interfaces (GUIs).

---

## Key Features

* **Drag & Drop Workspace**: Visually place, resize, and arrange your components exactly as they will appear in-game.
* **Instant Java Generation**: Export production-ready NeoForge Java code (`Screen` and `Menu` classes) with a single click.
* **Asset Manager Integration**: Load your local mod's `assets/` folder to preview your actual Minecraft textures inside the browser.
* **Interactive Hierarchy Tree**: View and manage the parent-child relationships of widgets, allowing easy sorting, nested styling, and group ordering.
* **Grid Snapping**: Align components cleanly using the configurable snap-to-grid grid system (2px, 4px, 8px increments).
* **Project Saving**: Save your workspace as a `.json` file and load it back later to resume your work.

---

## Canvas Controls & Shortcuts

The editor provides a responsive canvas with shortcuts designed to mirror professional design tools:

* **Drag & Drop**: Drag a tool from the **Components** list in the left panel and drop it onto the canvas (or onto a container like a `Scroll Panel` or `Group`).
* **Zooming**: Hold **Ctrl** and scroll the mouse wheel (or use the Zoom buttons in the top right) to scale the canvas from 20% to 500%.
* **Panning**: Hold the **Middle Mouse Button** (or hold **Alt** while clicking and dragging) to pan the canvas around the workspace.
* **Duplicate / Copy & Paste**: Press **Ctrl + C** to copy the selected component(s) and **Ctrl + V** to paste them at a slight offset.
* **Deletion**: Select any component and press **Delete** or **Backspace** to remove it.
* **Hierarchy Sorting**: Use **Move Forward** and **Move Backward** in the inspector to modify the layer render order (Z-index).

---

## Action Actions System

You can configure interactive events directly from the Properties Inspector:

### On Click Actions
When a player clicks an interactive component (e.g., a Button, Image Button, or Switch), it can trigger:  
1. **Print to Console**: Prints a custom string to stdout in Java (`System.out.println()`).  
2. **Play Sound**: Plays a sound event in-game (defaults to `minecraft:ui.button.click`).  
3. **Open Another Screen**: Transitions the client screen directly to another GUI class (e.g., `OptionsScreen`).  
4. **Open Web URL**: Prompts the player to open a website URL (wrapped using Minecraft's `ConfirmLinkScreen`).  
5. **Toggle Component Visibility**: Inverts the visible state of another component by target ID.  
6. **Close GUI**: Closes the current screen via `this.onClose()`.  
7. **Send Network Packet**: Pre-generates boilerplate code to send a network packet to the server.  
8. **Execute Server Command**: Runs a server-side command (e.g., `/ban ${player_name_box}`). You can interpolate text from an input field (`EditBox`) by placing the target component ID inside `${...}` blocks.  
9. **Copy to Clipboard**: Copies a static string or an input field's dynamic value to the system clipboard.  

### On Hover Actions
Triggers when a player hovers over a component:  
1. **Play Sound**: Plays a sound event upon hover.  
2. **Print to Console**: Prints a log message when hovered.  

---

## Render Conditions

Components can be dynamically rendered and initialized based on the player's state. If a condition is set, the widget is only registered if the check passes in Java:  
1. **Require OP Permission**: Check if player has permission level 2 or higher (`player.hasPermissions(2)`).  
2. **Require Creative Mode**: Check if player is in Creative mode (`player.getAbilities().instabuild`).  
3. **Require Specific Item**: Check if the player's inventory contains a target item (e.g. `minecraft:diamond`).  

---

## Dynamic Text Placeholders

You can embed special placeholder tags into the **Text** properties of labels and widgets. The Java generator will automatically parse these into active, reactive code:

| Placeholder | In-Editor Preview | Generated NeoForge Java Code |
|-------------|-------------------|-----------------------------|
| `%player_name%` | `Steve` | `net.minecraft.client.Minecraft.getInstance().player.getName().getString()` |
| `%health%` | `20.0` | `net.minecraft.client.Minecraft.getInstance().player.getHealth()` |
| `%xp%` | `15` | `net.minecraft.client.Minecraft.getInstance().player.experienceLevel` |
| `%x_pos%` | `0` | `net.minecraft.client.Minecraft.getInstance().player.getX()` |
| `%y_pos%` | `64` | `net.minecraft.client.Minecraft.getInstance().player.getY()` |
| `%z_pos%` | `0` | `net.minecraft.client.Minecraft.getInstance().player.getZ()` |

Placeholders can be chained or concatenated with standard text. For example, `Player: %player_name% (XP: %xp%)` will generate:
`Component.literal("Player: ").append(Component.literal(String.valueOf(Minecraft.getInstance().player.getName().getString()))).append(Component.literal(" (XP: ")).append(Component.literal(String.valueOf(Minecraft.getInstance().player.experienceLevel))).append(Component.literal(")"))`