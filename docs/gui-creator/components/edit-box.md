# Edit Box (Input Field)

The **Edit Box** component creates an interactive text input field where players can type strings or numbers. It translates into Minecraft's native `net.minecraft.client.gui.components.EditBox` class.

---

## Inspector Properties

* **Hint / Placeholder Text**: The grayed-out background text displayed when the input field is empty (e.g. `Search...`).
* **Use Translation Key**: Wraps the placeholder text in `Component.translatable()`.
* **Force Numeric Values Only**: Restricts text input to numbers. If enabled, the Java generator registers a regular expression pattern filter (`-?\d+`) to reject alphabetic keystrokes.
* **Text Input Actions**:
  * **Trigger Event**:
    * `ON_CHANGE`: Actions fire immediately as the player types.
    * `ON_ENTER`: Actions only execute when the player presses **Enter**.
  * **Action Type**:
    * **Update Target Label**: Dynamically syncs the input text to a `StringWidget` Label (ideal for live previewing names).
    * **Print to Console**: Output the typed content directly to the Java stdout console.
    * **Target ID**: The identifier of the widget being target-updated.
* **Layout**: Standard dimensions and screen grid coordinates.

---

## Prevent Inventory Closure (Key Collision)

Usually in Minecraft, pressing the key bound to opening/closing the inventory (typically **E**) will immediately close any active screen container. When typing in an `EditBox`, players expect the "E" keystrokes to register as characters instead of closing the menu.

The Ponder GUI generator **automatically solves this collision** by overriding the `keyPressed` method in your screen class:
* Checks if any `EditBox` has keyboard focus.
* If focused, intercepts the key strokes, forwards them to the widget, and blocks the event from propagating back to the screen controller (preventing closure).
* Handles the Escape key correctly to close the GUI.

---

## NeoForge Java Generation

An Edit Box initializes the native widget, binds filters/responders, and handles character inputs:

### Global Fields
```java
private net.minecraft.client.gui.components.EditBox edit_box_1720000000;
```

### Canvas Initialization (Numeric + Live Changer)
```java
// EditBox: edit_box_1720000000
this.edit_box_1720000000 = new net.minecraft.client.gui.components.EditBox(this.font, this.leftPos + 40, this.topPos + 35, 120, 20, Component.literal("Enter amount..."));
this.edit_box_1720000000.setHint(Component.literal("Enter amount..."));
// Restricts input to integers only
this.edit_box_1720000000.setFilter(s -> s.isEmpty() || s.matches("-?\\d+"));
// Set responder for real-time live update label
this.edit_box_1720000000.setResponder(text -> { 
    if (this.label_target != null) this.label_target.setMessage(Component.literal(text)); 
});
this.addRenderableWidget(this.edit_box_1720000000);
```

### Keyboard Interception Method
Generates within the Screen class to handle typing and the Enter key action:
```java
@Override
public boolean keyPressed(int keyCode, int scanCode, int modifiers) {
    boolean isEditBoxFocused = this.edit_box_1720000000 != null && this.edit_box_1720000000.isFocused();
    if (isEditBoxFocused) {
        if (keyCode == 257 || keyCode == 335) { // Enter or Numpad Enter keys
            // Enter Action: prints value
            if (this.edit_box_1720000000 != null && this.edit_box_1720000000.isFocused()) { 
                System.out.println("edit_box_1720000000 value: " + this.edit_box_1720000000.getValue()); 
                return true; 
            }
        }
        if (this.getFocused() != null && this.getFocused().keyPressed(keyCode, scanCode, modifiers)) {
            return true;
        }
        if (keyCode == 256) { // Escape key
            this.onClose();
            return true;
        }
        return true; // Blocks Minecraft inventory 'E' key from closing the GUI
    }
    return super.keyPressed(keyCode, scanCode, modifiers);
}
```