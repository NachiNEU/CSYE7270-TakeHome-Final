# AI Perception and Pursuit System (Unreal Engine) CSYE7270-TakeHome-Final

An Unreal Engine 5 tutorial project that teaches how to build intelligent enemy AI using **Behavior Trees**, **Blackboards**, and **AI Perception / PawnSensing**. By the end, you get an enemy that can **detect**, **chase**, and **engage** the player using industry-standard techniques. 

Created as a final take-home project for a game development course.

---

## 🎥 Tutorial Video

A complete step-by-step walkthrough of the **AI Perception & Pursuit System** is available here:
**[▶️ Watch the Full Tutorial](https://drive.google.com/file/d/1dwqeH_OtU0dfs7Val-HLXTTDeEL0WO10/view?usp=sharing)**

This video covers the entire pipeline—from setting up behavior trees and blackboards to implementing PawnSensing, AI Perception, and chase logic—making it easy to follow along and build the system yourself.


<img width="705" height="421" alt="image" src="https://github.com/user-attachments/assets/e1a37b0b-45e0-414f-9f6d-74bef2031154" />

---

## 🎯 Project Goals

This repo is both a **playable demo** and a **teaching resource**. After working through it, learners should be able to:

* Explain how **Behavior Trees**, **Blackboards**, and **AI Perception / PawnSensing** fit together in a perception → decision → action pipeline.
* Describe why Behavior Trees are often preferred over Finite State Machines for modern game AI.
* Implement a complete **chase & pursuit system** with distance-based logic (chase, attack, disengage).
* Debug common AI problems by isolating issues in **perception**, **decision making**, or **movement / actions**.
* Extend the base system with new behaviors (patrol, search, multiple enemy types).

---

## 🧠 What You’ll Learn

From the tutorial & project files: 

* **Game AI Fundamentals**

  * The goal is *believable* NPCs, not perfect opponents.
  * Real-time performance & designer-friendly tools.

* **Behavior Trees**

  * Selectors, Sequences, Decorators, Tasks, and Services.
  * Root-to-leaf evaluation every tick for reactive behavior.
  * Using decorators + observer aborts for instant state changes.

* **Blackboard System**

  * Storing shared AI memory as key–value pairs (`seeingTarget?`, `targetActor`).
  * Decoupling perception (writers) from decision logic (readers).

* **Perception & Sensing**

  * **PawnSensing** for beginner-friendly vision (On See Pawn events).
  * **AI Perception (AISense_Sight)** for more advanced setups and multiple senses.
  * Sight radius, lose-sight radius, FOV, and line-of-sight.

* **Chase & Pursuit Behavior**

  * Distance-based states: idle, chase, attack, disengage.
  * Hysteresis (different engage vs disengage distances) to avoid twitchy behavior.

* **Services & Performance**

  * Running distance checks on a timer (e.g., every 0.5s) instead of every frame.
  * Scalable design for multiple enemies in a level.

The slide deck in this repo visualizes the **AI pipeline** (Sensors → Blackboard → Behavior Tree → Actions) and the typical NPC loop (patrol → chase → search → back to patrol). 

---

## 👾 Features

Enemy AI examples included in the project: 

* **Enemy 1 – PawnSensing-based guard**

  * Uses `PawnSensing` to detect the player via vision.
  * Behavior Tree with idle and chase sequences.
  * Custom `Task_ChaseTarget` task using NavMesh pathfinding.
  * Blackboard keys:

    * `seeingTarget?` (bool)
    * `targetActor` (object)

* **Enemy 2 – AI Perception–based guard**

  * Uses `AIPerception` component with `AISense_Sight`.
  * Player character set up as an `AIPerceptionStimuliSource`.
  * Same behavior concepts as Enemy 1, but with the more flexible perception system.

Gameplay behavior:

* Patrols or idles by default.
* Detects player within a configurable vision range (e.g., 600 units).
* Chases the player using `AI MoveTo` and NavMesh.
* Stops pursuit when the player escapes beyond a disengage distance.
* Can be extended with attack animations, search, and patrol states.

---

## 📂 Repository Structure

(Adjust to match your actual folders, but conceptually it looks like this.)

* `AI/`

  * `BT_Enemy` – Behavior Tree asset.
  * `BD_AI` – Blackboard asset.
  * `Tasks/Task_ChaseTarget` – Custom BT Task for chasing the target.
  * `Services/BTS_CheckChaseDistance` – BT Service for distance monitoring (if included).
* `Blueprints/`

  * `BP_AI_Enemy` – AI Controller using PawnSensing.
  * `BP_Enemy` – Enemy character (mesh, movement, AI controller hookup).
  * `BP_AI_Enemy_2` – AI Controller using AI Perception.
  * `BP_Enemy_2` – Second enemy character variant.
  * `BP_ThirdPersonCharacter` – Player character with perception stimuli source.
* `Docs/`

  * `Tutorial-Documentation.pdf / .docx` – Full written tutorial (theory + implementation + debugging + exercises).
  * `Pedagogical-Report.pdf / .docx` – Teaching philosophy & design rationale.
  * `AI-Perception-and-Pursuit-System-Presentation.pdf` – Slide deck summarizing concepts.

---

## 🛠 Tech Stack

* **Engine:** Unreal Engine 5.3+ (works with UE 4.27+ with minor adjustments). 
* **Language:** Blueprints (no C++ required).
* **Template:** Third Person Template (or any project with a character & camera).
* **Recommended Hardware:** 16 GB RAM.

---

## 🚀 Getting Started

### 1. Clone or Download

```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
```

Or download the project as a ZIP and extract it.

### 2. Open in Unreal Engine

1. Open Unreal Engine (5.3+ recommended).
2. From the launcher, browse to the project folder and open the `.uproject`.
3. Let Unreal compile shaders and load the assets.

### 3. Load the Demo Level

1. Open the main demo level (e.g., `AI_Demo_Level.umap` if provided).
2. Press **P** in the viewport to visualize the **NavMesh** (you should see a green overlay on walkable areas).
3. Hit **Play** to test:

   * Move the player around.
   * Walk into the enemy’s field of view.
   * Watch them detect, chase, and then disengage.

If you use a different level, make sure it contains: 

* A placed instance of `BP_Enemy` or `BP_Enemy_2`.
* A **Nav Mesh Bounds Volume** large enough to cover your walkable area.
* A player character using the provided Third Person character setup.

---

## 🧩 How the System Works

High-level loop (also illustrated in the slides):

1. **Perception**

   * PawnSensing or AI Perception detects the player (On See Pawn / On Target Perception Updated).
   * The AI Controller writes to the Blackboard:

     * `seeingTarget? = true`
     * `targetActor = player reference`.

2. **Decision (Behavior Tree)**

   * Root selector chooses between **Idle** and **Chasing** sequences.
   * Decorators on each sequence read Blackboard values (e.g., “Is `seeingTarget?` set?”).
   * Observer Aborts (Self) make the tree immediately re-evaluate when blackboard values change.

3. **Action**

   * `Task_ChaseTarget` uses `AI MoveTo` to follow `targetActor`.
   * A BT Service (optional) periodically checks distance and clears the target when the player is too far.
   * When `targetActor` is cleared or `seeingTarget?` becomes false, the tree falls back to Idle.

This separation of **sensing → thinking → acting** is intentional for teaching: each layer can be tested and debugged independently. 

---

## 🧪 Using the Tutorial

The written tutorial in `Tutorial-Documentation` walks students through the project in this order: 

1. **Theoretical Foundations** – Behavior Trees, Blackboards, Perception, Services, chase distance logic.
2. **Step-by-Step Implementation**

   * Create Blackboard (`BD_AI`) and Behavior Tree (`BT_Enemy`).
   * Set up AI Controller (`BP_AI_Enemy`) and Enemy Blueprint (`BP_Enemy`).
   * Configure NavMesh and movement.
   * Implement PawnSensing + Blackboard writes.
   * Add decorators and chase task to the Behavior Tree.
   * Add AI Perception & second enemy type.
3. **Debugging Guide**

   * AI won’t chase? (PawnSensing, NavMesh, key names, Observer Aborts).
   * Blackboard keys not found.
   * Services not running.
   * NavMesh problems.
4. **Practice Exercises**

   * Increase chase distance to 1000 units.
   * Create a second enemy with different parameters.
   * Extend with patrol/search behaviors.

Learners are encouraged to **test after each major step** and use **Print String** nodes heavily for debugging.

---

## 📊 Teaching / Pedagogical Notes

The project is deliberately structured for teaching beginner–intermediate Unreal users: 

* **Scaffolded learning** – start from theory, then build AI controller → blackboard → behavior tree → perception → services in small, testable increments.
* **Visual emphasis** – diagrams and slides show the AI pipeline, common NPC loops, and how detection affects behavior.
* **Error-prevention & debugging** – frequent warnings about:

  * Case-sensitive Blackboard key names.
  * Observer Aborts being left as “None”.
  * Services created but not attached.
  * Missing NavMesh volumes.
* **Performance & scalability** – services run on intervals, squared distance checks, and shared Behavior Tree assets are used to illustrate scalable design.

If you’re using this repo for teaching, you can also use the **Pedagogical Report** document to justify design choices and assessment strategies.

---

## 🔧 Extending the Project

Ideas for further work (some suggested directly in the tutorial):

* Add **patrol** behavior using waypoints and a “Patrol” sequence in the Behavior Tree.
* Add a **search** state that moves to the last known player position and looks around.
* Implement **hearing** using AI Perception and noise events.
* Share a Blackboard between multiple enemies so they can **alert each other**.
* Use decorators & services to implement an **awareness meter** that ramps up over time.

---

## 📣 Credits

* **Author / Developer:** Nachiket Sahare (tutorial, implementation, slides, and written docs).
* **Engine:** Unreal Engine by Epic Games.
* **Course / Context:** Final take-home assignment for a university game development course (feel free to replace this with your specific course info).

If you use this project or find it helpful, consider:

* Starring the GitHub repo.
* Sharing screenshots, videos, or extensions you build.
* Filing issues or suggestions for improvements.

---
