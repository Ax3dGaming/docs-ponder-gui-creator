# Label (Text)

The **Label** component is used to render text strings onto the interface. It maps to Minecraft's native `StringWidget` component.

---

## Inspector Properties

When a Label is selected, you can configure the following properties:

* **Text Content**: The text string displayed in-game. Supports:
  * **Dynamic Placeholders**: Embed placeholders like `%player_name%` or `%health%` to display active player stats.
  * **Minecraft Color Coding**: Uses Minecraft standard color coding formatting in the React editor canvas.
* **Use Translation Key**: Wraps the text inside a `Component.translatable()` in the generated Java code. If disabled, generates `Component.literal()`.
* **Text Color**: A hexadecimal color code (e.g., `0xFFFFFF` for white, `0xFF5555` for red) to colorize the string.
* **Layout**: Configure coordinates (X, Y) and size (Width, Height). The label's bounding box determines centering and text wrapping behavior.

---

## NeoForge Java Generation

Depending on where the Label is placed in your canvas hierarchy, the generator will produce the following Java code:

### Global Fields
```java
private net.minecraft.client.gui.components.StringWidget label_1720000000;
```

### Root Canvas Initialization
If placed directly on the background:
```java
this.label_1720000000 = new net.minecraft.client.gui.components.StringWidget(this.leftPos + 40, this.topPos + 25, 100, 20, Component.literal("Welcome, Admin!"), this.font).setColor(0xFF5555);
this.addRenderableOnly(this.label_1720000000);
```

### Inside a Scroll Panel
If parented to a Scroll Panel:
```java
this.label_1720000000 = new net.minecraft.client.gui.components.StringWidget(10, 15, 100, 20, Component.literal("Scrollable Entry"), this.font).setColor(0xFFFFFF);
this.scroll_panel_1.addWidget(this.label_1720000000);
```