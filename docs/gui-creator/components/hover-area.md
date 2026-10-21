# Hover Area (Tooltip)

The **Hover Area** component defines an invisible interactive zone on the GUI screen. When a player hovers their cursor over this region, it displays a standard Minecraft tooltip. In Java, this translates into an empty `AbstractWidget` bound with a `Tooltip`.

---

## Inspector Properties

* **Tooltip Text**: The text message displayed in the tooltip window. Supports dynamic placeholders (e.g. `%player_name%` to display personalized greetings).
* **Use Translation Key**: Wraps the tooltip text in `Component.translatable()`. If disabled, defaults to `Component.literal()`.
* **Render Conditions**: Check OP permissions, Creative mode, or item existence before initializing this tooltip area.
* **Layout**: Dimension bounds (Width, Height) and absolute/relative coordinate offsets.

---

## NeoForge Java Generation

A Hover Area is generated as an invisible `AbstractWidget` that registers a `Tooltip` instance:

### Global Fields
```java
private net.minecraft.client.gui.components.AbstractWidget tooltip_zone_1720000000;
```

### Canvas Initialization
If placed directly on the root screen:
```java
// Hover Tooltip Area: tooltip_zone_1720000000
this.tooltip_zone_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 10, this.topPos + 10, 32, 32, Component.empty()) {
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {}
    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.tooltip_zone_1720000000.setTooltip(net.minecraft.client.gui.components.Tooltip.create(Component.literal("Clicking here does nothing, but hovering is informative!"));
this.addRenderableWidget(this.tooltip_zone_1720000000);
```