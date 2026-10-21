# Slider

The **Slider** component is an interactive bar element that lets players select numerical ranges. It maps directly to an anonymous subclass of Minecraft's native `AbstractSliderButton`.

---

## Inspector Properties

* **Label Text**: The text displayed on the slider handle (e.g. `Volume`).
* **Format & Suffixing**:
  * **Show Value suffix**: Appends the current numeric value after the label (e.g., `Volume: 50`).
  * **Number Format**: Set the decimal precision (`x` for integers, `x.x` for tenths, `x.xx` for hundredths, or `x.xxx` for thousandths).
* **Slider Values**:
  * **Min / Max Value**: Bounds of the slider.
  * **Default / Current Value**: The initial handle selection.
* **Custom Textures**:
  * **Use Custom Textures**: Enable custom skinning.
  * **Track Texture**: Resource location path for the slider background track.
  * **Thumb Texture**: Resource location path for the sliding handle.
  * **Thumb Width**: Width of the sliding handle (defaults to 8px).
* **Slider Action Triggers**:
  * **Update Target Scale**: Directly adjust the scaling variable of another rendering component (like `Item Display` or `Entity Display`).
  * **Update Target Rotation**: Control the Y-rotation angle variable of an `Entity Display` component.
  * **Update Progress Bar**: Set a progress bar variable dynamically with the slider's value.
  * **Target ID**: The identifier of the widget being manipulated.

---

## NeoForge Java Generation

A Slider creates an anonymous instance of `AbstractSliderButton` containing message formatting and value synchronization handlers:

### Global Fields
```java
private net.minecraft.client.gui.components.AbstractSliderButton slider_1720000000;
```

### Canvas Initialization (Standard Texture)
```java
// Slider Button: slider_1720000000
this.slider_1720000000 = new net.minecraft.client.gui.components.AbstractSliderButton(this.leftPos + 40, this.topPos + 60, 120, 20, Component.empty(), 0.5F) {
    {
        this.updateMessage();
    }
    @Override 
    protected void updateMessage() { 
        this.setMessage(Component.empty().append(Component.literal("Volume")).append(Component.literal(": " + (int)(this.value * 100.0)))); 
    }
    @Override 
    protected void applyValue() { 
        // Logic bound to slider changes: e.g. update target progress bar val
        val_progress_bar_123 = (float)(this.value * 100.0); 
    }
};
this.addRenderableWidget(this.slider_1720000000);
```

### Canvas Initialization (Custom Textures Skinned)
```java
this.slider_1720000000 = new net.minecraft.client.gui.components.AbstractSliderButton(this.leftPos + 40, this.topPos + 60, 120, 20, Component.empty(), 0.5F) {
    {
        this.updateMessage();
    }
    @Override protected void updateMessage() { this.setMessage(Component.literal("Speed: " + (int)(this.value * 10.0))); }
    @Override protected void applyValue() { }
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        // Renders the custom track background
        guiGraphics.blit(ResourceLocation.parse("mymod:textures/gui/slider_track.png"), this.getX(), this.getY(), 0, 0, this.width, this.height, this.width, this.height);
        // Calculates handle offset and blits the custom thumb
        int thumbX = this.getX() + (int)(this.value * (this.width - 8));
        guiGraphics.blit(ResourceLocation.parse("mymod:textures/gui/slider_thumb.png"), thumbX, this.getY(), 0, 0, 8, this.height, 8, this.height);
        
        int textColor = this.active ? 16777215 : 10526880;
        guiGraphics.drawCenteredString(net.minecraft.client.Minecraft.getInstance().font, this.getMessage(), this.getX() + this.width / 2, this.getY() + (this.height - 8) / 2, textColor | net.minecraft.util.Mth.ceil(this.alpha * 255.0F) << 24);
    }
};
this.addRenderableWidget(this.slider_1720000000);
```