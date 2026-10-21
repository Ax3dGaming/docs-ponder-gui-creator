# Group (Layout Folder)

The **Group** component is a structural and layout organization tool. It allows you to group multiple widgets together in the **Hierarchy Tree** to move them together and apply grouped offset positions.

In Java, groups are flattened by default. The coordinates of child widgets are automatically calculated by adding the group's offset, resulting in clean, direct absolute positioning code on the Screen. If a group is placed inside a `Scroll Panel`, it is instantiated as a dummy `AbstractWidget` layout wrapper.

---

## Inspector Properties

* **Offset X / Y**: Coordinate offsets. Moving a group automatically shifts all nested child widgets by the same delta on the canvas.
* **Size (Width & Height)**: Defines the visual bounding box of the group layout inside the editor canvas.
* **Render Conditions**: You can set render conditions (e.g., OP Permission Level 2+) on a group. *Note*: In the current exporter version, conditions should be set on individual children, but grouping coordinates helps lay them out together.

---

## Coordinate Flattening & Java Generation

To avoid nested widget overhead in Minecraft, the exporter flattens the coordinate system at generation time by accumulating parent offsets:

### 1. Root Canvas Flattening
If you have a **Group** named `my_group` at `X: 20, Y: 30`, and a **Button** named `btn_ok` nested inside at `X: 5, Y: 10`:
The generated Java code flattens `btn_ok` coordinates to `X: 25, Y: 40` (`leftPos + 25` and `topPos + 40`):

```java
// Button is placed at calculated absolute screen position:
this.btn_ok = net.minecraft.client.gui.components.Button.builder(Component.literal("OK"), button -> {
    // Action
}).bounds(this.leftPos + 25, this.topPos + 40, 50, 20).build();
this.addRenderableWidget(this.btn_ok);
```

### 2. Inner Scroll Panel Wrappers
If the group is nested inside a `Scroll Panel`, the group is instantiated as an empty anonymous `AbstractWidget` to act as a relative offset anchor:

```java
// Group inside Scroll Panel
this.my_group = new net.minecraft.client.gui.components.AbstractWidget(10, 10, 80, 50, Component.empty()) {
    @Override
    public void renderWidget(net.minecraft.client.gui.GuiGraphics guiGraphics, int mouseX, int mouseY, float partialTick) {}
    @Override protected void updateWidgetNarration(net.minecraft.client.gui.narration.NarrationElementOutput output) {}
};
this.scroll_panel_1.addWidget(this.my_group);
```
