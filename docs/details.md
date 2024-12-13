### Implementation Details for **Ragdoll Archers**

---

#### **Core Systems**

1. **Ragdoll Physics**:
   - Use a physics engine like **Unity's Rigidbody** for character and enemy motion.
   - Implement joint constraints for ragdoll effects on enemies when hit.
   - Adjust drag and mass values to simulate weight for realistic arrow impacts.
   - Use **hinge joints** for flailing limbs upon death or stagger effects.

2. **Arrow Mechanics**:
   - **Trajectory Calculation**:
     - Use a parabolic equation to calculate the arrow's path based on initial velocity, angle, and gravity (`Physics.gravity` in Unity).
     - Show a visual indicator (like a dotted line) for arrow trajectory during aiming.
   - **Collision Detection**:
     - Use `OnCollisionEnter` to detect impacts with enemies, objects, or apples.
     - Implement layers to differentiate between enemy hitboxes, environmental objects, and pickups (e.g., apples).
   - **Arrow Effects**:
     - Attach particle systems for specific arrow types:
       - **Electric Arrows**: Lightning bolts on collision, with an area damage script.
       - **Poison Arrows**: Green particle trail and periodic damage via a `Coroutine`.
       - **Explosive Arrows**: Trigger `AddExplosionForce` on impact for nearby objects.
     - Use object pooling for arrows to optimize memory usage.

3. **Health and Stamina**:
   - **Health Bar**:
     - Use a UI slider to represent health.
     - Reduce health on enemy hits (`TakeDamage()` method) and replenish it on red or golden apple collection.
   - **Stamina Bar**:
     - Similar to health but tied to actions like charging the bow or jumping.
     - Gradually regenerate stamina using a timer (`InvokeRepeating`).
   - **Apple Interaction**:
     - Detect apple collision using triggers (`OnTriggerEnter`).
     - Use animations to make apples fly in random paths or rotate while moving.

---

#### **Enemies**

1. **Behavior AI**:
   - **Basic Enemies**:
     - Use a NavMesh to navigate terrain and approach the player.
     - Attack with simple melee or slow arrows.
   - **Flying Enemies**:
     - Implement random movement patterns using `Vector3.Lerp` and sine waves for hovering.
     - Detect player position and swoop down to attack.
   - **Mini-Bosses**:
     - Use larger colliders and multiple weak points.
     - Introduce phases where attack patterns change (e.g., melee in the first phase, ranged in the second).
   - **Boss Mechanics**:
     - Implement destructible armor using layered health (`armor > 0 ? armor-- : health--`).
     - Add special abilities like area-of-effect attacks or summoning smaller enemies.

2. **Enemy Spawning**:
   - Use an **object pooler** to instantiate enemies efficiently.
   - Spawn waves using a coroutine:
     ```csharp
     IEnumerator SpawnWave() {
         for (int i = 0; i < enemiesPerWave; i++) {
             Instantiate(enemyPrefab, spawnPoints[Random.Range(0, spawnPoints.Length)], Quaternion.identity);
             yield return new WaitForSeconds(spawnInterval);
         }
     }
     ```

---

#### **Game Progression**

1. **Level Scaling**:
   - Gradually increase enemy health, armor, and damage:
     ```csharp
     enemyHealth = baseHealth + (waveNumber * healthIncrement);
     ```
   - Introduce new enemy types at specific milestones (e.g., wave 5, wave 10).

2. **Upgrade System**:
   - Store player stats (health, damage, stamina) in a scriptable object.
   - Use a **UI shop** for upgrades, deducting skulls on purchase.
   - Apply upgrades immediately by modifying player attributes:
     ```csharp
     player.damage += upgradeValue;
     player.healthMax += upgradeValue;
     ```

3. **Arrow Unlock System**:
   - Maintain a list of locked/unlocked arrows using a boolean array.
   - Unlock arrows on milestones or specific achievements (e.g., 500 skulls earned).
   - Enable arrow selection via a radial menu or inventory system.

---

#### **Multiplayer (PvP & Co-op)**

1. **Input Handling**:
   - Use Unity’s Input System for player controls:
     - Player 1: WASD + Left Shift.
     - Player 2: Arrow Keys + Right Shift.
   - Differentiate between players by assigning input maps.

2. **Multiplayer Modes**:
   - **PvP**:
     - Implement a damage system where arrows from Player 1 and Player 2 only affect the opponent.
     - Add obstacles in the arena for tactical positioning.
   - **Co-op**:
     - Use shared health and stamina pools or individual values.
     - Add revive mechanics where a player can shoot a "revive arrow" at their downed partner.

3. **Networking (Optional for Online Play)**:
   - Use **Photon Unity Networking (PUN)** or Unity’s built-in multiplayer service.
   - Sync player positions, actions, and projectiles across clients.

---

#### **User Interface (UI)**

1. **HUD**:
   - Display:
     - Player health and stamina bars.
     - Skulls earned.
     - Equipped arrow types with cooldown timers.
   - Use `Canvas` components and `Image.fillAmount` for dynamic bars.

2. **Shop UI**:
   - Create categories for abilities and arrows.
   - Use `ScrollRect` to navigate items.
   - Deduct skulls on purchase and update stats:
     ```csharp
     if (playerSkulls >= upgradeCost) {
         playerSkulls -= upgradeCost;
         player.damage += damageUpgrade;
     }
     ```

3. **Game Over Screen**:
   - Show score, highest wave reached, and skulls earned.
   - Include options to retry, go to the main menu, or access the shop.

---

#### **Visual Effects**

1. **Camera Effects**:
   - Add slight screen shake on boss attacks or critical hits.
   - Use `Cinemachine` for dynamic camera transitions (zoom in during mini-boss fights, slow pan for wave start).

2. **Particle Effects**:
   - Create trails for arrows (e.g., smoke for explosive arrows, sparks for electric arrows).
   - Add impact effects like debris flying on collisions.

3. **Environmental Animations**:
   - Moving platforms, swinging objects, and destructible elements.

---

#### **Optimization**

1. **Performance**:
   - Use object pooling for arrows, enemies, and particles.
   - Reduce the number of physics calculations by setting layers to ignore collisions when unnecessary.

2. **Asset Management**:
   - Compress textures and use LOD (Level of Detail) for distant objects.
   - Combine static objects into a single mesh to reduce draw calls.

3. **Testing**:
   - Use automated testing for player stats and upgrade balance.
   - Conduct stress tests with large waves to ensure performance stability.

---

This detailed implementation ensures the game is both fun and technically robust. Let me know if you'd like specifics on any particular section!
