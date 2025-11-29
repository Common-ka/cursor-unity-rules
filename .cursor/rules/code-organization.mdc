---
alwaysApply: true
---

# Code Organization

## Project Folder Structure

```
Assets/
├── !_Project/                   # Main project content (! keeps it at top)
│   ├── Art/
│   │   ├── Materials/
│   │   ├── Models/
│   │   ├── Textures/
│   │   └── Animations/
│   ├── Audio/
│   │   ├── Music/
│   │   ├── SFX/
│   │   └── Mixers/
│   ├── Prefabs/
│   │   ├── Characters/
│   │   ├── Environment/
│   │   ├── UI/
│   │   └── Effects/
│   ├── Scenes/
│   │   ├── Levels/
│   │   ├── Menus/
│   │   └── Test/
│   ├── Scripts/
│   │   ├── Runtime/           # Runtime code
│   │   │   ├── Core/          # Base systems
│   │   │   ├── Gameplay/      # Game logic
│   │   │   ├── UI/            # UI controllers
│   │   │   ├── Utilities/     # Helper classes
│   │   │   ├── DOTS/          # ECS implementation
│   │   │   │   ├── Components/    # IComponentData
│   │   │   │   ├── Systems/       # SystemBase/ISystem
│   │   │   │   └── Aspects/       # Aspects
│   │   │   └── Hybrid/        # Mixed MonoBehaviour + DOTS
│   │   ├── Editor/            # Editor scripts
│   │   └── Tests/             # Test assembly scripts
│   ├── ScriptableObjects/
│   │   ├── GameConfig/        # Game settings
│   │   ├── EventChannels/     # SO-based events
│   │   ├── Variables/         # Runtime shared data
│   │   ├── Sets/              # SO Sets for collections
│   │   └── Stats/             # Character/weapon data
│   └── Settings/              # Project settings
├── Packages/                  # Custom packages
│   └── com.yourcompany.core/  # Reusable modules
│       ├── Runtime/
│       ├── Editor/
│       ├── Tests/
│       └── Documentation~/
├── AddressableAssets/         # Addressables content
│   ├── Groups/                # Addressable Groups configs
│   ├── Settings/              # Addressable Settings
│   └── Content/               # Organized by strategy
│       ├── PerLevel/          # Concurrent usage strategy
│       ├── Characters/        # Logical entity strategy
│       └── Shared/            # Shared resources
├── Plugins/                   # Third-party plugins
├── Tests/                     # Test assemblies
│   ├── EditMode/
│   │   ├── Unit/              # Unit tests
│   │   └── Integration/       # Integration tests
│   ├── PlayMode/
│   │   ├── Functional/        # Functional tests
│   │   └── Performance/       # Profiling tests
│   └── TestUtilities/         # Common mocks and helpers
└── TextMesh Pro/              # TMP resources
```

## Asset Naming Conventions

### Modern Naming (No Hungarian Prefixes)
Organization through folders, not prefixes. Use PascalCase for all asset names.

### Prefabs
- Characters: `PlayerKnight.prefab` in `Prefabs/Characters/`
- Environment: `TreeOak.prefab` in `Prefabs/Environment/`
- UI: `MainMenu.prefab` in `Prefabs/UI/`
- Effects: `Explosion.prefab` in `Prefabs/Effects/VFX/`

### Scripts
Follow Microsoft C# conventions:

#### Classes and Methods
```
// PascalCase for classes, methods, properties, and public fields
public class GameManager { }
public class AudioManager { }
public class PlayerController { }
public void Initialize() { }
public int MaxHealth { get; set; }
```

#### Fields and Variables
```
// Private instance fields: _camelCase
private int _currentHealth;
private Transform _targetTransform;

// Static fields: s_camelCase
private static int s_instanceCount;
private static GameManager s_instance;

// Public fields: PascalCase (prefer properties)
public int MaxHealth;

// Local variables and parameters: camelCase
public void SetHealth(int newHealth)
{
    int previousHealth = _currentHealth;
    _currentHealth = newHealth;
}

// Constants: PascalCase
private const int MaxPlayers = 4;
public const string GameVersion = "1.0.0";
```

