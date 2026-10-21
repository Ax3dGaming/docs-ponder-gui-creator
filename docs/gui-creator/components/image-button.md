# Image Button

The **Image Button** component is a clickable button that displays custom graphics for normal and hover states. In Java, it exports as a custom anonymous implementation of Minecraft's `AbstractWidget`.

---

## Inspector Properties

* **Texture Source Type**: Choose whether the texture is loaded from a local mod asset file or a remote HTTP URL.
* **Texture Path / URL**: 
  * If local: The indexed asset location (e.g. `yourmod:textures/gui/button_normal.png`).
  * If remote URL: A direct web address (e.g. `https://example.com/button.png`). The generator will register a Minecraft `HttpTexture` to fetch and render the image dynamically.
* **Hover Texture**: A separate texture path or URL displayed when the player's cursor sits inside the button bounds. If left blank, the generator applies a fallback gray color tint to the normal texture upon hover.
* **On Click / Hover Actions**: Bind interactive behaviors (e.g. Command execution, play audio, network packet, close GUI, etc.).
* **Layout**: Standard dimensions (Width, Height) and offset coordinate points.

---

## NeoForge Java Generation

An Image Button translates into an anonymous subclass of `AbstractWidget` that manages hover state switching and handles click hooks:

### Global Fields
```java
private net.minecraft.client.gui.components.AbstractWidget image_btn_1720000000;
```

### Canvas Initialization (Local Texture Example)
```java
this.image_btn_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 30, this.topPos + 40, 20, 20, Component.empty()) {
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        boolean hovered = mouseX >= this.getX() && mouseX <= this.getX() + this.width && mouseY >= this.getY() && mouseY <= this.getY() + this.height;
        net.minecraft.resources.ResourceLocation tex = net.minecraft.resources.ResourceLocation.parse("mymod:textures/gui/btn_normal.png");
        if (hovered && this.active) {
            tex = net.minecraft.resources.ResourceLocation.parse("mymod:textures/gui/btn_hover.png");
        }

        guiGraphics.blit(tex, this.getX(), this.getY(), 0, 0, this.width, this.height, this.width, this.height);
    }

    @Override
    public boolean mouseClicked(double mouseX, double mouseY, int button) {
        if (this.active && this.visible && this.clicked(mouseX, mouseY)) {
            // Click Action: Play Sound
            net.minecraft.client.Minecraft.getInstance().getSoundManager().play(net.minecraft.client.resources.sounds.SimpleSoundInstance.forUI(net.minecraft.sounds.SoundEvents.UI_BUTTON_CLICK.value(), 1.0F));
            return true;
        }
        return false;
    }

    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.addRenderableWidget(this.image_btn_1720000000);
```

### Canvas Initialization (Remote URL Texture Example)
When using a web URL, the generator automatically registers a web-sourced texture at runtime:
```java
// Under init()
net.minecraft.resources.ResourceLocation urlNormal = net.minecraft.resources.ResourceLocation.parse("mymod:url_imagebtn_image_btn_1720000000_normal");
net.minecraft.client.Minecraft.getInstance().getTextureManager().register(urlNormal, new net.minecraft.client.renderer.texture.HttpTexture(null, "https://example.com/button.png", net.minecraft.resources.ResourceLocation.parse("minecraft:textures/gui/container/inventory.png"), false, null));

this.image_btn_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 30, this.topPos + 40, 20, 20, Component.empty()) {
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        // Renders using the registered runtime HttpTexture
        guiGraphics.blit(urlNormal, this.getX(), this.getY(), 0, 0, this.width, this.height, this.width, this.height);
    }
    // ... mouseClicked and narration overrides ...
};
this.addRenderableWidget(this.image_btn_1720000000);
```