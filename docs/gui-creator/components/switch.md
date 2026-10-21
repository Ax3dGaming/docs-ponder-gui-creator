# Switch (Toggle Button)

The **Switch** component represents a stateful binary toggle widget (similar to a checkbox, ON/OFF toggle, or slider switch). 

In Java, it exports as an anonymous subclass of `AbstractWidget` managing a public toggle state boolean (`isToggled_<id>`). When clicked, it flips its boolean state, plays a click audio cue, and updates its visual representation.

---

## Inspector Properties

* **Default State**: Choose whether the switch initializes in the ON or OFF position.
* **ON Texture**:
  * **Use Custom Textures**: Specify a custom skin path or URL for the ON state. If empty, renders a default solid green box with a border.
* **OFF Texture**:
  * **Use Custom Textures**: Specify a custom skin path or URL for the OFF state. If empty, renders a default solid gray box with a border.
* **On Click Actions**: Actions (like packets or commands) that fire every time the player clicks and flips the switch toggle state.
* **Layout**: Dimension bounds and layout positions.

---

## NeoForge Java Generation

A Switch declares a public boolean flag for tracking state and constructs the widget inside `init()`:

### Global Fields
```java
public boolean isToggled_switch_1 = false; // Tracks state (toggled ON/OFF)
private net.minecraft.client.gui.components.AbstractWidget switch_1;
```

### Canvas Initialization (Default Color Fill Example)
```java
// Switch Widget: switch_1
this.switch_1 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 40, this.topPos + 80, 30, 15, Component.empty()) {
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        if (isToggled_switch_1) {
            // Draw solid green box for ON state
            guiGraphics.fill(this.getX(), this.getY(), this.getX() + this.width, this.getY() + this.height, 0xFF22C55E);
            guiGraphics.renderOutline(this.getX(), this.getY(), this.width, this.height, 0xFF166534);
        } else {
            // Draw solid gray box for OFF state
            guiGraphics.fill(this.getX(), this.getY(), this.getX() + this.width, this.getY() + this.height, 0xFF3F3F46);
            guiGraphics.renderOutline(this.getX(), this.getY(), this.width, this.height, 0xFF52525B);
        }
    }

    @Override
    public boolean mouseClicked(double mouseX, double mouseY, int button) {
        if (this.active && this.visible && this.clicked(mouseX, mouseY)) {
            isToggled_switch_1 = !isToggled_switch_1; // Invert flag
            // Play vanilla click sound
            net.minecraft.client.Minecraft.getInstance().getSoundManager().play(net.minecraft.client.resources.sounds.SimpleSoundInstance.forUI(net.minecraft.sounds.SoundEvents.UI_BUTTON_CLICK, 1.0F));
            
            // Trigger associated actions (e.g. print state)
            System.out.println("switch_1 changed to: " + isToggled_switch_1);
            return true;
        }
        return false;
    }

    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.addRenderableWidget(this.switch_1);
```