#### Interfaces
```
// Prefix with 'I' + PascalCase
public interface IInteractable { }
public interface IDamageable { }
public interface IPoolable { }
```

### Scenes
- Levels: `Level01_Forest.unity`
- Menus: `MenuMain.unity`, `MenuSettings.unity`
- Test: `TestPlayerMovement.unity`

## Assembly Definition Organization

### Core Assemblies
```
Scripts/Runtime/Core/YourCompany.Core.asmdef
Scripts/Runtime/Gameplay/YourCompany.Gameplay.asmdef
Scripts/Runtime/UI/YourCompany.UI.asmdef
Scripts/Runtime/DOTS/YourCompany.DOTS.asmdef
Scripts/Editor/YourCompany.Editor.asmdef
Scripts/Tests/YourCompany.Tests.asmdef
```

### Example Assembly Definition
```
{
    "name": "YourCompany.Core",
    "rootNamespace": "YourCompany.Core",
    "references": [],
    "includePlatforms": [],
    "excludePlatforms": [],
    "allowUnsafeCode": false,
    "overrideReferences": false,
    "precompiledReferences": [],
    "autoReferenced": true,
    "defineConstraints": [],
    "versionDefines": [],
    "noEngineReferences": false
}
```

## Scene Hierarchy Organization

```
Hierarchy:
--- SYSTEMS ---              # Persistent (DontDestroyOnLoad)
├── GameManager
├── AudioManager
├── InputManager
└── UIManager
--- LEVEL ---                # Level content
├── Environment
│   ├── Terrain
│   ├── Props
│   └── Lighting
├── Gameplay
│   ├── SpawnPoints
│   └── Triggers
└── UI
    ├── Canvas_HUD
    └── Canvas_Menus
--- DYNAMIC ---              # Runtime spawned objects
```

## Prefab Structure

```
Player
├── Model                    # Visual model
├── Colliders               # Physics colliders
├── Effects                 # Particles, effects
└── Audio                   # Audio sources
```

## DOTS/ECS Structure

### Component Example
```
// Components/HealthComponent.cs
using Unity.Entities;

namespace YourCompany.DOTS.Components
{
    public struct HealthComponent : IComponentData
    {
        public float CurrentHealth;
        public float MaxHealth;
    }
}
```

### System Example
```
// Systems/HealthSystem.cs
using Unity.Entities;
using Unity.Burst;

namespace YourCompany.DOTS.Systems
{
    [BurstCompile]
    public partial struct HealthSystem : ISystem
    {
        [BurstCompile]
        public void OnUpdate(ref SystemState state)
        {
            // System logic
        }
    }
}
```

### Aspect Example
```
// Aspects/CharacterAspect.cs
using Unity.Entities;
using Unity.Transforms;

namespace YourCompany.DOTS.Aspects
{
    public readonly partial struct CharacterAspect : IAspect
    {
        readonly RefRW<LocalTransform> _transform;
        readonly RefRW<HealthComponent> _health;
    }
}
```

## Best Practices

### Naming
- Use PascalCase for all public identifiers (classes, methods, properties)
- Use _camelCase for private instance fields
- Use s_camelCase for static fields
- Use camelCase for local variables and parameters
- Use meaningful, descriptive names - prefer clarity over brevity
- Avoid abbreviations except widely known ones (UI, FX, AI)
- No Hungarian notation or type prefixes

### Organization
- Organize assets by type through folder structure, not prefixes
- Use Assembly Definitions to reduce compilation time
- Separate Runtime and Editor code
- Group DOTS code separately from MonoBehaviour code
- Use Addressables for content management with clear grouping strategy

### Architecture
- Use ScriptableObject-based architecture for events and shared data
- Implement EventChannel pattern for decoupled communication
- Use Assembly Definitions to enforce architectural boundaries
- Separate concerns: Core, Gameplay, UI, Utilities
- Create custom packages for reusable systems

### Testing
- Organize tests by type: Unit, Integration, Functional, Performance
- Use separate assemblies for test code
- Create TestUtilities assembly for shared test helpers
- Write tests for critical gameplay systems