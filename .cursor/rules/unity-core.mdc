---
alwaysApply: true
---


# Unity Core Rules - C# & MonoBehaviour

## Naming Conventions for Unity 6.2

### Classes and Structs
```
// ✅ DO: PascalCase
public class PlayerController : MonoBehaviour { }

// ✅ DO: Readonly structs for data integrity
public readonly struct GameConfig { }

public interface IHealthSystem { }

// ❌ DON'T
public class player_controller { } // snake_case
public class playerController { } // camelCase
public struct MutableConfig { } // Avoid mutable structs
```

### Fields and Properties
```
public class Example : MonoBehaviour
{
    // ✅ DO: Private fields with _ prefix (.NET Style)
    [SerializeField] private float _moveSpeed = 5f;
    private Transform _targetTransform;
    
    // ✅ DO: Public properties in PascalCase
    public float MoveSpeed => _moveSpeed;
    public bool IsMoving { get; private set; }
    
    // ✅ DO: Constants in PascalCase (Microsoft Standard)
    private const float MaxHealth = 100f;
    private const string PlayerTag = "Player";
    
    // ✅ DO: Static fields with _ prefix
    private static int _instanceCount = 0;
    
    // ✅ DO: Booleans with is/has/can prefix
    private bool _isGrounded;
    private bool _hasWeapon;
    private bool _canJump;
    
    // ❌ DON'T: Public fields without [SerializeField]
    public float moveSpeed; // Use property or [SerializeField] private
    
    // ❌ DON'T: Hungarian notation
    private float m_Speed; 
    private float fSpeed;
}
```

### Methods and Events
```
public class EventExample : MonoBehaviour
{
    // ✅ DO: Methods in PascalCase
    public void ProcessInput() { }
    private void HandleCollision() { }
    
    // ✅ DO: Events with On prefix
    public event Action OnPlayerDeath;
    public event Action<int> OnScoreChanged;
    
    // ✅ DO: Async methods with Async suffix (Use Awaitable for Unity 6.2)
    private async Awaitable LoadDataAsync() { }
    public async Awaitable<bool> TryConnectAsync() { }
}
```

## MonoBehaviour Lifecycle

### Correct Method Order
```
public class LifecycleExample : MonoBehaviour
{
    // 1. Serialized Fields
    [SerializeField] private float _speed = 5f;
    
    // 2. Private Fields
    private Rigidbody _rigidbody;
    private bool _isInitialized;
    
    // 3. Properties
    public bool IsInitialized => _isInitialized;
    
    // 4. Unity Lifecycle Methods (in call order)
    private void Awake()
    {
        // Initialize components on this object
        // Use TryGetComponent to avoid implicit allocation if missing
        if (!TryGetComponent(out _rigidbody))
        {
            Debug.LogError("Rigidbody missing!");
        }
    }
    
    private void OnEnable()
    {
        // Subscribe to events
        GameEvents.OnLevelStart += HandleLevelStart;
    }
    
    private void Start()
    {
        // Initialization after all Awake calls
        _isInitialized = true;
    }
    
    private void FixedUpdate()
    {
        // Physics
        if (_isInitialized)
        {
            ApplyPhysics();
        }
    }
    
    private void Update()
    {
        // Game logic and input
        ProcessInput();
    }
    
    private void LateUpdate()
    {
        // Called after all Update calls (e.g., camera)
    }
    
    private void OnDisable()
    {
        // Unsubscribe from events
        GameEvents.OnLevelStart -= HandleLevelStart;
    }
    
    private void OnDestroy()
    {
        // Cleanup
        CleanupResources();
    }
    
    // 5. Custom Methods
    private void ProcessInput() { }
    private void ApplyPhysics() { }
    private void HandleLevelStart() { }
    private void CleanupResources() { }
}
```

## Serialization

