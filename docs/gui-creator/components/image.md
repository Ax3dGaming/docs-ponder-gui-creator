# Static Image

The **Static Image** component renders custom graphic assets onto the screen canvas. It uses standard Minecraft coordinate rendering and does not instantiate a physical Java button class; instead, it is directly drawn to the screen backdrop using `guiGraphics.blit()`.

---

## Inspector Properties

* **Texture Source Type**: Choose between a local mod asset file or a remote HTTP URL.
* **Texture Path / URL**:
  * Local resource location path (e.g. `yourmod:textures/gui/banner.png`).
  * Remote HTTP URL (e.g. `https://example.com/logo.png`). If set to URL, the generator registers an `HttpTexture` at runtime.
* **U & V Offsets**: Pixels to offset horizontally (U) and vertically (V) inside the source texture sheet. This lets you blit a specific icon from a combined sprite sheet.
* **Overlay Tint Color**: Apply a hexadecimal color overlay (e.g., `0xFF5555` to red-tint the image). Defaults to `0xFFFFFF` (white, no tint).
* **Layout**: Dimension (Width, Height) and position offsets (X, Y).

---

## NeoForge Java Generation

Because images are static, they do not declare variables as class fields unless they register a dynamic HTTP URL. They write blit commands inside the `renderBg` method:

### Root Canvas Rendering (Local Asset)
```java
// Under renderBg method:
// Static Image: image_1720000000
guiGraphics.blit(ResourceLocation.parse("mymod:textures/gui/logo.png"), this.leftPos + 10, this.topPos + 10, 0, 0, 64, 64);
```

### URL Image Registration & Drawing
If an HTTP URL is used, the image registers a dynamic URL asset at initialization and draws it:
```java
// Under init():
// URL Image Registration: image_1720000000
net.minecraft.client.renderer.texture.HttpTexture httpTex_image_1720000000 = new net.minecraft.client.renderer.texture.HttpTexture(null, "https://example.com/image.png", net.minecraft.resources.ResourceLocation.parse("minecraft:textures/gui/container/inventory.png"), false, null);
net.minecraft.client.Minecraft.getInstance().getTextureManager().register(net.minecraft.resources.ResourceLocation.parse("mymod:url_image_image_1720000000"), httpTex_image_1720000000);

// Under renderBg():
guiGraphics.blit(net.minecraft.resources.ResourceLocation.parse("mymod:url_image_image_1720000000"), this.leftPos + 10, this.topPos + 10, 0, 0, 64, 64);
```

### Color Tintelated Rendering
If a custom color (e.g., green `0x00FF00`) is configured:
```java
// Under renderBg():
int color_image_1720000000 = (int) Long.parseLong("0xFF00FF00".replace("0x", ""), 16);
guiGraphics.setColor(((color_image_image_1720000000 >> 16) & 0xFF) / 255.0F, ((color_image_image_1720000000 >> 8) & 0xFF) / 255.0F, (color_image_image_1720000000 & 0xFF) / 255.0F, ((color_image_image_1720000000 >> 24) & 0xFF) / 255.0F);
guiGraphics.blit(ResourceLocation.parse("mymod:textures/gui/logo.png"), this.leftPos + 10, this.topPos + 10, 0, 0, 64, 64);
guiGraphics.setColor(1.0F, 1.0F, 1.0F, 1.0F); // Reset color tint
```