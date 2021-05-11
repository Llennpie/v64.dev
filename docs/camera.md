# Freezing the Camera

##### Freeze and Soft-Freeze

To freeze the camera, a `0x80` byte must be inserted at `0x8033C848`. To unfreeze, insert `0x00`.

Here is an code example which uses *mupen64plus*'s debugger memory functions.
```cpp
void SetFreezeCamera(bool toggle) {
    int value = (toggle) ? 0x80 : 0x00;
    (*DebugMemWrite8)(0x8033C848, (unsigned char)(value));
}
```

There is a bug that may prevent Mario from exiting C-UP mode once it has been entered while frozen. When Mario exits C-UP, the camera byte changes to `0x22`. However, if Mario exits C-UP while the camera is frozen, it may change the byte to `0xA2` (`0x80` + `0x22`). This will prevent Mario from exiting C-UP mode, and he will be stuck in place until the camera is unfrozen.

<img src="img/freezing/frozen.png" width=320px class="round"/>

To soft-freeze the camera, a `0x01` byte must be inserted at `0x8033B205`. To unfreeze, insert `0x33`.

Here is another code example in *mupen64plus*.
```cpp
void SetSoftFreezeCamera(bool toggle) {
    int value = (toggle) ? 0x01 : 0x33;
    (*DebugMemWrite8)(0x8033B205, (unsigned char)(value));
}
```

I have created a GS code capable of freezing/unfreezing the camera.

```
D133AFA0 0800
8033C848 0080
D133AFA0 0400
8033C848 0000
D133AFA2 0200
8033B205 0001
D133AFA2 0100
8033B205 0033
```

The *DPAD-UP*/*DPAD-DOWN* buttons control the freeze camera, while the *DPAD-LEFT*/*DPAD-RIGHT* buttons control the soft-freeze camera.

##### Freezing Outside

When *frozen*, the game believes the camera is paused. Because of this, the camera will zoom out afar in some levels, the same way the pause screen shows an overhead view of the map.

This can be fixed, however. From addresses `0x8032F870` to `0x8032F883` is the area mask that determines which levels cause the camera to zoom out when the game is paused. If all 20 bytes are replaced with zeroes, the camera will no longer zoom out and will remain in place when frozen.

Here is an example in *M64MM*:
```csharp
// MainForm.cs
WriteBytes(BaseAddress + 0x32F870, new byte[20]);
```

Here is a GS code that uses this method:

```
8032F872 0000
8032F874 0000
8032F875 0000
8032F876 0000
8032F877 0000
8032F878 0000
8032F879 0000
8032F87A 0000
8032F87B 0000
8032F87C 0000
8032F87E 0000
8032F87F 0000
8032F880 0000
8032F881 0000
8032F882 0000
```

This code should allow you to freeze the camera using the methods above. Note that this code doesn't include all 20 addresses, as some were already set to 0.