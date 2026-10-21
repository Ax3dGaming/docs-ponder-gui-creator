# Inventory Slots (Input & Output)

In Minecraft, container-based GUIs use slots to store, input, or yield items. Ponder GUI Creator supports adding **Input Slots** and **Output Slots** onto the canvas. 

Because slots span both logical inventory data and visual overlays, they generate code across both the `Screen` and `Menu` Java classes.

---

## Input Slot vs. Output Slot

* **Input Slot**: A standard inventory slot where players can place, extract, or swap items.
* **Output Slot**: A results-only inventory slot. Players can extract items from it, but cannot place anything inside. In Java, this is implemented by overriding `mayPlace()` to return `false` on the slot instance.

---

## Inspector Properties

* **Slot Index**: The integer index of the inventory buffer slot (corresponds to the backing container buffer slot index).
* **Ghost Icon (Optional)**: A texture resource location path (e.g. `minecraft:textures/item/empty_armor_slot_shield.png`) displayed inside the slot when it is empty. The generator draws this icon translucently when `hasItem()` is false.
* **Layout**: Positions (X, Y) relative to the container. Slots have a fixed vanilla standard dimension of `18x18` pixels (the item renders at `16x16` pixels centered inside).

---

## NeoForge Java Generation

Slot coordinates are registered inside the constructor of the **Menu** class. In the **Screen** class, slots are automatically managed and rendered by the client, though the ghost icon is rendered inside `renderBg`:

### Menu Constructor Generation
For each slot in your GUI, the `Menu` class adds standard slot bindings:

```java
// --- GENERATED SLOTS REGISTERING ---
// Input Slot: input_slot_1
this.addSlot(new Slot(container, 0, 44, 36));

// Output Slot: output_slot_1
this.addSlot(new Slot(container, 1, 98, 36) {
    @Override 
    public boolean mayPlace(ItemStack stack) { 
        return false; // Blocks inserting items
    }
});
```

### Screen class Ghost Icon rendering
If a slot is configured with a **Ghost Icon**, the Screen's `renderBg` method will check if the slot is empty and draw the texture sheet sprite:

```java
// Under renderBg():
if (!this.menu.slots.get(0).hasItem()) {
    net.minecraft.client.renderer.RenderSystem.enableBlend();
    guiGraphics.blit(net.minecraft.resources.ResourceLocation.parse("minecraft:textures/item/empty_armor_slot_shield.png"), this.leftPos + 44, this.topPos + 36, 0, 0, 16, 16, 16, 16);
    net.minecraft.client.renderer.RenderSystem.disableBlend();
}
```