# fluffytw

<p align="center">
  <img src="./images/logo.png" alt="Fluffytw Logo">
</p>

<p align="center">fluffytw is a base for Teeworlds hacking. </p>
<div align="center">
  <!-- Primary Badges -->
  <a href="https://github.com/fluffysnaff/fluffytw"><img src="https://img.shields.io/badge/For-Teeworlds-98d243?style=for-the-badge" alt="For Teeworlds"></a>
  <a href="https://github.com/fluffysnaff/fluffytw"><img src="https://img.shields.io/badge/Type-Hacking%20Base-blueviolet?style=for-the-badge" alt="Type: Hacking Base"></a>
  <a href="https://github.com/fluffysnaff/fluffytw/commits/master"><img src="https://img.shields.io/github/last-commit/fluffysnaff/fluffytw?style=for-the-badge" alt="Last Commit"></a>
  <!-- Secondary / Tech Badges -->
  <br/>
  <img src="https://img.shields.io/badge/C++-00599C?style=flat-square&logo=c%2B%2B&logoColor=white" alt="C++">
  <img src="https://img.shields.io/badge/Compatible%20with-DDNet-lightgrey?style=flat-square" alt="Compatible with DDNet">
  <!-- Special Call-to-Action Badge -->
  <a href="https://krxteam.com/krx-client#pricing"><img src="https://img.shields.io/badge/Premium%20Version-KRX%20Client-gold?style=flat-square&logo=rocket" alt="KRX Client"></a>
</div>

---

## From Open-Source to Ultimate Power

Liking `fluffytw`? If you want to skip the setup and access the most powerful features, like advanced replay bots, ad-free experience, and priority support—all in a ready-to-go package, **KRX Client** is for you.

It's the ultimate, pre-compiled version of our work, built for players who want maximum performance with zero hassle.

