<p align="center"><img src="Resources/Icon128.png" alt="UELink" width="96"></p>

# UELink — Unreal Engine 5 bridge for After Effects, Cinema 4D, Blender, 3ds Max & Houdini

**Version 1.0 · Unreal Engine 5.7 and 5.8 · Windows 64-bit**

UELink connects **Unreal Engine 5 Sequencer** to your compositing and 3D applications. Send cameras,
nulls, meshes and animation between Unreal and the app you work in, without rebuilding cameras or
matching animation by hand.

**Video overview:** https://youtu.be/ErXj9IhNQIw
**Support — Discord:** https://discord.gg/7eNVSDA6yS
**Support — email:** parulep1@gmail.com

---

## Contents

1. [What UELink does](#1-what-uelink-does)
2. [Requirements](#2-requirements)
3. [Installing and opening UELink](#3-installing-and-opening-uelink)
4. [The UELink panel](#4-the-uelink-panel)
5. [The Actors list](#5-the-actors-list)
6. [After Effects](#6-after-effects-unreal--after-effects)
7. [Reflection nulls](#7-reflection-nulls)
8. [Cinema 4D](#8-cinema-4d-unreal--cinema-4d)
9. [Blender](#9-blender-unreal--blender)
10. [3ds Max](#10-3ds-max-unreal--3ds-max)
11. [Houdini](#11-houdini-unreal--houdini)
12. [Receiving a scene in Unreal](#12-receiving-a-scene-in-unreal)
13. [Procedural objects (experimental)](#13-procedural-objects-experimental-cinema-4d-and-blender)
14. [Settings page](#14-settings-page)
15. [Where UELink writes files](#15-where-uelink-writes-files)
16. [Troubleshooting](#16-troubleshooting)
17. [Known limitations](#17-known-limitations)
18. [Uninstalling](#18-uninstalling)
19. [Third-party software and trademarks](#19-third-party-software-and-trademarks)
20. [Getting support](#20-getting-support)

---

## 1. What UELink does

| App | Direction | What moves |
|-----|-----------|------------|
| **After Effects** | One way: Unreal → AE | Animated 3D cameras (focal length, focus distance and aperture on every frame), animated 3D nulls, reflection-null rigs, "paste media here" comps, optional draft preview plate. |
| **Cinema 4D** | Both ways: Unreal ⇄ C4D | Unreal → C4D: cameras, nulls and meshes with their animation. C4D → Unreal: static and animated geometry, cameras and nulls, Cloners as instances, Rigid Body simulations. |
| **Blender** | Both ways: Unreal ⇄ Blender | Unreal → Blender: cameras, nulls and meshes with their animation. Blender → Unreal: static and animated geometry, cameras and empties, Rigid Body simulations. |
| **3ds Max** | Both ways: Unreal ⇄ 3ds Max | Unreal → Max: cameras, nulls and meshes with their animation. Max → Unreal: environment, props and cameras (Datasmith) plus deforming geometry (Alembic), routed automatically. |
| **Houdini** | Both ways: Unreal ⇄ Houdini | Unreal → Houdini: cameras, nulls and meshes with their animation. Houdini → Unreal: animated simulations (Alembic), static meshes (FBX), smoke and fire volumes (OpenVDB). |

You only need the apps you use. Each integration works on its own.

**How it works.** Everything travels as ordinary files. UELink never opens, finds or controls
another application: **you open each app yourself**, and a small UELink connector inside that app
picks the data up.

- **After Effects:** UELink writes an ExtendScript (`.jsx`) file and installs a small hidden
  After Effects extension that runs it as soon as After Effects is open.
- **Cinema 4D, Blender, 3ds Max, Houdini:** UELink installs a small connector into the app's own
  user folder. Both sides watch a shared exchange folder. Unreal sends a scene description plus one
  FBX file per unique mesh; the apps send Alembic, Datasmith, FBX or OpenVDB files back.

No network traffic, no telemetry, no background services.

> **Characters (rigged / skinned meshes) are coming in UELink version 2.** In this version a
> character in a send is left out and the app tells you which object it was. Everything else in the
> selection still goes through.

---

## 2. Requirements

- **Windows 10 or 11** (64-bit)
- **Unreal Engine 5.7 or 5.8**
- One or more of these, which you install yourself:
  - **Adobe After Effects 2024, 2025 or 2026** (any minor build)
  - **Maxon Cinema 4D 2023** or newer
  - **Blender 4.0** or newer
  - **Autodesk 3ds Max 2021** or newer
  - **SideFX Houdini 22.0** or newer
- For **3ds Max → Unreal through Datasmith**: Epic's free *Datasmith Exporter for 3ds Max*,
  installed into 3ds Max by you.

UELink does not bundle, install or redistribute any of these applications.

### Engine plugins UELink turns on

Enabling UELink also enables these plugins, all of which ship with Unreal Engine: Level Sequence
Editor, Movie Render Queue, Movie Render Queue Additional Render Passes, Control Rig, Datasmith
Importer, Datasmith Content, USD Importer, glTF Exporter, Geometry Cache and Alembic Importer.

**Two of them are marked Beta by Epic** in Unreal Engine 5.7 and 5.8:

- **USD Importer** — reads the USD files Cinema 4D can send.
- **Movie Render Queue Additional Render Passes** — used only for the optional Cryptomatte EXR
  preview plate.

---

## 3. Installing and opening UELink

1. Install **UELink** from **Fab** through the Epic Games Launcher. The Launcher builds the plugin
   into your engine; there is no separate installer.
2. Open your Unreal project.
3. Go to **Edit → Plugins**, search for **UELink** and make sure it is enabled. Restart the editor
   if Unreal asks.
4. Open the panel from **Window → UELink**, or click the **UELink** button on the editor toolbar.

---

## 4. The UELink panel

The panel has a navigation bar on the left:

| Tab | What it is for |
|-----|----------------|
| **After Effects** | Export cameras and nulls to After Effects, reflection nulls, preview plate. |
| **Cinema 4D** | Install the Cinema 4D connector, send to Cinema 4D, manage scenes received from it. |
| **Blender** | The same for Blender. |
| **3ds Max** | The same for 3ds Max. |
| **Houdini** | The same for Houdini, plus smoke/fire volumes and fluid materials. |
| **Settings** | Options shared by every tab (also under **Edit → Project Settings → Plugins → UELink**). |
| **About** | Version, supported apps and how UELink works. |

The status line at the bottom of each page tells you what just happened (for example
"Ready — add actors and click Export").

---

## 5. The Actors list

Every sending tab has an **Actors** list on the right. It holds the actors that will be sent.

- **Add actors:** drag them from the **World Outliner** or from the **Sequencer** track list into
  the Actors list.
- **What each badge means:** **C** = camera, **M** = mesh, **N** = null. The badge tells you what
  the actor becomes in the other app.
- **Remove one actor:** click the **✕** on its row. **Clear all** empties the list.
- **Naming:** the first camera in the list names the After Effects comp and the export file.
- **Create Null:** select one or more objects in the viewport and click **Create Null**. UELink
  creates a null at each object's position and orientation, attaches it to the object so it follows
  the object's animation, adds it to the open Level Sequence and adds it to the Actors list.
  Use it when you want a clean tracking point on a mesh.

> The Level Sequence you want to send must be **open in Sequencer**. UELink reads the frame range
> and frame rate from it. If a **Movie Render Queue** job exists for the sequence, UELink uses that
> job's resolution, frame rate and frame range instead.

---

## 6. After Effects (Unreal → After Effects)

### 6.1 One-time After Effects setup

1. Open After Effects.
2. Go to **Edit → Preferences → Scripting & Expressions**.
3. Tick **Allow Scripts to Write Files and Access Network** and click OK.

Do this once per machine. If you ever see *"Cannot write to disk — scripting access disabled"*,
this preference is the cause.

**First export only:** if After Effects was already open the very first time you export, close
it and open it again once. After Effects loads extensions only when it starts. From then on every
export arrives on its own.

### 6.2 Export a shot

1. Open the shot's Level Sequence in Sequencer.
2. Open the **After Effects** tab.
3. Drag the camera and any objects you want as nulls into the **Actors** list.
4. Set the options in the **Export Settings** column (see 6.4).
5. Click **Export to After Effects**.
6. Switch to After Effects (open it if it is closed). Within about two seconds a new composition
   appears with the camera and nulls, fully animated.

The comp and the script file are named `<CameraName>_<Width>x<Height>_<FPS>_<Scale>`, for example
`CineCameraActor_1920x1080_24_1`.

**What arrives in After Effects**

- **Cameras** — an animated 3D camera with focal length, focus distance and aperture keyed on every
  frame. Look-at (tracking) cameras are rebuilt so they aim correctly. Cameras mounted on a
  Camera Rig Rail are supported when the rail position is keyed in Sequencer.
- **Nulls** — every non-camera actor becomes an animated 3D null at the matching position and
  orientation.
- **Static values** — a value that never changes is written once instead of on every frame.

### 6.3 Update an existing comp

After changing the animation in Unreal, click **Update Existing Comp** instead of Export.

UELink finds the comp it made earlier and replaces only the keyframes on the layers it created.
It **keeps** your parenting, effects, blending modes, layer order and every layer you added
yourself. If the comp was deleted or renamed beyond recognition, Update creates a new one, so the
button always works. Update never re-renders the preview plate.

### 6.4 Export Settings

| Option | What it does |
|--------|--------------|
| **Export scale** | How many After Effects units equal one Unreal centimetre (default 1). Use it to make the scene larger or smaller in After Effects. Applies to After Effects only. |
| **Null rotation offset (selected null)** — **X / Y / Z / Reset** | Select a null in Unreal and click **X**, **Y** or **Z** to turn it by 90° around that axis in After Effects. Click again to add another 90°. **Reset** clears it. Use it when a layer parented to the null faces the wrong way, for example a phone screen in portrait. |
| **New null — Position only** | Turn on, then click **Create Null**. The new null follows the object's position but not its rotation or scale. Use it as the main null on a spinning object (a wheel, a turntable) so reflection nulls around it don't spin too. |
| **Create linked media comp per null** | For every exported null, UELink builds a 1000 × 1000 comp named `<NullName> - paste media here`, places it in the main comp as a 3D layer and links it to the null's position and rotation. Drop footage or graphics into that comp and they ride the null. Scale stays free for you to set. On by default. |
| **Render draft preview plate on export** | Renders a quick draft image sequence of the shot inside Unreal (Movie Render Queue) and places it as the comp's bottom background layer, so you can line up graphics against the real shot. Only on Export, never on Update. |
| **Render Cryptomatte EXR (instead of JPEG)** | Renders the plate as a 16-bit multilayer EXR with an Object ID (Cryptomatte) pass instead of JPEG, so you can isolate objects in After Effects. Needs the preview plate option on. |

**Preview plate already rendered?** If a plate for that camera already exists, UELink asks:
**Use previous render as backplate** (instant) or **Overwrite previous render** (renders again).

Plates are saved to `<YourProject>/Saved/MovieRenders/<CameraName>/`.

### 6.5 Camera shake

Camera shake is added by Unreal while the game runs and is not part of the camera's keyed
animation, so it cannot be exported. When UELink finds a camera-shake track in the sequence, it
warns you before exporting. To keep the shake, bake it into the camera's keys in Sequencer first.

### 6.6 After Effects options on the Settings page

| Option | What it does |
|--------|--------------|
| **Output folder for After Effects scripts** | Where the `.jsx` is saved, relative to the project. Default `Saved/AEExport`. |
| **Pass F-stop through** | On: After Effects receives Unreal's f-stop value directly. Off: it is converted to a physical aperture size. |
| **Hold (step) keyframes** | On: keys hold until the next one. Off (default): linear keys. |
| **Also write a JSON data file** | Writes a data file next to the script. Only needed when reporting a problem. |

---

## 7. Reflection nulls

Reflection nulls are a cluster of extra nulls arranged around one **main null**. In After Effects
you can attach light streaks, reflections, sparkles or text to them. They follow the main null's
animation, can animate on their own, and export with the camera.

### 7.1 Create a cluster

1. In Unreal, select **one** actor to be the main null (usually a null made with **Create Null**).
2. In the **Reflection Nulls** card on the After Effects tab, set:
   - **Axis** — **X**, **Y** or **Z**. For Circular, Square and Random this is the axis the layout
     faces (Z lays the cluster flat in the XY plane). For Linear it is the direction of the line.
   - **Null count** — how many nulls to create.
   - **Cluster size (cm)** — the radius or half-width of the layout.
3. Click a layout: **Circular**, **Square**, **Linear** or **Random**.

The nulls appear around the main null, are attached to it, are added to the Level Sequence and are
added to the Actors list. You can move individual nulls by hand afterwards.

**Clear reflection nulls** removes the cluster of the selected main null, together with its
Sequencer entries. The main null itself is never deleted. Keep the Level Sequence open when
clearing.

### 7.2 Animate a cluster

Under **Animation (click to combine)**, turn on one or more building blocks:

| Block | Movement |
|-------|----------|
| **Cascade** | Each null flies out from the main null to its place (or back in). |
| **Pop** | Each null scales up from nothing in place (or down to nothing). |
| **Float** | A continuous, gentle drift. Always linear; ignores direction and easing. |

Then choose how it moves:

| Control | What it does |
|---------|--------------|
| **Dir — In / Out / In-Out** | **In** plays forward (from the main null to the final layout). **Out** plays in reverse. **In-Out** plays forward, holds, then plays in reverse. |
| **Easing — Linear / Ease In / Ease Out / In-Out / Invert** | **Ease In** starts slowly, **Ease Out** finishes slowly, **In-Out** does both. **Invert** is only available with the In-Out direction: the move in eases at the end and the move out eases at the start. |
| **Ease strength (%)** | How strong the easing is. 66 % is close to After Effects' Easy Ease. |
| **Duration (frames)** | How long one move takes. |
| **Stagger (frames/null)** | Delay between one null and the next, so they start one after another. |
| **In-Out gap (frames)** | How long the cluster holds its final layout between the in and out moves. Only used by In-Out. |

Two buttons apply it:

- **Apply animation** — builds the animation from the current settings. This resets key timing.
- **Update easing** — applies new easing and strength to the existing keys **without moving
  them**, so timing you adjusted by hand in Sequencer is kept.

Click **Export to After Effects** (or **Update Existing Comp**) to send the animation.
Reflection nulls are the only nulls whose scale is sent to After Effects (for Pop).

---

## 8. Cinema 4D (Unreal ⇄ Cinema 4D)

### 8.1 Install the connector (once)

1. Open the **Cinema 4D** tab in Unreal.
2. Under **1. Install the Cinema 4D panel**, click **Install Cinema 4D plugin**.
3. Restart Cinema 4D once. The **UELink** panel opens on its own at start-up. To reopen it later,
   use the **Extensions** menu → **UELink**.

When the tab says **Update available**, click **Update Cinema 4D plugin** and restart Cinema 4D.
This happens after you update UELink.

Make sure **Enable live bridge (send to and receive from Cinema 4D)** is on.

### 8.2 Unreal → Cinema 4D

1. Drag the actors into the tab's **Actors** list.
2. Under **2. Send this page's actors to Cinema 4D**, click **Export to Cinema 4D**.
3. In Cinema 4D the objects appear under a **UELink** group with their animation. The project's
   frame rate, frame range and render resolution are set to match the Unreal sequence.
4. After changes in Unreal, click **Update Cinema 4D**. The same objects are refreshed in place;
   no duplicates.

Options:

- **Export destination (optional)** — a different folder to write the send to. Leave it on
  default unless you need the files somewhere specific. **Browse…** picks a folder.
- **Send real mesh geometry** — on: real meshes are sent. Off: simple boxes of the right size are
  sent instead (faster). This option is shared by every app.

### 8.3 Cinema 4D → Unreal

1. In Cinema 4D, **select** the objects to send. Selecting a group sends its visible children.
2. In the **UELink** panel, set the options:
   - **Export nulls to Unreal** — also send nulls as Unreal actors. Turn it off to send the camera
     and geometry only (useful when your scene has many organising nulls).
   - **Send as static mesh (no animation)** — sends the current frame only, without animation.
   - **Experimental: Export Procedural Objects** — see
     [section 13](#13-procedural-objects-experimental-cinema-4d-and-blender).
   - **Include subdivision on simulations** — keeps a Subdivision Surface on top of a simulation.
   - **Export folder** — optional; **Browse** to choose, **Clear** to go back to the default.
3. Click **Export to UE** and enter a **job name**. The job name becomes the scene's folder in
   Unreal.
4. Unreal picks the scene up and asks where to place it (see [section 12](#12-receiving-a-scene-in-unreal)).
5. After changes in Cinema 4D, click **Update UE**. The same job is refreshed in place.
6. **Add selection to job** adds the currently selected objects to the last job, so the next
   **Update UE** includes them.

Sending is automatic: UELink looks at each selected object and sends geometry and animation as
Alembic, **Cloners** as instances and **Rigid Body** simulations through their own route.
**Advanced...** lets you force one route (**Auto (default)**, **Alembic**, **Cloner → Instances**,
**Rigid Body**). Leave it on Auto unless you need one specific route.

Redshift cameras are supported.

---

## 9. Blender (Unreal ⇄ Blender)

### 9.1 Install the add-on (once)

1. Open the **Blender** tab in Unreal.
2. Click **Install Blender add-on**.
3. Restart Blender once. In the 3D Viewport press **N** and open the **UELink** tab in the sidebar.

When the tab says **Update available**, click **Update Blender add-on** and restart Blender.

Make sure **Enable live bridge (send to and receive from Blender)** is on.

### 9.2 Unreal → Blender

Drag actors into the tab's **Actors** list and click **Export to Blender**. The scene appears in
Blender with its animation. **Update Blender** refreshes it in place. **Export destination** and
**Send real mesh geometry** work as described for Cinema 4D.

### 9.3 Blender → Unreal

1. **Select** the objects to send. Blender always sends the selection only; selecting a parent
   sends its visible children.
2. In the **UELink** sidebar tab, set the options:
   - **Export nulls to Unreal** — also send empties as Unreal nulls.
   - **Export folder** — optional custom folder.
   - **Experimental: Export Procedural Objects** — see
     [section 13](#13-procedural-objects-experimental-cinema-4d-and-blender).
   - **Send as static mesh (no animation)** — sends only the current frame.
   - **Include subdivision on simulations** — keeps a Subdivision Surface modifier after a
     simulation.
3. Click **Send to Unreal** and enter a job name.
4. After changes, click **Update Unreal**. **Add selection to job** adds the current selection to
   the last job.

Ordinary modifiers such as Subdivision Surface and Mirror can stay on the mesh. If nothing in the
selection moves, Blender offers to send it as a static mesh.

**Advanced — force a specific lane:** **Auto (Scene)** (default, routes every object for you),
**Alembic (forced)** (everything as one animated geometry cache) or **Rigid/Soft Body**
(simulated objects baked to a cache).

Only perspective cameras are sent. Orthographic and panoramic cameras are left out, and Blender
tells you so.

---

## 10. 3ds Max (Unreal ⇄ 3ds Max)

### 10.1 Install the connector (once)

1. Open the **3ds Max** tab in Unreal.
2. Click **Install 3ds Max connector**.
3. Restart 3ds Max once. The **UELink** panel opens on its own at start-up. To reopen it, go to
   the **Utilities** panel → **MAXScript** → choose **UELink** → **Open UELink panel**, or assign
   the **UELink** action (category *UELink*) to a toolbar or menu.

When the tab says **Update available**, click **Update 3ds Max connector** and restart 3ds Max.

Make sure **Enable live bridge (send to and receive from 3ds Max)** is on.

### 10.2 Unreal → 3ds Max

Drag actors into the tab's **Actors** list and click **Export to 3ds Max**. **Update 3ds Max**
refreshes the scene in place.

### 10.3 3ds Max → Unreal

1. In the **UELink** panel:
   - **Selected objects only** — tick it to send only the selection (otherwise the whole scene is
     sent).
   - **Send as static mesh (no animation)** — sends only the current frame.
   - **Export folder** — optional; **Browse...** to choose, **Reset** for the default.
2. Click **Send to Unreal** and enter a job name. UELink routes automatically: environment, props
   and cameras through **Datasmith**, deforming (animated) geometry as **Alembic**.
3. After changes, click **Update**. **Add selection to job** adds the selection to the last job.

**Advanced — force a specific lane:** **Send Alembic** sends deforming geometry as an animated
Alembic cache.

The Datasmith route needs Epic's free **Datasmith Exporter for 3ds Max**, installed into 3ds Max.

---

## 11. Houdini (Unreal ⇄ Houdini)

### 11.1 Install the connector (once)

1. Open the **Houdini** tab in Unreal.
2. Click **Install Houdini connector**.
3. Restart Houdini once.

When the tab says **Update available**, click **Update Houdini connector** and restart Houdini.

Make sure **Enable live bridge (send to and receive from Houdini)** is on.

### 11.2 Unreal → Houdini

Drag actors into the tab's **Actors** list and click **Export to Houdini**. **Update Houdini**
refreshes the scene in place.

### 11.3 Houdini → Unreal: the UELink Export node

1. Inside a Geometry network, press **Tab** and add a **UELink Export** node. Connect it to the
   geometry you want to send (or pick the output node in **Source Geometry**).
2. Set the parameters:

| Parameter | What it does |
|-----------|--------------|
| **Format** | **Alembic** — an animated simulation or animated geometry. **FBX** — a static mesh at the start frame. **VDB** — a smoke or fire volume. |
| **Job Name** | The scene's name and folder in Unreal. |
| **Mode** | **Create** for the first send, **Update** to refresh the same job. |
| **Frame Range** | First and last frame to send. Equal values send one frame. |
| **Output File** | Where the files are written (optional). |
| **Volume Precision**, **Volume Fields** | VDB only: file size versus quality, and which grids to send. |

3. Click **Send to Unreal**. Send again with the same job name to update.

**Smoke and fire volumes** have their own guide, bundled with the plugin:
[Houdini smoke and fire volumes](docs/houdini-vdb.md).

### 11.4 Fluids: the UELink Attributes node

For liquid surfaces, add a **UELink Attributes** node after the surface mesh and before the
surface File Cache. It prepares the attributes Unreal needs:

- **UVs** — keep existing UVs, or project new ones (choose the plane and **Texture Size**).
- **Normals** — keep, generate or recompute smooth normals.
- **Motion Vectors (Velocity)** — keep the velocity for motion blur in Unreal.

**Check Setup** inspects the current frame and reports anything missing, without running your
simulation. Then send the cached surface through **UELink Export**.

### 11.5 Make a material follow the fluid

On the Houdini tab in Unreal, under **4. Make a material follow the fluid**: pick a material and
click **Convert to follow the fluid**. UELink makes a copy of the material whose textures move with
the liquid's surface instead of sliding across it. Options:

- **Replace UV selectors and other UV channels** — also converts layers that use other UV channels.
- **Also apply the copy to the selected UELink cache actors** — assigns the copy right away.

Your original material is never changed.

---

## 12. Receiving a scene in Unreal

When an app sends a scene, Unreal imports it automatically while the editor is open.

1. **Place Imported Content** appears the first time a job arrives. Choose:
   - **Current Level** — into the level you have open.
   - **Existing Sublevel** — into one of the level's sublevels.
   - **New Sublevel** — into a new sublevel named after the job.

   Click **Place**. **Cancel** discards the import; nothing is created.
2. The assets go to `/Game/UELink/<App>/<JobName>/` in the Content Browser, the actors are placed in
   the chosen level, and a **Level Sequence** named after the job opens with the animation.
3. When the app sends an **Update** of the same job, Unreal replaces the previous import in place
   and reuses your placement choice without asking again.
4. **Save** the level (use **File → Save All** when the import went into a sublevel).

### Manage imported jobs

Each app tab has **3. Manage imported jobs**. It lists every job received from that app.
**Remove** deletes a job completely — its content, its actors and its Level Sequence — so you can
import it again cleanly.

### Import options on the Settings page

| Option | What it does |
|--------|--------------|
| **Optimize imported sequences** | Removes redundant keys and tracks that never move. On by default. Turn it off if an imported animation looks wrong, then import again. |
| **Create a placeholder material for each imported material slot** | Gives every material slot its own plain material so you can tell parts apart and replace them. |
| **Alembic cache mode** | How animated geometry is imported. **GPU vertex cache** (fast, light on memory; plays in the viewport, Sequencer and Movie Render Queue, but is not included in packaged games). **Baked** (a standard Unreal Geometry Cache; works in packaged games; large caches import slowly and use a lot of memory). |
| **Store every Nth frame** | Baked mode only. Keeps every Nth frame to import faster and use less memory. Unreal blends between the stored frames. 1 keeps every frame. |

---

## 13. Procedural objects (experimental, Cinema 4D and Blender)

To send generators, instances or Geometry Nodes, tick **Experimental: Export Procedural Objects**
in the app's UELink panel. UELink bakes temporary polygon geometry and animation while leaving your
original setup untouched and editable. Unreal receives the result, not an editable node setup.

Limits:

- Topology, parts, UVs and material assignments must stay the same on every frame. If they change,
  the send stops and names the object and frame.
- Custom split normals and normal tags are refused.
- Render hide/show animation is kept (including Cinema 4D parent Display tags). Viewport-only hiding
  is ignored. Partial-opacity Display-tag fades are refused.
- Blender volumes, point clouds and particle emitters are refused.
- Cinema 4D Cloners do not need this option; they always use their own route. A send that mixes
  Cloners with objects that need baking is refused — send them as separate jobs.
- Particles are not transferred from any app.

---

## 14. Settings page

The **Settings** tab (also **Edit → Project Settings → Plugins → UELink**) holds options shared by
every tab. The After Effects options are in [6.6](#66-after-effects-options-on-the-settings-page) and
the import options in [section 12](#12-receiving-a-scene-in-unreal).

### Export limits

Cinema 4D, Blender and Houdini check these before every send to Unreal. A refused send names the
limit, so you know which one to raise.

| Limit | What it does |
|-------|--------------|
| **Max memory for animated geometry (GB, 0 = Auto)** | The largest animated cache Unreal will load. A send that needs more is refused before it exports. |
| **Max separate moving objects** | Above this, an animated send arrives as one geometry cache instead of separate meshes. Never refuses. |
| **Warn above N million polygons (Cinema 4D)** | Cinema 4D asks OK/Cancel above this. A warning only. |
| **Warn above N MB estimated size (Blender)** | Blender shows a size warning above this. A warning only. |
| **Max frames per procedural / volume send** | Longer procedural (Cinema 4D / Blender) or volume (Houdini) sends are refused. |
| **Max clones per cloner** | A Cinema 4D Cloner with more clones refuses the send and names the Cloner. |

### Export cache

Every send leaves files in the exchange folder. **Clean up export cache...** finds the files no
actor or asset in your project still uses and lets you move or delete them. Files still in use,
and export folders you chose yourself, are never touched.

---

## 15. Where UELink writes files

Everything UELink writes is inside your project or your own Windows user folders. It never needs
administrator rights.

| Item | Location |
|------|----------|
| After Effects scripts | `<YourProject>/Saved/AEExport/` |
| Preview plate frames | `<YourProject>/Saved/MovieRenders/<CameraName>/` |
| After Effects extension | `%APPDATA%\Adobe\CEP\extensions\com.uelink.listener\` |
| Exchange folder (all apps) | `%LOCALAPPDATA%\UELink\exchange\<app>\` |
| App connectors | Each app's own user plugin, add-on or scripts folder |
| Imported assets | `/Game/UELink/<App>/<JobName>/` |
| Logs | `%APPDATA%\UELink\` and `%LOCALAPPDATA%\UELink\exchange\<app>\*.log` |

### The one registry value UELink sets

For After Effects to load the UELink extension, every After Effects export sets
`PlayerDebugMode = "1"` under `HKEY_CURRENT_USER\Software\Adobe\CSXS.9` through `CSXS.13`. This is
Adobe's documented per-user switch for loading extensions that are not code-signed through Adobe.
It needs no administrator rights and affects only your Windows account.

While it is on, After Effects will load *any* unsigned extension in your extensions folder, not
only UELink's. If that matters in your environment, delete those values; you can still run each
exported `.jsx` by hand with **File → Scripts → Run Script File**. UELink sets the value again on
the next After Effects export. The Cinema 4D, Blender, 3ds Max and Houdini bridges never touch it.

---

## 16. Troubleshooting

| Problem | What to do |
|---------|------------|
| The first After Effects export does nothing and After Effects was already open | Close and reopen After Effects once (see 6.1). |
| The export succeeds but nothing appears in After Effects | Turn on **Allow Scripts to Write Files and Access Network** in After Effects (see 6.1). |
| After Effects reports *"invalid keyframe data version number"* | One property failed. `%APPDATA%\UELink\jsx.log` names it; send it to support. |
| The export comes out without animation | Make sure the shot's Level Sequence is open in Sequencer and the actor is animated by it. Send `%APPDATA%\UELink\sampler.log` to support. |
| A layer parented to a null faces the wrong way | Select the null and use **Null rotation offset X / Y / Z** (see 6.4). |
| Camera shake is missing in After Effects | Expected. Bake the shake into the camera keys first (see 6.5). |
| An app's connector does nothing after installing | Restart the app once. Check that the bridge is enabled on its tab. |
| The tab says **Update available** | Click the update button on that tab and restart the app. |
| An import arrives twice or in a new folder | Use **Update** with the same job name to refresh a job. A new job name creates a new folder. |
| An imported animation looks simplified or wrong | Turn off **Optimize imported sequences** and send again. |
| A send is refused with the name of a limit | Raise that limit on the Settings page (see section 14). |
| A character was left out of a send | Expected in version 1. Characters come in version 2. |
| A null jumped to the world origin after saving | Make sure you used **File → Save All** when the import went into a sublevel. |

Logs are added to, never overwritten. Send the relevant one with any report.

---

## 17. Known limitations

- **Windows only.**
- **After Effects is one way** (Unreal → After Effects).
- **UELink never opens your apps for you.** You open them; UELink handles the transfer.
- **Characters** (rigged / skinned meshes) are coming in version 2.
- **Particles** are not transferred from any app.
- **Camera shake** is not exported (see 6.5).
- **Export scale** applies to After Effects only. The 3D apps always transfer at real size.
- **GPU vertex cache** imports are not included in packaged games. Use **Baked** for geometry a
  packaged game needs.
- The **3ds Max → Unreal Datasmith** route needs Epic's free Datasmith Exporter for 3ds Max.

---

## 18. Uninstalling

Uninstall UELink from the **Epic Games Launcher** (Library → UELink → Uninstall). For a completely
clean removal, also delete:

- The After Effects extension: `%APPDATA%\Adobe\CEP\extensions\com.uelink.listener\`
- Each app's connector, in that app's user plugin / add-on folder. For Houdini: delete
  `python\uelink_connector.py` and `packages\UELink.json`, and remove the block between
  `# BEGIN UELINK HOUDINI` and `# END UELINK HOUDINI` in both `scripts\123.py` and
  `scripts\456.py` inside each `Documents\houdiniXX.X\` folder. Your original scripts were kept as
  `123.py.uelink.bak` / `456.py.uelink.bak`.
- The `PlayerDebugMode` values under `HKEY_CURRENT_USER\Software\Adobe\CSXS.9` … `CSXS.13`
  (see section 15).
- The exchange folder `%LOCALAPPDATA%\UELink\` and the log folder `%APPDATA%\UELink\`.

---

## 19. Third-party software and trademarks

UELink is a self-contained Unreal Engine plugin. It does not bundle, install or redistribute any
third-party application, SDK or runtime. It works with software you already own by reading and
writing standard files (Alembic, FBX, glTF, Datasmith, OpenVDB, USD, JSON, ExtendScript) through
Unreal's own engine plugins, and by installing a small text connector into each app's user folder.

Adobe and After Effects are trademarks of Adobe Inc. Cinema 4D is a trademark of Maxon Computer
GmbH. Blender is a trademark of the Blender Foundation. 3ds Max and Autodesk are trademarks of
Autodesk, Inc. Houdini and SideFX are trademarks of Side Effects Software Inc. Unreal and Unreal
Engine are trademarks of Epic Games, Inc. All product names are used for compatibility and
identification only. UELink is an independent product and is not affiliated with, sponsored by, or
endorsed by Adobe, Maxon, the Blender Foundation, Autodesk or SideFX.

---

## 20. Getting support

**Discord (fastest):** https://discord.gg/7eNVSDA6yS
**Email:** parulep1@gmail.com
**Video overview:** https://youtu.be/ErXj9IhNQIw

To get a quick answer, please include:

1. Your **Unreal Engine version** (5.7 or 5.8).
2. The **app and version** you were working with (for example After Effects 2025).
3. **What you did** and **what happened instead**.
4. The relevant **log file**:

| Problem area | Log |
|--------------|-----|
| After Effects delivery | `%APPDATA%\UELink\launcher.log` |
| After Effects script errors | `%APPDATA%\UELink\jsx.log` |
| Export came out without animation | `%APPDATA%\UELink\sampler.log` |
| Cinema 4D / Blender / 3ds Max / Houdini | `%LOCALAPPDATA%\UELink\exchange\<app>\*.log` |

Bug reports and feature requests are both welcome.

---

*UELink 1.0 — developed and maintained by Parul Sharma.*
