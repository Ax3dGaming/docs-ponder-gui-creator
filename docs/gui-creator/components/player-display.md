# Player Display

The **Player Display** component is a specialized rendering widget that displays a 3D player skin model. It can render the active local client player or dynamically fetch and load external skins from Mojang servers using usernames or UUIDs (supporting live binding to text fields).

In Java, it uses Minecraft's native `InventoryScreen.renderEntityInInventoryFollowsMouse` rendering system.

---

## Inspector Properties

* **Target Player**: Specifies whose skin to render:
  * `%player_name%` (default): Renders the active local client player who opened the GUI.
  * Username / UUID String: Renders a static target player profile (e.g., `notch` or `d066f7a8-e1e6-47b2-9d33-97994fa525a4`).
  * Dynamic Bind: Bind to an input field (e.g., `${player_name_box}`). The model will reload the skin in real-time as the player types.
* **Is UUID Input**: Check this if the target string contains a UUID instead of a username.
* **Scale**: Size of the player model projection.
* **Layout**: Dimension bounds and layout positions. The bottom center of the boundary acts as the entity standing pivot.

---

## NeoForge Java Generation

Depending on the configuration, the generator builds either a direct local player rendering command or a remote skin fetcher:

### 1. Active Client Player (Default `%player_name%`)
If rendering the active player, the code calls the renderer directly inside `renderBg`:

```java
// Under renderBg():
net.minecraft.client.gui.screens.inventory.InventoryScreen.renderEntityInInventoryFollowsMouse(
    guiGraphics, 
    this.leftPos + 20, // X min
    this.topPos + 10,  // Y min
    this.leftPos + 60, // X max
    this.topPos + 80,  // Y max
    30,                // Scale
    0.0625F, 
    mouseX, 
    mouseY, 
    this.minecraft.player // Active client player entity
);
```

### 2. Dynamic Remote Player Skin Fetching
If you bind a player name to an input field (e.g., `${edit_box_1}`), the generator creates a custom `RemotePlayer` entity and fetches skin details asynchronously from the Mojang Session Server:

```java
// Global Fields inside Screen class
private net.minecraft.client.player.RemotePlayer dummyPlayer_player_disp;
private String lastTarget_player_disp = "";
private java.util.UUID targetUUID_player_disp = null;

private void createDummyPlayer_player_disp(com.mojang.authlib.GameProfile profile) {
    if (this.minecraft.level != null) {
        this.dummyPlayer_player_disp = new net.minecraft.client.player.RemotePlayer(this.minecraft.level, profile) {
            private net.minecraft.resources.ResourceLocation lastSkinTexture = null;
            @Override
            public net.minecraft.client.resources.PlayerSkin getSkin() {
                net.minecraft.client.resources.PlayerSkin skin = net.minecraft.client.Minecraft.getInstance().getSkinManager().getInsecureSkin(this.getGameProfile());
                if (skin != null && !skin.texture().equals(lastSkinTexture)) {
                    lastSkinTexture = skin.texture();
                }
                return skin;
            }
            @Override
            public boolean isModelPartShown(net.minecraft.world.entity.player.PlayerModelPart part) {
                return true; // Always enable outer skin overlays (jackets, sleeves, capes)
            }
        };
    }
}
```

Inside the Screen's `renderBg` method, the code checks if the target has changed, fetches skin profiles asynchronously from the sessionserver API, caches the results, and renders the skin:

```java
// Under renderBg():
String currentTarget_player_disp = String.valueOf(this.edit_box_1.getValue());

if (!currentTarget_player_disp.equals(this.lastTarget_player_disp)) {
    this.lastTarget_player_disp = currentTarget_player_disp;
    this.dummyPlayer_player_disp = null;

    // Checks cache or runs supplyAsync thread fetching sessionserver Mojang profiles...
    // Creates dummy profile and calls createDummyPlayer_player_disp().
}

if (this.dummyPlayer_player_disp != null) {
    // Renders the loaded RemotePlayer looking at cursor
    net.minecraft.client.gui.screens.inventory.InventoryScreen.renderEntityInInventoryFollowsMouse(guiGraphics, this.leftPos + 20, this.topPos + 10, this.leftPos + 60, this.topPos + 80, 30, 0.0625F, mouseX, mouseY, this.dummyPlayer_player_disp);
} else {
    // Fallback default Steve skin preview
    java.util.UUID fallbackUuid = java.util.UUID.randomUUID();
    guiGraphics.blit(net.minecraft.client.resources.DefaultPlayerSkin.get(fallbackUuid).texture(), this.leftPos + 20, this.topPos + 10, 32, 32, 40, 70, 256, 256);
}
```
