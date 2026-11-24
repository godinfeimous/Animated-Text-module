# Memory management & leak prevention

[Home](../README.md) · [Performance](performance.md) · [API](api.md) · [Custom effects](custom-effects.md)

AnimatedText is designed to reuse animation resources and clean up text objects when their lifecycle ends. This guide explains the documented cleanup behavior, integration responsibilities and a practical way to investigate memory growth.

> [!NOTE]
> Pooling and caching intentionally retain reusable resources. A higher memory reading after warm-up is not, by itself, a memory leak. The procedures below are recommended validation tests, not a claim that the module has passed every stress test or is guaranteed leak-free.

## Contents

- [Resource ownership](#resource-ownership)
- [Stop, reset, pause or destroy?](#stop-reset-pause-or-destroy)
- [Reuse instead of recreating](#reuse-instead-of-recreating)
- [Event connections and tasks](#event-connections-and-tasks)
- [Custom effects and external resources](#custom-effects-and-external-resources)
- [Pools and caches](#pools-and-caches)
- [Stress test](#stress-test)
- [Interpreting results](#interpreting-results)
- [Release checklist](#release-checklist)

## Resource ownership

| Resource | Owner / cleanup responsibility |
| --- | --- |
| AnimatedTextCanvas and generated glyphs | AnimatedText text object |
| Internal animation playback and connections | AnimatedText text object |
| Original TextLabel, TextButton or TextBox | Your UI |
| Connections to external services or buttons | Your integration code |
| Spawned tasks and custom instances | Code that creates them |
| Shared glyph pools and measurement cache | Module-level infrastructure |

The documented Destroy method stops animations, disconnects internal connections, removes generated text and restores the original label display and stroke. Destroying the label also destroys its associated AnimatedText object automatically. Keep a clear owner for your text object, and do not retain obsolete handles or references after teardown.

## Stop, reset, pause or destroy?

| API | Use when |
| --- | --- |
| Handle:Stop(fade?) | One animation is no longer needed |
| Text:StopAll(fade?) | All current animations should stop |
| Text:Pause() / Resume() | A retained UI is temporarily hidden or inactive |
| Text:Reset() | Stop animations and restore the base character state |
| Text:Destroy() | The animated text object is no longer needed |

Stopping and pausing are not substitutes for destroying an unused text object. Pausing preserves playback state. Infinite effects such as Wave and Rainbow require an explicit stop or duration if they should end. Held effects intentionally preserve their final visual state; clear them when they are no longer useful.

## Reuse instead of recreating

Create one text object per persistent label. Replace its content when needed.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Text = AT.new(script.Parent)

local function ShowMessage(Message: string)
    Text:StopAll()
    Text:Reset()
    Text:SetText(Message)
    Text:Play("Typewriter", { CharactersPerSecond = 35 })
end

ShowMessage("Welcome!")
```

Avoid constructing a new AnimatedText object every time a button is clicked, a counter changes or a new subtitle appears. Avoid accumulating handles in a table unless you need them for playback control. When replacing an independently running sequence, stop the previous sequence before starting a new one.

## Event connections and tasks

AnimatedText cleans up its own lifecycle resources, but your integration must disconnect events it subscribes to on persistent objects and services. In particular, a connection to RunService or a RemoteEvent can outlive a removed UI and retain a closure referencing it.

Example: a ModuleScript controller used by a LocalScript. The caller owns the returned cleanup function and must call it when closing the feature.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)

local function Mount(Label: TextLabel, Event: RemoteEvent)
    local Text = AT.new(Label)
    local Closed = false

    local Connection = Event.OnClientEvent:Connect(function(Message)
        if Closed or typeof(Message) ~= "string" then
            return
        end

        Text:StopAll()
        Text:SetText(Message)
        Text:Play("Typewriter")
    end)

    local DestroyingConnection

    local function Cleanup()
        if Closed then
            return
        end

        Closed = true
        Connection:Disconnect()
        if DestroyingConnection then
            DestroyingConnection:Disconnect()
        end
        Text:Destroy()
    end

    DestroyingConnection = Label.Destroying:Connect(function()
        if Closed then
            return
        end
        Closed = true
        Connection:Disconnect()
        DestroyingConnection:Disconnect()
        -- AnimatedText handles automatic cleanup when its label is destroyed.
    end)

    return Cleanup
end

return Mount
```

Call Cleanup before destroying or replacing a mounted UI when possible. Cancel delayed tasks you own, or use a lifecycle token so stale callbacks cannot access a destroyed text object. Do not connect an event inside a per-frame effect update.

## Custom effects and external resources

Custom effects should write to Context.State rather than create new GUI instances on every frame. Use Context.Data for animation-scoped data and Context.TargetData for target-scoped data. The engine provides lifecycle callbacks including Cleanup(Context), as documented in the [custom effect tutorial](custom-effects.md).

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)

local Drift = {
    DefaultConfig = { Speed = 2, Distance = 6 },
}

function Drift:Update(Context)
    local Data = Context.TargetData

    if Data.Phase == nil then
        Data.Phase = Context.Random:NextNumber(0, math.pi * 2)
    end

    local Offset = math.sin(Context.Time * Context.Config.Speed + Data.Phase)
    Context.State.PositionOffset = Vector2.new(0, Offset * Context.Config.Distance)
end

AT.RegisterEffect("MemoryFriendlyDrift", Drift)
```

This example creates no external instances, connections or background tasks. If an advanced effect does allocate its own resources, retain their references and release them in Cleanup. Make cleanup safe to call after partial initialization. Prefer existing decoration helpers when they fit the desired effect. Register reusable effects once, not on every playback.

## Pools and caches

AnimatedText documents a shared scheduler, pooled glyphs and slices, and cached measurements. The configuration reference lists PoolMaxSize and MeasureCacheSize. These are tuning knobs, not total client memory limits.

- Prewarm before a large cinematic rather than during a critical transition.
- Avoid unnecessarily high slice counts.
- Reuse common fonts and text styles where practical.
- Do not expect Destroy to empty every shared pool or cache.
- Compare memory after equivalent workloads, rather than comparing a cold start with a fully warmed scene.

## Stress test

Run the following diagnostic LocalScript in StarterPlayerScripts in a dedicated test place. It creates temporary labels, layers two continuous effects, destroys the objects, and logs client memory after each batch. It also exercises automatic label-destruction cleanup on alternating iterations.

```lua
local AT = require(game.ReplicatedStorage.AnimatedText)
local Players = game:GetService("Players")
local Stats = game:GetService("Stats")

local Gui = Instance.new("ScreenGui")
Gui.Name = "AnimatedTextStressTest"
Gui.Parent = Players.LocalPlayer:WaitForChild("PlayerGui")

local Batches = 10
local LabelsPerBatch = 20

local function Sample(Name: string)
    local Metrics = AT.GetStats()
    print(string.format(
        "[%s] Client memory: %.2f MB | Lua heap: %.2f KB | Active text objects: %s",
        Name,
        Stats:GetTotalMemoryUsageMb(),
        gcinfo(),
        tostring(Metrics.Texts)
    ))
    print("Animation metrics:", Metrics)
end

local function RunBatch()
    local Entries = {}

    for Index = 1, LabelsPerBatch do
        local Label = Instance.new("TextLabel")
        Label.Size = UDim2.fromOffset(320, 24)
        Label.Position = UDim2.fromOffset(0, (Index - 1) * 24)
        Label.Text = "AnimatedText stress test " .. Index
        Label.Parent = Gui

        local Text = AT.new(Label)
        Text:Play("Wave", { Amplitude = 4 })
        Text:Play("Rainbow")
        Entries[Index] = { Label = Label, Text = Text }
    end

    task.wait(0.5)

    for Index, Entry in ipairs(Entries) do
        if Index % 2 == 0 then
            Entry.Text:Destroy()
        end
        Entry.Label:Destroy()
    end

    table.clear(Entries)
    task.wait(1)
end

RunBatch()
task.wait(3)
Sample("Warm baseline")

for Batch = 1, Batches do
    RunBatch()
    Sample("Batch " .. Batch)
end

Gui:Destroy()
task.wait(5)
Sample("Final")
```

This test uses real client allocations and waits for cleanup to settle, but cannot force garbage collection or isolate every Roblox subsystem. Increase the batch count gradually, and run separate tests for slices, audio, rich text, repeated SetText, and any custom effects your game uses. Do not ship the stress-test script in production.

## Interpreting results

Use the Developer Console (F9) memory view to inspect client categories, and the Studio MicroProfiler to inspect frame-time spikes. Compare the same scene and client configuration over repeated runs.

| Observation | Interpretation |
| --- | --- |
| Memory grows initially then fluctuates around a stable range | Consistent with warm-up, caching and garbage collection |
| Active text count remains above its original baseline | Investigate retained text objects and cleanup paths |
| Memory increases across equivalent batches without settling | Investigate a possible leak; isolate the effect or integration |
| Frame time rises while active text count stays constant | Investigate per-frame work, rendering and other systems |
| Memory remains elevated after teardown | Could be pools, assets or delayed collection; compare longer runs |

Do not rely on a single heap measurement. Total client memory includes unrelated systems; gcinfo measures the Luau heap rather than all engine instances. Inspect several metrics and look for repeatable trends.

## Release checklist

- [ ] Reuse persistent text objects.
- [ ] Stop obsolete infinite effects and sequences.
- [ ] Destroy temporary text objects and their owner UI.
- [ ] Disconnect external events and cancel obsolete tasks.
- [ ] Release custom effect resources during cleanup.
- [ ] Verify repeated mounting and unmounting.
- [ ] Check animation metrics, client memory and frame time.
- [ ] Test the actual target devices and representative text lengths.

[Back to performance](performance.md) · [Back to documentation](../README.md)
