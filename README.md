# Eternal Grace Arena


🎬 **Gameplay footage:** https://youtu.be/dwP9lMNThy0


> A third-person **soulslike action-combat game** built in **Unreal Engine 5 (C++)** — my **first-ever Unreal project**, developed solo.

*Eternal Grace Arena* is a complete, self-contained boss-rush soulslike: you fight a series of bosses in 1v1 arena battles from an atmospheric hub area. I built it solo over ~4 months as the final project of my games-programming education at the SAE Institute.

I share it as an **engineering portfolio piece**: the focus below is on *how the systems are built*. Because this is an early project, I keep the assessment **honest** — what's strong, and what already shows its age.

> **Status — complete but an early-career project.** Unlike my later work this is not architecturally polished; its value is **breadth and end-to-end ownership** — a whole game, across every gameplay system, built alone as a first Unreal project.

<div align="center">
<img width="2915" height="1319" alt="EG2" src="https://github.com/user-attachments/assets/0ff76999-29b7-4a12-bf67-fa1825e277a7" width = "600"/>
 </div>

 
---

## Where this fits in my development

This is the project where I moved from Unity to **Unreal Engine and C++** for the first time. It sits between my self-taught beginnings and the deliberately clean rebuild that followed:

| Project | When | Engine | What it shows |
|---|---|---|---|
| **Bonfire** | earliest (~1.5 yrs in) | Unity / C# | First 3D & codebase: hand-built FSM, interfaces, ScriptableObject events |
| **➡ Eternal Grace Arena (this repo)** | university | UE5 / C++ | My **first Unreal project** — a complete solo soulslike across all systems |
| **EternalGrace Prototype** | after graduating | UE5 / C++ | The deliberate clean rebuild: data payloads, SOLID, save subsystem, Behavior-Tree AI |
| **Project Mirror / Glass Purgatory** | current indie project | UE5 / C++ | Decoupled Pub/Sub event subsystem, CommonUI + MVVM, framework-for-reuse |

Two threads run directly through this project:

- **AI:** hand-built FSM (*Bonfire*) → **manual, perception-based sensing & aggro here** (`UPawnSensingComponent`) → Behavior Trees (*EternalGrace Prototype*) → State Trees (later, professional work).
- **Architecture:** I already reached for the right primitives early — components, interfaces and delegates — but gameplay logic still largely lived **inside the character**, and damage was passed as **loose parameters**. The very next project (*EternalGrace Prototype*) is my conscious refactor of exactly those weaknesses (data payloads, interface segregation, dedicated subsystems).

Everything in [`Eternal_Grace_Arena/Source/`](Eternal_Grace_Arena/Source/) is my own work.

---

## Tech stack

| Area | Details |
|---|---|
| Engine | **Unreal Engine 5.5** |
| Language | **C++** with `UActorComponent` composition and C++ interfaces (`BlueprintNativeEvent`) |
| Animation | Gameplay driven by **custom Animation Notify States** (frame-accurate hooks) |
| AI | `UPawnSensingComponent`-based perception, aggro/awareness logic, multiple boss patterns |
| VFX/Audio | **Niagara** spell effects, post-process status feedback, spatial sound |
| UI | UMG widgets (health bars, progress/animated/blending widgets, title screen) |

---
<div align="center">
<img width="3042" height="1259" alt="EG3" src="https://github.com/user-attachments/assets/ce227888-227d-4335-9dcd-8e90603dab34" width = "600" />
 </div>

## Engineering highlights

### Souls-style action combat
A combat system with real depth: light / heavy / **charged** attacks, sprint attacks, a **dodge roll**, **guard/block**, and a **guard-counter / parry** gated by a reaction window (`GuardCounterReactionTimer`). → Combat feel and timing mechanics.