➡️ **[Check out KRX Client's Premium Features](https://krxteam.com/krx-client#pricing)**

---

  ## Installation to 19.9
  
  Before you start, make sure to clone the DDNet repository.
  
  ```bash
  # do this inside DDNet main directory
  cd src/game/client
  git submodule add https://github.com/fluffysnaff/fluffytw.git
  ```
  
  Add this to `CMakeLists.txt`:
  ```cmake
  add_subdirectory(src/game/client/fluffytw)
  ```
  
  It should be placed before:
  ```cmake
  set(CLIENT_SRC ${ENGINE_CLIENT} ${PLATFORM_CLIENT} ${GAME_CLIENT} ${GAME_EDITOR} ${GAME_MAP} ${GAME_GENERATED_CLIENT})
  ```
  
  ## Config
  
  Add this to `src/engine/shared/config_variables.h`:
  ```cpp
  MACRO_CONFIG_INT(FluffyAimbot, cl_fluffy_aimbot, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: enable aimbot")
  MACRO_CONFIG_INT(FluffyAimbotFov, cl_fluffy_aimbot_fov, 360, 0, 360, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: aimbot field of view")
  MACRO_CONFIG_INT(FluffyAimbotSilent, cl_fluffy_aimbot_silent, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: silent aimbot (only hooks, no mouse move)")
  MACRO_CONFIG_INT(FluffyAimbotHookVisible, cl_fluffy_aimbot_hookvisible, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: only hook when target is visible")
  MACRO_CONFIG_INT(FluffyAimbotEdge, cl_fluffy_aimbot_edge, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: enable edge/hitpoint scan")
  MACRO_CONFIG_INT(FluffyAimbotAccuracy, cl_fluffy_aimbot_accuracy, 1, 1, 100, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: edge scan accuracy (higher = more hitpoints)")
  MACRO_CONFIG_INT(FluffyEsp, cl_fluffy_esp, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: enable esp/visuals")
  MACRO_CONFIG_INT(FluffyEspFov, cl_fluffy_esp_fov, 0, 0, 1, CFGFLAG_SAVE | CFGFLAG_CLIENT, "fluffytw: draw aimbot fov")
  ```
    
  Inside `gameclient.cpp` add this:
  ```cpp
  #include <memory>
  #include "fluffytw/f_helper.h"
  std::unique_ptr<FHelper> fHelper;
  ```
  
  Now add this code to `void CGameClient::OnConsoleInit()`:
  ```cpp
  void CGameClient::OnConsoleInit()
  {
  // ...
  fHelper = std::make_unique<FHelper>(this);
  // ...
  }
  ```
    
  1. **Visuals**  
     Inside `players.cpp` include:
     ```cpp
     #include "game/client/fluffytw/f_helper.h"
     ```
  
     Then in `RenderPlayer()` add:
     ```cpp
     // ...
     fHelper->m_pConfig->espCfg.enabled = g_Config.m_FluffyEsp != 0;
     fHelper->m_pConfig->espCfg.drawFov = g_Config.m_FluffyEspFov != 0;
     fHelper->m_pVisuals->Run(ClientId, Angle, Position);
     ```
  
  2. **Helper**  
     Add this in `CCollision` class:
     ```cpp
     // inside collision.h
     public:
        // ...
        CTile *GetTiles() { return m_pTiles; }
     ```
  
  3. **Bots**  
     Inside `controls.cpp` include:
     ```cpp
     #include "game/client/fluffytw/f_helper.h"
     ```
     Then in `SnapInput()` add:
     ```cpp
     // ...
     fHelper->m_pConfig->aimbotCfg.enabled = g_Config.m_FluffyAimbot != 0;
     fHelper->m_pConfig->aimbotCfg.fov = static_cast<float>(g_Config.m_FluffyAimbotFov);
     fHelper->m_pConfig->aimbotCfg.silent = g_Config.m_FluffyAimbotSilent != 0;
     fHelper->m_pConfig->aimbotCfg.hookVisible = g_Config.m_FluffyAimbotHookVisible != 0;
     fHelper->m_pConfig->aimbotCfg.edge = g_Config.m_FluffyAimbotEdge != 0;
     fHelper->m_pConfig->aimbotCfg.accuracy = static_cast<float>(g_Config.m_FluffyAimbotAccuracy);
     fHelper->m_pBots->Run();
     ```
     It should be placed after:
     ```cpp
     if(!m_aInputDirectionLeft[g_Config.m_ClDummy] && m_aInputDirectionRight[g_Config.m_ClDummy])
     	m_aInputData[g_Config.m_ClDummy].m_Direction = 1;
     ```

## Enabling
```
cl_fluffy_aimbot 1
cl_fluffy_aimbot_hookvisible 1
cl_fluffy_aimbot_edge 1
```
The aimbot is **hook-assisted**. For it to aim on its own, enable
**Aimbot + Auto Hook + Edge Scan**. With only **Aimbot** on, it just aims while
you hold hook and there's a clear line to the target.

## Config variables
| Variable | Default | Description |
| --- | --- | --- |
| `cl_fluffy_aimbot` | 0 | Master enable |
| `cl_fluffy_aimbot_fov` | 360 | Aim FOV (0–360) |
| `cl_fluffy_aimbot_silent` | 0 | Silent (hook only, no mouse move) |
| `cl_fluffy_aimbot_hookvisible` | 0 | Auto-press hook when target visible |
| `cl_fluffy_aimbot_edge` | 0 | Edge/hitpoint scan |
| `cl_fluffy_aimbot_accuracy` | 1 | Edge scan accuracy (1–100) |
| `cl_fluffy_esp` | 0 | Enable ESP |
| `cl_fluffy_esp_fov` | 0 | Draw aimbot FOV |


## Possible problems
1. If something isn't working, make sure that configs are setup right and everything is executing.  
2. Aimbot isn't working - Debug it. For example set `m_CanAim` always to true and see if it works.  

## Showcase
1. [Hitpoint scan](https://streamable.com/s81xls)   
2. [Hook prediction](https://streamable.com/j51ilg)  & [Without hook prediction](https://streamable.com/4zegsy)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=fluffysnaff/fluffytw&type=Date)](https://star-history.com/#fluffysnaff/fluffytw&Date)
