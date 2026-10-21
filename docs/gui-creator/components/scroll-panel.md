# Scroll Panel

The **Scroll Panel** is a container component that hosts child widgets (like labels, buttons, or checkboxes) and provides smooth scrolling overflow vertically or horizontally.

Since Minecraft does not have a native scroll-panel widget, the exporter creates a custom, reusable **`ScrollPanelWidget` class** in your mod.

---

## Inspector Properties

* **Scroll Orientation**:
  * **Vertical Scroll**: Enable vertical scrolling. Disables horizontal scrolling.
  * **Horizontal Scroll**: Enable horizontal scrolling. Disables vertical scrolling.
* **Scroll Boundary**:
  * **Max Scroll Distance**: The virtual canvas size of the scrollable content (e.g., if the panel height is 100px, but you place content spanning 300px, set this to 300).
* **Borders & Backgrounds**:
  * **Show Border**: Draw a visual frame border around the viewport.
  * **Border Color**: Hexadecimal border color (e.g., `0x803B82F6`).
  * **Background Texture**: Resource location path of the viewport inner background.
* **Scrollbar Skinning**:
  * **Vertical Track Texture & Vertical Thumb Texture**: Customize the vertical scrollbar assets.
  * **Horizontal Track Texture & Horizontal Thumb Texture**: Customize the horizontal scrollbar assets.
* **Widget Package**: The target Java package declarations for the generated `ScrollPanelWidget` (defaults to `com.<modid>.client.gui.components`).

---

## Screen Integration

The Scroll Panel wraps child widgets by managing clipping boundaries via scissor boxes (`guiGraphics.enableScissor`). Inside your Screen, the child widgets are positioned relative to the panel.

### Global Fields
```java
private com.mymod.client.gui.components.ScrollPanelWidget scroll_panel_1;
private net.minecraft.client.gui.components.Button button_inside_scroll;
```

### Canvas Initialization
```java
// ScrollPanel Container: scroll_panel_1
this.scroll_panel_1 = new com.mymod.client.gui.components.ScrollPanelWidget(this.leftPos + 10, this.topPos + 10, 100, 100, 100, 250, false, true, true, 0x803B82F6);
this.scroll_panel_1.setBackgroundTexture("mymod:textures/gui/panel_bg.png");
this.scroll_panel_1.setVerticalScrollTextures("mymod:textures/gui/scroll_track.png", "mymod:textures/gui/scroll_thumb.png");

// Add children positioned relative to the panel coordinates:
this.button_inside_scroll = net.minecraft.client.gui.components.Button.builder(Component.literal("Option 1"), button -> {
    System.out.println("Clicked!");
}).bounds(10, 15, 80, 20).build();

this.scroll_panel_1.addWidget(this.button_inside_scroll);
this.addRenderableWidget(this.scroll_panel_1);
```

---

## Custom ScrollPanelWidget Class

When exporting your GUI, the exporter generates a standalone utility class `ScrollPanelWidget.java`. Make sure to place this file inside your mod code matching the package path.

Key features handled automatically by `ScrollPanelWidget.java`:
* Smooth scroll velocity interpolation (`targetScrollY` eased with `scrollAmountY`).
* Viewport clipping logic using `enableScissor` so children rendering outside bounds are hidden.
* Forwarding click, hover, drag, text inputs, and scroll wheel events to child elements only if they sit inside the active panel viewport.