### Resource & hit systems
**Stamina** consumption plus a **poise / stagger** model (`PoiseDamage`, `EStaggeringType`, directional damage, blocked vs. unblocked) split across dedicated components — `HealthComponent`, `StaminaComponent`, `StaggerComponent`, `ShieldComponent`. → Combat-balancing architecture.

### Animation-driven gameplay via custom Notify States
Gameplay logic is bound **frame-accurately to animation** through a family of custom notify states: `NS_WeaponSwing`, `NS_WeaponSwing_CollisionToggle`, `NS_ChargedWeaponSwing`, `NS_CastSpell`, `NS_DrainStamina`, `NS_ContinousRecourceConsumption`, `NS_RotateTowardsTarget`, `NS_PlayFootstepSound`, `NS_CancelMontage`. For example, weapon collision is only active during the swing, and pawn collision is disabled mid-attack. → A core technique for action games.

### Lock-on / targeting with a dynamic camera
`ULockOnSystem` drives smooth camera and character rotation via `RInterpTo`, applies **distance-based Z-angle adjustment** with clamping, and projects a target reticle into **screen space**. → Camera mathematics and 3D vector work.

### Enemy AI & multiple bosses
Perception via `UPawnSensingComponent`, aggro states and distance checks, plus **back-attack detection** (`CheckIfPlayerIsBehind`). Several distinct boss types — Mage Boss, Mushroom King, Weapon Master and a Caster — each with their own behaviour. → AI behaviour and boss design.

### Spell / projectile framework
`ASpell` and its variants (`Spells_Projectile`, `Spells_HomingProjectile`, `Spells_EffectArea`, `Spell_Teleport`) provide sphere hitboxes, **Niagara VFX**, class-filtered hit targets and spawn/loop sounds. → A reusable ability/projectile system.

### Status effects with visual feedback
`UStatusEffectComponent` applies effects such as poison/exhaustion with a **post-process blend** as player feedback. → Game feel through visual communication.

### World state & cross-level persistence
`UEternalGrace_GameInstance` holds persistent **object states** (`ObjectStates` — e.g. "boss defeated" changes the world), **win-condition dependencies**, health carry-over across **map travel**, loading screens, and **achievement tracking** (e.g. "took no damage"). → State, progression and persistence management.

### Component & interface architecture
Custom components (Health, Stamina, Stagger, Shield, Weapon, StatusEffect, LockOn) and C++ interfaces (`I_Damageable`, `I_Interactable`, `I_Targetable`) with `BlueprintNativeEvent`, **multicast delegates** (`OnCharacterDeath`, `OnWorldUpdate`) and a clean inheritance hierarchy (Character → Player / Enemy → concrete bosses). → OOP/engine architecture laid out correctly even at this early stage.

### UI & a first dialogue system
Custom UMG UI (player & enemy health bars, progress/animated/blending widgets, title screen) and an early **dialogue system** (`Dialogue`, `DialogueComponent`, `DialogueWidget`).

---

## Honest maturity note

I keep this transparent, because the **learning curve is part of the story**:

- The code shows an **earlier stage**: commented-out dead code in headers, inconsistent naming, the occasional typo in category names, and thin components whose logic still lives in the character.
- What stands out positively is that the **architecture already reaches for the right tools** (components, interfaces, delegates) — and that the code contains **self-critical TODOs**, e.g. one precisely naming a violated *separation of concerns* in the lock-on system.

That ability to question my own code early is exactly what made the jump to the much cleaner [EternalGrace Prototype](../Shadow-Keep/EternalGrace_Proto) (data payloads, interface segregation, a dedicated save subsystem, Behavior-Tree AI) a deliberate one.

---

<div align="center">
<img width="2809" height="1300" alt="EG4" src="https://github.com/user-attachments/assets/d2215901-b4bf-4891-ac79-ca79592a9b55" width = "600" />
</div>


## License

© 2026 Leonard Kemenani. All rights reserved.
Source is published for **portfolio and review purposes**. Not licensed for redistribution or reuse without permission.
