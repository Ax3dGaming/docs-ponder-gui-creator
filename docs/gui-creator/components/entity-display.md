# Entity Display

The **Entity Display** component spawns and renders a 3D animated living entity (e.g., player, zombie, pig, custom mod mob) directly inside your GUI viewport. 

It instantiates a local client-side entity model at initialization and renders it in `renderBg` using Minecraft's `InventoryScreen.renderEntityInInventory` method.

---

## Inspector Properties

* **Entity Registry ID**: The entity namespace identifier (e.g. `minecraft:zombie`, `minecraft:creeper`, `minecraft:sheep`).
* **Scale**: The render size in pixels (defaults to `30`).
* **Rotations**: Customize the angles:
  * **Rotation X / Y / Z**: Pitch, Yaw, and Roll angles in degrees.
* **Follow Cursor**:
  * **Look at Mouse**: If enabled, the entity model automatically turns its head and body to look at the player's mouse pointer inside the GUI.
* **Animations**:
  * **Animate Rotation**: If enabled, the entity constantly spins around on the Y-axis.
* **Layout**: Dimensions and positioning. The bottom center of the boundary acts as the entity's standing base pivot.

---

## NeoForge Java Generation

Spawner hooks run in the screen `init()` method while rendering logic goes into `renderBg()`:

### Global Fields
```java
private net.minecraft.world.entity.LivingEntity entity_display_1;
private float scale_entity_display_1 = 30f;
private float rotX_entity_display_1 = 0f;
private float rotY_entity_display_1 = 0f;
private float rotZ_entity_display_1 = 0f;
```

### Canvas Initialization (Spawning Entity)
```java
// Under init()
net.minecraft.world.entity.EntityType<?> type_display = net.minecraft.core.registries.BuiltInRegistries.ENTITY_TYPE.get(net.minecraft.resources.ResourceLocation.parse("minecraft:zombie"));
if (type_display != null && this.minecraft != null && this.minecraft.level != null) {
    this.entity_display_1 = (net.minecraft.world.entity.LivingEntity) type_display.create(this.minecraft.level);
}
```

### Rendering Method (Look At Mouse Example)
```java
// Under renderBg()
if (this.entity_display_1 != null) {
    // Standing base coordinates
    int posX = this.leftPos + 40 + 25; // X center
    int posY = this.topPos + 20 + 50; // Y base
    int scale = (int) this.scale_entity_display_1;

    // Head-turning logic to follow cursor
    float mouseDeltaX = (float) (posX - mouseX);
    float mouseDeltaY = (float) (posY - scale - mouseY);
    this.entity_display_1.setYRot(180.0F + mouseDeltaX * 0.04F);
    this.entity_display_1.setXRot(mouseDeltaY * 0.1F);
    this.entity_display_1.yBodyRot = 180.0F + mouseDeltaX * 0.02F;
    this.entity_display_1.yHeadRot = this.entity_display_1.getYRot();
    this.entity_display_1.yHeadRotO = this.entity_display_1.getYRot();

    org.joml.Quaternionf quaternionf = (new org.joml.Quaternionf()).rotationZ((float)Math.PI);
    org.joml.Quaternionf quaternionf1 = (new org.joml.Quaternionf()).rotationX(this.entity_display_1.getXRot() * ((float)Math.PI / 180F));
    quaternionf.mul(quaternionf1);

    net.minecraft.client.gui.screens.inventory.InventoryScreen.renderEntityInInventory(
            guiGraphics,
            (float)posX,
            (float)posY,
            (float)scale,
            new org.joml.Vector3f(0.0F, 0.0F, 0.0F),
            quaternionf,
            quaternionf1,
            this.entity_display_1
    );
}
```