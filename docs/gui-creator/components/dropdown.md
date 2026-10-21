# Dropdown (Select Menu)

The **Dropdown** component (or Select Menu) allows players to click and expand a list of multiple options to select one. It translates into an anonymous subclass of Minecraft's `AbstractWidget` that handles folding, option coordinate mapping, hover highlights, and custom texture assets.

---

## Inspector Properties

* **Options List**: Define the list of selection options (e.g. `Easy`, `Normal`, `Hard`).
* **Selected Index**: The default selected option index (0-indexed).
* **Main Button Texture**:
  * **Use Custom Textures**: Specify a custom skin file path or URL for the collapsed select bar. If empty, renders a default gray vanilla button outline.
* **Expanded List Texture**:
  * **Use Custom Textures**: Specify a custom background skin path or URL for the expanded panel list. If empty, renders a dark zinc pane background.
* **Layout**: Dimension bounds and layout positions. The expanded dropdown translates coordinate translation offsets up to prevent clipping behind other buttons.

---

## NeoForge Java Generation

The generator compiles the dropdown selection state inside an anonymous `AbstractWidget`:

### Global Fields
```java
private net.minecraft.client.gui.components.AbstractWidget dropdown_1720000000;
```

### Canvas Initialization
```java
// Dropdown Menu: dropdown_1720000000
this.dropdown_1720000000 = new net.minecraft.client.gui.components.AbstractWidget(this.leftPos + 40, this.topPos + 35, 120, 20, Component.empty()) {
    private boolean isOpen = false;
    private int selectedIndex = 0;
    private final String[] options = new String[]{ "Easy", "Normal", "Hard" };

    public String getSelectedOption() {
        if (selectedIndex >= 0 && selectedIndex < options.length) return options[selectedIndex];
        return "";
    }

    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {
        // Draw the main dropdown button backdrop
        guiGraphics.fill(this.getX(), this.getY(), this.getX() + this.width, this.getY() + this.height, 0xFF3F3F46);
        guiGraphics.renderOutline(this.getX(), this.getY(), this.width, this.height, 0xFF52525B);
        
        String display = options.length > 0 ? options[Math.min(selectedIndex, options.length - 1)] : "";
        guiGraphics.drawString(net.minecraft.client.Minecraft.getInstance().font, display, this.getX() + 4, this.getY() + (this.height - 8) / 2, 0xFFFFFF, false);
        guiGraphics.drawString(net.minecraft.client.Minecraft.getInstance().font, isOpen ? "▲" : "▼", this.getX() + this.width - 10, this.getY() + (this.height - 8) / 2, 0xAAAAAA, false);

        // Draw expanded options panel overlay
        if (isOpen) {
            guiGraphics.pose().pushPose();
            guiGraphics.pose().translate(0, 0, 400); // Bring list elements to the front (Z-Index override)
            int listY = this.getY() + this.height;
            guiGraphics.fill(this.getX(), listY, this.getX() + this.width, listY + (options.length * 14), 0xFF27272A);
            guiGraphics.renderOutline(this.getX(), listY, this.width, options.length * 14, 0xFF52525B);
            
            for (int i = 0; i < options.length; i++) {
                int itemY = listY + (i * 14);
                boolean hovered = mouseX >= this.getX() && mouseX <= this.getX() + this.width && mouseY >= itemY && mouseY <= itemY + 14;
                if (hovered) {
                    guiGraphics.fill(this.getX() + 1, itemY + 1, this.getX() + this.width - 1, itemY + 13, 0xFF3F3F46);
                }
                guiGraphics.drawString(net.minecraft.client.Minecraft.getInstance().font, options[i], this.getX() + 4, itemY + 3, hovered ? 0xFFFFFF : 0xAAAAAA, false);
            }
            guiGraphics.pose().popPose();
        }
    }

    @Override
    public boolean mouseClicked(double mouseX, double mouseY, int button) {
        if (this.active && this.visible) {
            if (isOpen) {
                int listY = this.getY() + this.height;
                if (mouseX >= this.getX() && mouseX <= this.getX() + this.width && mouseY >= listY && mouseY <= listY + (options.length * 14)) {
                    int clickedIndex = (int) (mouseY - listY) / 14;
                    if (clickedIndex >= 0 && clickedIndex < options.length) {
                        this.selectedIndex = clickedIndex;
                        net.minecraft.client.Minecraft.getInstance().getSoundManager().play(net.minecraft.client.resources.sounds.SimpleSoundInstance.forUI(net.minecraft.sounds.SoundEvents.UI_BUTTON_CLICK, 1.0F));
                    }
                    this.isOpen = false;
                    return true;
                }
            }
            
            if (this.clicked(mouseX, mouseY)) {
                this.isOpen = !this.isOpen;
                net.minecraft.client.Minecraft.getInstance().getSoundManager().play(net.minecraft.client.resources.sounds.SimpleSoundInstance.forUI(net.minecraft.sounds.SoundEvents.UI_BUTTON_CLICK, 1.0F));
                return true;
            } else {
                this.isOpen = false; // Collapse list on click outside
            }
        }
        return false;
    }

    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.addRenderableWidget(this.dropdown_1720000000);
```
