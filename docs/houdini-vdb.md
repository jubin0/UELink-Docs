# Houdini smoke and fire volumes

UELink sends static or animated OpenVDB volumes from Houdini to Unreal's native Sparse Volume
Textures and Heterogeneous Volume actors. This workflow targets smoke, fire, dust and fog.

## Send a volume

1. Enable the Houdini bridge in Unreal and install/update the Houdini connector. Restart Houdini
   after updating an already loaded connector.
2. In Houdini, connect **UELink Export** after the simulation's VDB output or cached File SOP.
   For ordinary Houdini volume primitives, add **Convert VDB** upstream first. For an export node
   in `/out`, select the output SOP in **Source Geometry**.
3. Select **VDB (smoke / fire volume)**, enter a unique **Job Name**, and choose the frame range.
   Equal start/end frames produce a static volume. Set the correct scene FPS before exporting.
   Set **Volume Precision** and **Volume Fields** for this cache — see
   [Import memory](#import-memory) below. They matter most on long or high-resolution sequences.
4. Press Send. UELink writes a separate directory for this revision and notifies Unreal only
   after every frame is complete. The output field points to the first file after success.
5. Choose the destination level in Unreal. Open the Level Sequence named after the job and scrub.
   Save the level to retain the volume actor and its sequence binding.

The output filename field chooses the **parent directory** for VDB. Each send creates a unique
`uelink_vdb_<jobid>` subdirectory containing numbered files. It does not overwrite an existing
simulation cache. Interrupted/failed exports are not published; their incomplete directory can
remain on disk for inspection.

## Required fields and materials

| Houdini grid | Purpose | Imported channel |
| --- | --- | --- |
| `density` | Required smoke extinction | Attributes A, red |
| `temperature` | Optional fire colour | Attributes B, red |
| `flame` | Optional emission strength | Attributes B, green |

**Connect upstream of Pyro Bake Volume.** A Pyro Bake Volume SOP *replaces* `density` /
`temperature` / `flame` with vec3 shading volumes named `scatter` and `emission`, for Karma/Mantra
rendering. Those cannot drive an Unreal volume, and renaming them does not help — they are colour
fields, not a fog density. Attach **UELink Export** straight after the Pyro solver, or after the
file cache of the solver's output. If you send baked output, UELink refuses it and says so.
`flame` is what drives emission in Unreal: a solver that outputs only `density` and `temperature`
gives you smoke with no fire, so enable the solver's flame/burn field if you want emissive fire.

Fields must be scalar floating-point VDBs, and each of the three exported names must appear
exactly once. Other grids in the stream — velocity, colour, unnamed — are discarded and cannot
block a send, but two grids both named `density` are ambiguous and are refused.
Their transforms must match within
each frame; resample other grids to the density grid if needed. Keep the same exported grid names
and types throughout the sequence. Surface SDFs, packed geometry, meshes and point grids are not
supported by this volume export. Other grids, including velocity and colour, are omitted.

Unreal creates `M_UELinkVolume` and `MI_UELinkVolume` in the job's content folder. The instance
offers **Density**, **SmokeColor**, **FireIntensity**, **TemperatureScale** and **TemperatureOffset**.
TemperatureScale/Offset convert the simulation's temperature to the material's approximate
blackbody colour. Start by adjusting these to the solver's temperature convention; Houdini's
shader and render settings are not transferred. Emission requires a `flame` grid.

## Update and rendering

Select **Update** with the same Job Name to refresh the cache. UELink retains actor placement and
material settings, updates the job's frame keys, and handles shorter or longer frame ranges.
An assigned custom material must use the Volume domain and expose exactly one Sparse Volume
Texture parameter. UELink creates a local child instance so the shared material is not modified.

The component's **Playing** setting stays off because Sequencer controls its frame. New actors
enable **Issue Blocking Requests** so cinematic playback waits for volume data. Disable that
option for faster previews if desired; streaming may then show a coarser volume while catching up.

Use **UELink Update** to refresh exported simulations. Unreal's native right-click **Reimport**
has its own channel mapping dialog; its defaults may differ from the table above.

SVT revisions are retained in the job's `Volumes` folder to preserve references and recovery.
They can occupy substantial disk space. Remove unused revisions through Unreal's Content Browser
after saving your project and checking references; deleting source VDBs also removes the ability
to reimport those revisions. Runtime playback uses imported Unreal assets, not the source files.

## Import memory

Unreal's volume importer holds **every uncooked frame of the sequence in memory at the same time**
while it builds the asset, and loads several source files concurrently on top of that. Peak RAM is
therefore a multiple of the cache on disk, not of one frame — a ~20 GB cache has been observed to
peak near 90 GB. Three levers reduce it; the first two are on the UELink Export node.

**Volume Precision** sets the bytes stored per voxel, and peak import memory scales directly with it.

| Setting | Per voxel | Use when |
| --- | --- | --- |
| 8-bit | 1 byte | Every exported field stays within 0..1. Halves the memory of 16-bit. |
| 16-bit (default) | 2 bytes | Almost every pyro cache. Safe for values beyond 1.0. |
| 32-bit | 4 bytes | Only when 16-bit precision is visibly insufficient. |

8-bit stores values from 0 to 1 and **clamps** anything above it, with no rescaling — so a field
peaking at, say, 23.9 would flatten to solid white. UELink measures each exported grid as it writes
and **refuses an 8-bit send that would clamp**, naming the field, the frame and the measured value,
rather than producing a damaged volume. If you see that refusal, either switch to 16-bit or
normalise the field in Houdini first.

**Volume Fields** set to *Smoke only* sends `density` alone. Unreal then leaves its second attribute
texture unmapped and skips allocating it entirely, cutting both the cache on disk and the import
memory. The volume cannot emit fire in that mode.

**Fewer frames per job** is the third lever, and the only one that helps when a single frame is
already large: peak memory is roughly linear in frame count, so two 25-frame jobs peak at about half
of one 50-frame job. Sends over 4 GB raise a warning on the export node saying so.

## Limits

- Maximum 10,000 frames per transfer and 2 GB per source frame. These checks are not a guarantee
  of sufficient import RAM; large/high-resolution caches can use much more memory when expanded.
- Selected field values must be finite and within half-float range (-65504 to 65504). Frames whose
  grids are empty — the lead-in of a simulation before it ignites — are exported as empty frames,
  not refused.
- No live simulation streaming, liquid surface meshing, velocity-based motion blur, or automatic
  Houdini shader matching.
- UE 5.8 is the validated target for this addition. Real-time performance depends on resolution,
  active voxels and storage bandwidth. Epic labels its native volume feature Experimental.

Engine background: [Sparse Volume Textures](https://dev.epicgames.com/documentation/en-us/unreal-engine/sparse-volume-textures-in-unreal-engine)
and [Heterogeneous Volumes](https://dev.epicgames.com/documentation/unreal-engine/heterogeneous-volumes-in-unreal-engine).
