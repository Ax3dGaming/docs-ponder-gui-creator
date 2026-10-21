# Progress Bar

The **Progress Bar** component visualizes progress or resource bars (e.g., player health, XP thresholds, item processing times). It maps to a custom `AbstractWidget` in Java and supports both solid color drawing and custom textured graphics.

---

## Inspector Properties

* **Numerical Bounds**:
  * **Min / Max Value**: Minimum and maximum capacity. These fields support literal values or dynamic placeholders.
  * **Current Value**: The active bar amount. Can use dynamic placeholders (e.g., `%health%` or `%xp%`).
* **Fill Direction**: Dictates how the fill graphic expands:
  * `LTR` (Left to Right)
  * `RTL` (Right to Left)
  * `TTB` (Top to Bottom)
  * `BTT` (Bottom to Top)
* **Visual Style**:
  * **Solid Color Mode**:
    * **Fill Color**: Hexadecimal color code for the progress fill (e.g., `0x10B981`).
    * **Background Color**: Hexadecimal color code for the empty background container (e.g., `0x3F3F46`).
  * **Custom Texture Mode**:
    * **Use Custom Textures**: Enable custom skinning.
    * **Background Texture**: Path to the background PNG asset.
    * **Fill Texture**: Path to the fill overlay PNG asset.
* **Layout**: Width, Height, and Position.

---

## NeoForge Java Generation

A Progress Bar is generated as an anonymous `AbstractWidget` that calculates the ratio dynamically inside `renderWidget`:

### Global Fields
```java
private net.minecraft.client.gui.components.AbstractWidget progress_bar_1720000000;
private float val_progress_bar_1720000000 = (float)(0.0); // Bound value
```

### Canvas Initialization (Solid Color Example)
```java
// Progress Bar: progress_bar_1720000000
this.progress_bar_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 25, this.topPos + 110, 100, 12, Component.empty()) {
    private float min = (float)(0);
    private float max = (float)(100);

    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        float progress = Math.max(0.0f, Math.min(1.0f, (val_progress_bar_1720000000 - min) / (max - min)));
        int color = (int) Long.parseLong("0xFF10B981".replace("0x", ""), 16);
        
        // Draw background container
        guiGraphics.fill(this.getX(), this.getY(), this.getX() + this.width, this.getY() + this.height, (int) Long.parseLong("0xFF3F3F46".replace("0x", ""), 16));
        
        // Draw LTR progress overlay
        int fgWidth = (int)(this.width * progress);
        if (fgWidth > 0) {
            guiGraphics.fill(this.getX(), this.getY(), this.getX() + fgWidth, this.getY() + this.height, color);
        }
    }

    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.addRenderableWidget(this.progress_bar_1720000000);
```

### Canvas Initialization (Custom Texture Example)
```java
this.progress_bar_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 25, this.topPos + 110, 100, 12, Component.empty()) {
    private float min = (float)(0);
    private float max = (float)(20); // E.g. Player Health Max
    private final net.minecraft.resources.ResourceLocation bgTex = net.minecraft.resources.ResourceLocation.parse("mymod:textures/gui/health_bar_bg.png");
    private final net.minecraft.resources.ResourceLocation fillTex = net.minecraft.resources.ResourceLocation.parse("mymod:textures/gui/health_bar_fill.png");

    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        float progress = Math.max(0.0f, Math.min(1.0f, (val_progress_bar_1720000000 - min) / (max - min)));
        guiGraphics.blit(bgTex, this.getX(), this.getY(), 0, 0, this.width, this.height, this.width, this.height);
        
        int fgWidth = (int)(this.width * progress);
        if (fgWidth > 0) {
            guiGraphics.blit(fillTex, this.getX(), this.getY(), 0, 0, fgWidth, this.height, this.width, this.height);
        }
    }
    // ... narration override ...
};
this.addRenderableWidget(this.progress_bar_1720000000);
```