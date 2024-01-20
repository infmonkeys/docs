# infmonkeys
infmonkeys is a development project dedicated to improving gear development overall by developing a variety of tools.

The primary goal is to develop a framework that will reduce redundant code that is seen across various gears, as well as unifying various features that should be consistent across.

Under the hood, gears perform various algorithms and operations that may be redundant or repetitive.

# Modules
This project will involve various modules that implement features while reducing redundancy and increasing modularity.

## Humanoid module
This is a shared module between client and server.

The humanoid module provides various functionalities that allow developers to easily interact with humanoids. It primarily provides an abstraction of health manipulation (e.g. damage) as well as quick searching.

### Health interactions
Dealing with health can be tricky, especially when damage mitigation.

This module implements the following features for humanoids:
- DoDamage - deals damage to a humanoid after applying all attributes and status effects
- DoDamageRaw - deals damage to humanoid directly, ignoring all attributes
- CalcDamage - calculates the amount of damage to be dealt after applying all attributes
- Heal - heals the humanoid after applying all attributes and status effects
- HealRaw - heals the humanoid directly
- CalcHeal - calculates the amount of health to heal after considering all attributes and status effects

These functions interact with the [status effect module](#status-effect-module).

For future maintenance and updates, it be worth considering implementing damage types and their respective defenses.

### Target selection
Many gears have effects that impact all humanoids or player's characters.

When searching for all player's humanoids, it is a simple loop over the player pool and returning all their humanoids. However, when dealing with *all* humanoids, including any humanoids in workspace, it becomes necessary to perform a recursive search of sorts.

Implementing this in multiple different gears can be very redundant.

## Status effect module
This is a shared module between client and server. It only replicates from server to client.

Many gears apply statuses to humanoids, like defense, burn, poison, slow, antiheal, and damage boost.

Therefore, the status effect module must modularly implement these status effects, to allow for existing and new statuses to be created and used. Status effects should be easy to register for developers.

Status effects are stateless.

 ### Base status effects
The most basic status effects are:
- **Defense** - Damage taken is decreased
- **Vulnerability** - Damage taken is increased
- **Antiheal** - Heal reduction
- **Heal boost** - Heal increase
- **Weakness** - Damage dealt is reduced
- **Damage boost** - Damage dealt is increased

### Extended status effects
- **Burn** - take tick damage, can be put out by water or by freezing. Burn can spread to other players.
- **Poison** - Take tick damage.
- **Slow** - Restricts player's movement speed by reducing walkspeed and regulating movement magnitude
- **Bleed** - unable to heal and take tick damage
- **Bind** - can not use movement abilities
- **Glitch** - unable to use gear abilities or switch tools
- **Stun** - force unequip, unable to use hotbar, unable to move (platformstand)
- **Blind** - can not see players / humanoids
- **Frozen** - stunned but can be thawed by fires / heat / etc.
- **Sleep** - stunned until damage is taken. Can not be applied if target has taken damage recently.
- **Exhaust** - target is slowed and does reduced damage
- **Shocked** - Affliction can be spread to other players on touch if they are wet. Target's movement is hindered and periodically performs random actions:
  - unequip tool
  - equip random tool
  - use tool ability
  - activate tool
- **EMP** - all abilities will be disabled and only melee weapons will be able to be activated
- **Unwinged** - Unable to fly
- **Heavy** - increases player's artificially weight by a factor, making them fall when attempting to fly.
- **Vision** - Reveals other humanoids through walls
- Invisibility
- **Marked** - Character model is highlighted and can be seen through walls, etc.
- **Confusion** - randomly invert user inputs or swap them.

## Entity tagging module
This is a shared module between client and server. It only replicates from server to client.

Entity tagging helps to index the game quicker for specific resources, like shields, characters, humanoids, player-created entities, and more.

This is especially important when dealing with entities that should absorb / reflect projectiles.

The entity tagging can be simply implemented using ROBLOX's CollectionService. It will be a wrapper on CollectionService with a simplified API.

## Damage tagging module
This is a shared module between client and server. It only replicates from server to client.

As it stands, damage tagging provides very little information about damage dealt, besides the player who dealt it.

Any new damage tagging should include information about:
- damage dealt
    - raw damage
    - actual damage (post-defense or vulnerability)
- origin of damage
    - tool
    - damage description (burn? poison? default?)

The damage tracking and logging system should be used for healing as well.

### Damage tracking
With a new damage tagging system, it is important to make this trackable, without bloating the memory.

By default, damage tags will expire after 15 seconds. This timer is configurable, but caps at 3 minutes. The garbage collection of damage tags should be fairly configurable.

## User input module
This is a shared module between client and server. 

The user input module makes it easy for developers to bridge the gap across client and server when it comes to responding to user input on the server.

Additionally, it also allows provides a wrapper on the various user input services provided by ROBLOX natively (e.g. UserInputService, PlayerMouse, ContextActionService) and consolidates them into one.

This means that the module should provide an improved cross-platform development experience, by giving developers simpler tooling to create bindings for:
- mobile devices
- consoles

It interacts with the [overhauled user interface](./ui.md).

Another issue that exists with the ContextActionService GUI buttons is that the positions must be manually set and can often be inconsistent between gears. The solution for this problem is to create designated positions on the screen that developers can assign abilities to.

## Pool
- Humanoid Detection
- Easy Target Selection API
    - Target pools (e.g. within radius, allies, enemies, etc.)
    - Different selector methods on pools
- Unified Damage Method
    - Damage mitigation
    - Damage multiplication
- Status effects
    - Positives (Overhealth, shield, speed boost, jump boost)
    - Negatives (Bleed, slow, poison, stun, bind)
- Proper tagging system for:  
    - NPCs
    - Damage source
    - Potential LL implementation for damage source
    - Misc entities (shields, reflectors, etc.)
- Simple melee hitbox handling
    - Handle interactions with shields
- Projectile hitbox handling
    - Optimized for client / server interaction
    - Handle gravity, interactions with shields, reflectors, etc.
- Advanced player and entity hitboxes
    - Player hitboxes beyond just parts
    - Shield hitboxes to block hits
    - Reflector hitboxes to interact with projectiles
- Visual effects module
    - various vfx included base
    - port takeo's vfx over
    - API for rendering client first, server second to make gear feel responsive
- Various util functions
    - Bezier (super stomp boots)
    - Custom debris implementation
    - Binding debris deletion to events (e.g. delete debris on tool unequipped)
- Mobile compatible GUI for cooldowns, tool resources, ability indicators, etc.
- Wrapper over ROBLOX standard input (should be globally accessible on client)
 
## Potential features
- Simple modular damage types
    - Damage types with interactions with certain statuses
    - Basic damage types:
        - Impact
        - Pierce
        - Slash
        - Blast
        - Magic (anything else, like elemental stuff)