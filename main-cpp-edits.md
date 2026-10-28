# `src/main.cpp` edits (mobile browser instructions)

Use these in the public source repo `fraudian/Predictive-Input-Buffer-V2`. Do not replace the entire 950-line file; make the three edits below.

## Edit 1 — make the Windows console include Android-safe

Find this line near the top:

```cpp
#include <Windows.h>
```

Replace it with:

```cpp
#if defined(_WIN32)
#include <Windows.h>
#endif
```

## Edit 2 — compile the optional console only on Windows

Find `$on_mod(Loaded) {` and the following block. In the current source it looks like this:

```cpp
$on_mod(Loaded) {
    buf.console = Mod::get()->getSettingValue<bool>("debug-console");
    readSettings();
    applyStage();
    if (buf.console) {
        AllocConsole();
        FILE* out = nullptr;
        freopen_s(&out, "CONOUT$", "w", stdout);
        SetConsoleTitleA("Predictive Input Buffer - Live Console");
    }
}
```

Replace that whole block with:

```cpp
$on_mod(Loaded) {
    buf.console = Mod::get()->getSettingValue<bool>("debug-console");
    readSettings();
    applyStage();
#if defined(_WIN32)
    if (buf.console) {
        AllocConsole();
        FILE* out = nullptr;
        freopen_s(&out, "CONOUT$", "w", stdout);
        SetConsoleTitleA("Predictive Input Buffer - Live Console");
    }
#else
    // Android has no separate Windows console; keep the in-game panel available.
    buf.console = false;
#endif
}
```

## Edit 3 — add the touch buttons

Find the `class $modify(BufPlayLayer, PlayLayer)` section, then find this exact pair inside its `init(...)` method:

```cpp
        this->addChild(buf.panel);
        refreshPanel();
```

Immediately **after** those two lines, paste:

```cpp
        // Touch controls: BUF does what F6 does; LOG does what K does.
        // They sit near the upper-right edge, below the pause control.
        if (Mod::get()->getSettingValue<bool>("mobile-controls")) {
            auto* stageSprite = ButtonSprite::create("BUF");
            auto* stageButton = CCMenuItemSpriteExtra::create(
                stageSprite, this, menu_selector(BufPlayLayer::onCycleStageTouch)
            );
            auto* logSprite = ButtonSprite::create("LOG");
            auto* logButton = CCMenuItemSpriteExtra::create(
                logSprite, this, menu_selector(BufPlayLayer::onTogglePanelTouch)
            );
            auto* touchMenu = CCMenu::create();
            if (stageButton && logButton && touchMenu) {
                stageButton->setScale(0.65f);
                logButton->setScale(0.65f);
                stageButton->setPosition({0.f, 0.f});
                logButton->setPosition({0.f, -42.f});
                touchMenu->addChild(stageButton);
                touchMenu->addChild(logButton);
                touchMenu->setPosition({win.width - 48.f, win.height - 125.f});
                touchMenu->setZOrder(1001);
                this->addChild(touchMenu, 1001);
            }
        }
```

Then, still inside `class $modify(BufPlayLayer, PlayLayer)`, find `void onQuit() {`. Paste these two methods immediately **above** it:

```cpp
    void onCycleStageTouch(CCObject*) {
        cycleStage();
    }

    void onTogglePanelTouch(CCObject*) {
        buf.panelOpen = !buf.panelOpen;
        refreshPanel();
    }
```

Keep the existing `onQuit`, `resetLevel`, and `playEndAnimationToPos` methods. Do not paste the callbacks outside the `BufPlayLayer` class.

The original keyboard shortcuts remain available on desktop. The touch overlay uses normal Geode/Cocos menu buttons, so it will capture taps that land directly on the two small buttons; turn it off in settings if the position is inconvenient.
