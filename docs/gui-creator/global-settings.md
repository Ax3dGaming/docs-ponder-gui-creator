# Global Configuration

Before adding widgets, you must configure the screen's core properties and assets. These settings guide how the editor lays out components and dictates the structure of the generated NeoForge Java classes.

---

## Class & Package Setup

In the left panel's **Class Configuration** section, you can configure the Java class structure:

* **Mod ID**: The unique identifier of your mod (e.g., `pondertestgui`). This maps resource locations and defines default packages.
* **Class Name**: The prefix name of the generated Java files. For instance, entering `MyCustom` will export two files:
  * `MyCustomScreen.java`
  * `MyCustomMenu.java`
* **Menu Package (Optional)**: The package declaration for the Menu class. Defaults to `com.<modid>.world.inventory`.
* **Screen Package (Optional)**: The package declaration for the Screen class. Defaults to `com.<modid>.client.gui`.
* **In-Game Title**: The screen title text passed to the `Screen` constructor.
* **GUI Width & Height (px)**: The container's viewport dimension in Minecraft pixels (e.g., 176x166 for a standard vanilla chest, 256x256 for large menus).

---

## Screen Background Configuration

You can set up a custom visual background texture for your GUI. Under the inspector's background panel:

* **Background Type**: Set to `CUSTOM` to map a custom background image.
* **Background Texture**: Select a texture indexed by the **Asset Manager** (e.g., `pondertestgui:textures/gui/bg.png`).
* **Texture File Width & Height**: The actual pixel size of your source PNG file (typically 256x256). This ensures Minecraft's `guiGraphics.blit()` scales and coordinates UVs accurately without distorting.

---

## Lifecycle Actions & Tick Code

You can run custom code at key moments in the Screen's lifespan:

### On Init Actions
Executes when the GUI first launches:

* **None**: No action.
* **Play Sound**: Plays a sound event immediately when opened.
* **Send Packet**: Fires a client-to-server network packet on initialization.

### Custom Java Injections
Directly inject custom code blocks within the inspector's advanced fields:

* **Custom Tick Code**: Injected into the `containerTick()` method to handle animations, updates, or state checks every tick.
* **Custom Close Code**: Injected into the `onClose()` method, perfect for executing cleanup or sending exit sync packets to the server.

---

## Asset Manager Integration

The **Asset Manager** allows the GUI creator to index your local development assets so you can preview custom textures on the canvas.

### How to use:
1. Under the **Asset Manager** foldout in the left panel, click **Load Assets Folder**.
2. Select your mod project's local directory: `src/main/resources/assets`.
3. The browser will search recursively and index all `.png` files under any `textures/` subdirectory.
4. When selecting images or buttons, the texture drop-down menus will automatically show choices using standard resource locations (e.g. `yourmodid:textures/gui/my_widget.png`).