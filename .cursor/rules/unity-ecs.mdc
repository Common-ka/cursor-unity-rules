---
description: "DOTS, ECS, Job System, Burst, ISystem, IJobEntity"
alwaysApply: false
---

# Unity ECS & DOTS (Unity 6.2+)

## Core Principles

1.  **Prefer `ISystem` (struct)** over `SystemBase` (class) to avoid GC overhead.
2.  **Always use `[BurstCompile]`** for Systems and Jobs to unlock native performance.
3.  **Use `IJobEntity`** for component iteration; it handles chunking and multithreading automatically.
4.  **Follow Microsoft Naming Conventions**: Use **PascalCase** for public fields in Jobs and Components.

## Entity Component System

### 1. Component Data
```
using Unity.Entities;
using Unity.Mathematics;

public struct MovementSpeed : IComponentData
{
    public float Value;
}
```

### 2. System (ISystem + IJobEntity)
This is the modern standard. It separates data iteration (Job) from the system lifecycle (System).

```
using Unity.Entities;
using Unity.Transforms;
using Unity.Burst;
using Unity.Mathematics;

[BurstCompile]
partial struct MovementSystem : ISystem
{
    [BurstCompile]
    public void OnCreate(ref SystemState state)
    {
        // Prevents the system from running if no entities have MovementSpeed
        state.RequireForUpdate<MovementSpeed>();
    }

    [BurstCompile]
    public void OnUpdate(ref SystemState state)
    {
        // Pass delta time into the job
        float deltaTime = SystemAPI.Time.DeltaTime;

        // Schedule the job to run in parallel across available worker threads
        new MoveJob
        {
            DeltaTime = deltaTime
        }.ScheduleParallel();
    }
}

// The Source Generator automatically creates the optimized chunk iteration code for this job
[BurstCompile]
partial struct MoveJob : IJobEntity
{
    public float DeltaTime;

    // Use 'in' for read-only data to improve performance
    private void Execute(ref LocalTransform transform, in MovementSpeed speed)
    {
        transform.Position += new float3(0, speed.Value * DeltaTime, 0);
    }
}
```

## Job System (Native Arrays)

Use this pattern when processing raw memory (`NativeArray`) outside of the ECS framework.

```
using Unity.Jobs;
using Unity.Collections;
using Unity.Burst;
using Unity.Mathematics;

[BurstCompile]
public struct VelocityJob : IJobParallelFor
{
    // Microsoft Style: Public fields should be PascalCase
    public NativeArray<float3> Velocity;
    
    [ReadOnly] 
    public NativeArray<float3> Acceleration;
    
    public float DeltaTime;
    
    public void Execute(int index)
    {
        Velocity[index] += Acceleration[index] * DeltaTime;
    }
}
```