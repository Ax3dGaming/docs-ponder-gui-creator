# Item Display

The **Item Display** component renders a 3D-rendered item sprite from the Minecraft item registry onto the canvas. It allows custom scaling, multi-axis rotations, hover tooltips, and automated spin animations.

Instead of registering a physical widget, the item is drawn dynamically inside `renderBg` using Matrix Pose transformations and `guiGraphics.renderFakeItem()`.

---

## Inspector Properties

* **Item Registry ID**: The item namespace identifier (e.g. `minecraft:diamond` or `yourmod:custom_sword`).
* **Scale**: Factor to scale the item size up or down (e.g. `2.0` renders a double-sized sprite).
* **Rotations**: Customize the 3D projection tilt of the item:
  * **Rotation X / Y / Z**: Pitch, Yaw, and Roll angles in degrees.
* **Animations**:
  * **Animate Rotation**: If enabled, automatically spins the item on the Y-axis inside the GUI (uses the game's clock millisecond cycle).
* **Hover Tooltip**:
  * **Show Tooltip**: If enabled, hovering over the item's bounding box draws the item's standard in-game tooltip (including registry ID, lore, and NBT attributes).
* **Layout**: Bounding box size (Width, Height) and offset coordinate points.

---

## NeoForge Java Generation

The generator creates float fields to track transformations, allowing you to manipulate scales and rotations at runtime (for example, bound to a Slider). The drawing commands sit inside the `renderBg` method:

### Global Fields
```java
private float scale_item_disp = 1.0f;
private float rotX_item_disp = 0.0f;
private float rotY_item_disp = 0.0f;
private float rotZ_item_disp = 0.0f;
```

### Canvas Rendering (Animated Spin + Tooltip Example)
```java
// Under renderBg method:
guiGraphics.pose().pushPose();
// Translate to center pivot point of the 16x16 sprite area
guiGraphics.pose().translate(this.leftPos + 50 + 8.0f, this.topPos + 30 + 8.0f, 0.0f);

if (this.scale_item_disp != 1.0f) {
    guiGraphics.pose().scale(this.scale_item_disp, this.scale_item_disp, 1.0f);
}
if (this.rotX_item_disp != 0) {
    guiGraphics.pose().mulPose(com.mojang.math.Axis.XP.rotationDegrees(this.rotX_item_disp));
}

// Animate Y-rotation based on game system tick timer
guiGraphics.pose().mulPose(com.mojang.math.Axis.YP.rotationDegrees(this.rotY_item_disp + (float)((net.minecraft.Util.getMillis() % 3600L) / 10.0f)));

if (this.rotZ_item_disp != 0) {
    guiGraphics.pose().mulPose(com.mojang.math.Axis.ZP.rotationDegrees(this.rotZ_item_disp));
}

// Render the item centering back onto coordinates
guiGraphics.renderFakeItem(new ItemStack(net.minecraft.core.registries.BuiltInRegistries.ITEM.get(net.minecraft.resources.ResourceLocation.parse("minecraft:diamond"))), -8, -8);
guiGraphics.pose().popPose();

// Tooltip collision detector
if (mouseX >= this.leftPos + 50 && mouseX <= this.leftPos + 50 + 16 && mouseY >= this.topPos + 30 && mouseY <= this.topPos + 30 + 16) {
    guiGraphics.renderTooltip(net.minecraft.client.Minecraft.getInstance().font, new ItemStack(net.minecraft.core.registries.BuiltInRegistries.ITEM.get(net.minecraft.resources.ResourceLocation.parse("minecraft:diamond"))), mouseX, mouseY);
}
```