### [SerializeField] Best Practices
```
public class SerializationExample : MonoBehaviour
{
    // ✅ DO: Private fields with [SerializeField] and _ prefix
    [SerializeField] private float _health = 100f;
    [SerializeField] private GameObject _weaponPrefab;
    
    // ✅ DO: Headers for grouping
    [Header("Movement Settings")]
    [SerializeField] private float _moveSpeed = 5f;
    [SerializeField] private float _jumpForce = 10f;
    
    [Header("Audio")]
    [SerializeField] private AudioClip _jumpSound;
    [SerializeField] private AudioClip _landSound;
    
    // ✅ DO: Tooltip for inspector documentation
    [Tooltip("Maximum speed the player can reach")]
    [SerializeField] private float _maxSpeed = 20f;
    
    // ✅ DO: Range to limit values
    [Range(0f, 1f)]
    [SerializeField] private float _volume = 0.5f;
    
    // ✅ DO: HideInInspector to hide runtime values
    [HideInInspector]
    public int RuntimeValue; // Used by systems but not editable
}
```

### ScriptableObject for Configuration
```
// ✅ DO: Configuration via ScriptableObject
[CreateAssetMenu(fileName = "WeaponConfig", menuName = "Game/Weapon Config")]
public class WeaponConfig : ScriptableObject
{
    [Header("Stats")]
    [SerializeField] private int _damage = 10;
    [SerializeField] private float _fireRate = 0.5f;
    
    [Header("Visuals")]
    [SerializeField] private GameObject _modelPrefab;
    [SerializeField] private ParticleSystem _muzzleFlash;
    
    public int Damage => _damage;
    public float FireRate => _fireRate;
    public GameObject ModelPrefab => _modelPrefab;
}

public class Weapon : MonoBehaviour
{
    [SerializeField] private WeaponConfig _config;
    
    public void Fire()
    {
        // Using the configuration
        DealDamage(_config.Damage);
    }
}
```

## Namespaces

```
// ✅ DO: Use namespaces
namespace MyGame.Core
{
    public class GameManager : MonoBehaviour
    {
    }
}

namespace MyGame.Gameplay.Player
{
    public class PlayerController : MonoBehaviour
    {
    }
}

namespace MyGame.UI
{
    public class MainMenuUI : MonoBehaviour
    {
    }
}
```

## XML Documentation

```
/// <summary>
/// Controls player movement and actions.
/// </summary>
public class PlayerController : MonoBehaviour
{
    /// <summary>
    /// Current movement speed of the player.
/// </summary>
    [SerializeField] private float _moveSpeed = 5f;
    
    /// <summary>
    /// Moves the player in the specified direction.
/// </summary>
    /// <param name="direction">Movement direction (normalized vector).</param>
    /// <param name="deltaTime">Time since last frame.</param>
    public void Move(Vector3 direction, float deltaTime)
    {
        transform.position += direction * _moveSpeed * deltaTime;
    }
    
    /// <summary>
    /// Checks if the player can jump.
/// </summary>
    /// <returns>True if the player can jump.</returns>
    public bool CanJump()
    {
        return IsGrounded() && !IsJumping;
    }
}
```

## Modern C# Features (Unity 6.2 / C# 12.0)

```
// ✅ DO: Pattern matching
public void HandleInput(InputAction.CallbackContext context)
{
    switch (context.phase)
    {
        case InputActionPhase.Started:
            OnInputStarted();
            break;
        case InputActionPhase.Performed:
            OnInputPerformed();
            break;
        case InputActionPhase.Canceled:
            OnInputCanceled();
            break;
    }
}

// ✅ DO: Null-conditional operator
private void SafeInvoke()
{
    OnPlayerDeath?.Invoke();
}

// ✅ DO: Expression-bodied members
public bool IsAlive => _health > 0;
public float HealthPercentage => _health / MaxHealth;

// ✅ DO: String interpolation
private void LogStatus()
{
    Debug.Log($"Player Health: {_health}/{MaxHealth}");
}

// ✅ DO: Unity Awaitable (Replaces Task)
private async Awaitable PerformActionAsync()
{
    await Awaitable.NextFrameAsync();
    _health += 10;
}
```

