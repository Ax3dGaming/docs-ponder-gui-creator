# Player Inventory

The **Player Inventory** component is a helper widget that automatically maps and registers the player's personal inventory slots (the 27 main inventory slots and 9 hotbar slots) in the generated Screen/Menu. 

---

## How It Works

Adding the **Player Inventory** component to your canvas tells the Java generator that the GUI interacts with the player's personal items. Rather than requiring you to place 36 separate slots manually, this single component automatically generates the slot loops inside the **Menu** constructor.

---

## Inspector Properties

* **Layout**: Positions (X, Y) relative to the screen. 
  * *Note*: The component has a fixed vanilla standard layout size of `162x76` pixels, mirroring the typical arrangement of a player's inventory box in standard Minecraft containers.

---

## NeoForge Java Generation

When this component is present, the **Menu** constructor automatically appends slot declarations:

### Menu Constructor Slot Binding
```java
// Player Inventory layout slots (9x3)
for (int si = 0; si < 3; ++si) {
    for (int sj = 0; sj < 9; ++sj) {
        this.addSlot(new Slot(playerInventory, sj + si * 9 + 9, 8 + sj * 18, 84 + si * 18));
    }
}
// Player Hotbar layout slots (9x1)
for (int si = 0; si < 9; ++si) {
    this.addSlot(new Slot(playerInventory, si, 8 + si * 18, 142));
}
```
If you change the X/Y coordinates in the editor, the slot indices adjust offsets accordingly to match the visual representation on screen.