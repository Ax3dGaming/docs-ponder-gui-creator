# Button

The **Button** component represents a standard, clickable Minecraft button widget. It translates into Minecraft's native `net.minecraft.client.gui.components.Button` class.

---

## Inspector Properties

* **Display Text**: The text rendered on the button. Supports dynamic placeholders (e.g. `%player_name%`).
* **Use Translation Key**: Wraps the text inside `Component.translatable()` rather than `Component.literal()`.
* **On Click Action**: Set the behavior when clicked. Actions range from simple command execution to playing audio, opening another screen, copying text, or sending custom network packets (see the Action System section in the [Overview](../index.md)).
* **On Hover Action**: Play audio or print debugging statements when hovered.
* **Render Conditions**: Conditionally display this button only if the player is OP, in Creative mode, or holds a specific item.
* **Layout**: Change size (Width, Height) and coordinates (X, Y).

---

## NeoForge Java Generation

Here is an example of the Java code exported for a Button configured to execute a command:

### Global Fields
```java
private net.minecraft.client.gui.components.Button button_1720000000;
```

### Canvas Initialization
If placed directly on the root screen:
```java
// Button: button_1720000000
this.button_1720000000 = net.minecraft.client.gui.components.Button.builder(Component.literal("Kick Player"), button -> {
    if (this.minecraft.player != null) this.minecraft.player.connection.sendCommand("kick BadPlayer");
}).bounds(this.leftPos + 50, this.topPos + 80, 80, 20).build();
this.addRenderableWidget(this.button_1720000000);
```

### Inside a Scroll Panel
If parented to a scroll container:
```java
this.button_1720000000 = net.minecraft.client.gui.components.Button.builder(Component.literal("Scroll Action"), button -> {
    this.onClose();
}).bounds(10, 10, 60, 20).build();
this.scroll_panel_1.addWidget(this.button_1720000000);
```