---
description: "Guidelines for working with the New Input System in Unity 6.2"
globs: ["**/Input*.cs", "**/Player*.cs", "**/*Controller*.cs"]
alwaysApply: false
---

# Unity Input System Rules

> Note: All private fields in the examples use camelCase names with a leading underscore, following common Microsoft C# naming conventions for fields.[web:17]

## REQUIRED: New Input System

⚠️ **Legacy Input System is FORBIDDEN for new projects**

### Package installation

```
Package Manager → Input System → Install
```

In Project Settings → Player → Active Input Handling:
- Select **"Input System Package (New)"**

## Input Actions Asset

### Creating Input Actions

```
// ✅ DO: Create an Input Actions Asset
// Assets → Create → Input Actions
```

### Input Actions structure

```
PlayerInputActions
├── Gameplay
│   ├── Move (Value, Vector2)
│   ├── Look (Value, Vector2)
│   ├── Jump (Button)
│   ├── Fire (Button)
│   └── Interact (Button)
├── UI
│   ├── Navigate (Value, Vector2)
│   ├── Submit (Button)
│   └── Cancel (Button)
└── Menu
    ├── Pause (Button)
    └── OpenInventory (Button)
```

## PlayerInput Component Approach

### Automatic wiring via PlayerInput

```
using UnityEngine;
using UnityEngine.InputSystem;

/// <summary>
/// Handles player input via the PlayerInput component.
/// </summary>
public class PlayerInputHandler : MonoBehaviour
{
    [Header("References")]
    [SerializeField] private PlayerInput _playerInput;

    private Vector2 _moveInput;
    private Vector2 _lookInput;
    private bool _jumpPressed;

    private void Awake()
    {
        if (_playerInput == null)
        {
            _playerInput = GetComponent<PlayerInput>();
            if (_playerInput == null)
            {
                Debug.LogError("PlayerInput component is missing on PlayerInputHandler.");
                enabled = false;
            }
        }
    }

    // ✅ DO: Use message methods (automatically called by PlayerInput)
    // NOTE: Prefer InputAction.CallbackContext over InputValue
    public void OnMove(InputAction.CallbackContext context)
    {
        _moveInput = context.ReadValue<Vector2>();
    }

    public void OnLook(InputAction.CallbackContext context)
    {
        _lookInput = context.ReadValue<Vector2>();
    }

    public void OnJump(InputAction.CallbackContext context)
    {
        _jumpPressed = context.ReadValueAsButton();

        if (_jumpPressed)
        {
            PerformJump();
        }
    }

    public void OnFire(InputAction.CallbackContext context)
    {
        if (context.performed)
        {
            PerformFire();
        }
    }

    private void Update()
    {
        // Use cached values for continuous input
        ProcessMovement(_moveInput);
        ProcessLook(_lookInput);

        // Example: reset one-frame jump flag if it is meant to be transient
        _jumpPressed = false;
    }

    private void ProcessMovement(Vector2 input)
    {
        // Movement logic
    }

    private void ProcessLook(Vector2 input)
    {
        // Look logic
    }

    private void PerformJump()
    {
        // Jump logic
    }

    private void PerformFire()
    {
        // Fire logic
    }
}
```

## Manual Input Actions Approach

### Working directly with InputActionAsset

```
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.InputSystem.InputActionRebindingExtensions;

/// <summary>
/// Controls input via direct references to Input Actions.
/// </summary>
public class ManualInputController : MonoBehaviour
{
    [Header("Input Actions")]
    [SerializeField] private InputActionAsset _inputActions;

    private InputActionMap _gameplayActionMap;
    private InputAction _moveAction;
    private InputAction _jumpAction;
    private InputAction _fireAction;

    private void Awake()
    {
        if (_inputActions == null)
        {
            Debug.LogError("InputActionAsset reference is missing on ManualInputController.");
            enabled = false;
            return;
        }

        // ✅ DO: Retrieve actions from the asset
        _gameplayActionMap = _inputActions.FindActionMap("Gameplay", throwIfNotFound: true);
        _moveAction = _gameplayActionMap.FindAction("Move", throwIfNotFound: true);
        _jumpAction = _gameplayActionMap.FindAction("Jump", throwIfNotFound: true);
        _fireAction = _gameplayActionMap.FindAction("Fire", throwIfNotFound: true);
    }

    private void OnEnable()
    {
        // ✅ DO: Subscribe to events
        _jumpAction.performed += OnJumpPerformed;
        _jumpAction.canceled += OnJumpCanceled;
        _fireAction.performed += OnFirePerformed;

        // ✅ DO: Prefer enabling the entire action map
        _gameplayActionMap.Enable();
    }

    private void OnDisable()
    {
        // ✅ DO: ALWAYS unsubscribe and disable
        _jumpAction.performed -= OnJumpPerformed;
        _jumpAction.canceled -= OnJumpCanceled;
        _fireAction.performed -= OnFirePerformed;

        _gameplayActionMap.Disable();
    }

    private void Update()
    {
        // ✅ DO: Read values in Update for continuous input
        Vector2 moveInput = _moveAction.ReadValue<Vector2>();
        ProcessMovement(moveInput);
    }

    private void OnJumpPerformed(InputAction.CallbackContext context)
    {
        Debug.Log("Jump performed!");
        PerformJump();
    }

    private void OnJumpCanceled(InputAction.CallbackContext context)
    {
        Debug.Log("Jump released!");
    }

    private void OnFirePerformed(InputAction.CallbackContext context)
    {
        PerformFire();
    }

    private void ProcessMovement(Vector2 input)
    {
        // Movement logic
    }

    private void PerformJump()
    {
        // Jump logic
    }

    private void PerformFire()
    {
        // Fire logic
    }
}
```

## Generated C# Class Approach (RECOMMENDED)

### Generating the C# class

```
In the Input Actions Asset:
Inspector → Generate C# Class
Namespace: MyGame.Input
Class Name: PlayerInputActions
```

### Using the generated class

```
using UnityEngine;
using UnityEngine.InputSystem;
using MyGame.Input;

/// <summary>
/// Handles input using the generated C# class (preferred approach).
/// </summary>
public class ModernInputController : MonoBehaviour
{
    private PlayerInputActions _inputActions;

    // ✅ DO: Expose read-only properties for input access
    public Vector2 MoveInput { get; private set; }
    public Vector2 LookInput { get; private set; }
    public bool IsJumping { get; private set; }

    private void Awake()
    {
        // ✅ DO: Create a single instance
        _inputActions = new PlayerInputActions();
    }

    private void OnEnable()
    {
        if (_inputActions == null)
        {
            _inputActions = new PlayerInputActions();
        }

        // ✅ DO: Subscribe using explicit handlers (avoid lambdas to prevent duplicate subscriptions)
        _inputActions.Gameplay.Jump.performed += OnJump;
        _inputActions.Gameplay.Fire.performed += OnFire;

        // ✅ DO: Enable the whole action map
        _inputActions.Gameplay.Enable();
    }

    private void OnDisable()
    {
        if (_inputActions == null)
        {
            return;
        }

        // ✅ DO: Unsubscribe from events on disable
        _inputActions.Gameplay.Jump.performed -= OnJump;
        _inputActions.Gameplay.Fire.performed -= OnFire;

        _inputActions.Gameplay.Disable();
    }

    private void Update()
    {
        // ✅ DO: Read continuous input each frame
        MoveInput = _inputActions.Gameplay.Move.ReadValue<Vector2>();
        LookInput = _inputActions.Gameplay.Look.ReadValue<Vector2>();

        ProcessMovement(MoveInput);
        ProcessLook(LookInput);

        // Example: reset one-frame flag after processing
        IsJumping = false;
    }

    private void OnJump(InputAction.CallbackContext context)
    {
        if (!context.performed)
        {
            return;
        }

        IsJumping = true;
        PerformJump();
    }

    private void OnFire(InputAction.CallbackContext context)
    {
        if (!context.performed)
        {
            return;
        }

        PerformFire();
    }

    private void ProcessMovement(Vector2 input)
    {
        // Movement logic
    }

    private void ProcessLook(Vector2 input)
    {
        // Look logic
    }

    private void PerformJump()
    {
        // Jump logic
    }

    private void PerformFire()
    {
        // Fire logic
    }
}
```

## Multiplayer Input

### PlayerInputManager for local multiplayer

```
using UnityEngine;
using UnityEngine.InputSystem;

/// <summary>
/// Manages multiple players (split-screen, local co-op).
/// </summary>
[RequireComponent(typeof(PlayerInputManager))]
public class LocalMultiplayerManager : MonoBehaviour
{
    private PlayerInputManager _playerInputManager;

    private void Awake()
    {
        _playerInputManager = GetComponent<PlayerInputManager>();

        if (_playerInputManager == null)
        {
            Debug.LogError("PlayerInputManager component is missing on LocalMultiplayerManager.");
            enabled = false;
        }
    }

    private void OnEnable()
    {
        if (_playerInputManager == null)
        {
            return;
        }

        // ✅ DO: Set up callbacks in OnEnable
        _playerInputManager.onPlayerJoined += OnPlayerJoined;
        _playerInputManager.onPlayerLeft += OnPlayerLeft;
    }

    private void OnDisable()
    {
        if (_playerInputManager == null)
        {
            return;
        }

        // ✅ DO: Unsubscribe in OnDisable
        _playerInputManager.onPlayerJoined -= OnPlayerJoined;
        _playerInputManager.onPlayerLeft -= OnPlayerLeft;
    }

    private void OnPlayerJoined(PlayerInput playerInput)
    {
        Debug.Log($"Player {playerInput.playerIndex} joined!");

        // Configure player-specific settings
        var controller = playerInput.GetComponent<PlayerController>();
        if (controller != null)
        {
            controller.SetPlayerIndex(playerInput.playerIndex);
        }
        else
        {
            Debug.LogWarning("PlayerController component is missing on spawned PlayerInput GameObject.");
        }
    }

    private void OnPlayerLeft(PlayerInput playerInput)
    {
        Debug.Log($"Player {playerInput.playerIndex} left!");
    }
}
```

## Input Rebinding

### Changing bindings at runtime

```
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.InputSystem.InputActionRebindingExtensions;
using System;
using System.Collections;
using MyGame.Input;

/// <summary>
/// Allows the player to rebind controls.
/// </summary>
public class InputRebinder : MonoBehaviour
{
    // ✅ DO: Use the same generated actions instance as the rest of the game
    // This can be injected from a central input service or created once and shared.
    private PlayerInputActions _inputActions;

    private InputActionRebindingExtensions.RebindingOperation _activeRebindOperation;

    private void Awake()
    {
        // Example: create a local instance.
        // In a real project, prefer sharing a single PlayerInputActions instance across systems.
        _inputActions = new PlayerInputActions();
        _inputActions.Gameplay.Enable();
    }

    private void OnDisable()
    {
        // Ensure any in-progress rebind is cleaned up
        CancelActiveRebind();
    }

    /// <summary>
    /// Starts the rebinding process for an action by name.
    /// </summary>
    public void StartRebinding(string actionName)
    {
        var actionMap = _inputActions.Gameplay;
        var action = actionMap.FindAction(actionName, throwIfNotFound: false);

        if (action == null)
        {
            Debug.LogError($"Action '{actionName}' was not found in the Gameplay action map.");
            return;
        }

        // ✅ DO: Use RebindingOperation
        CancelActiveRebind();

        action.Disable();

        _activeRebindOperation = action
            .PerformInteractiveRebinding()
            // Optional: add filters, e.g. .WithControlsExcluding("Mouse")
            .OnComplete(operation => OnRebindComplete(operation, action))
            .OnCancel(operation => OnRebindCancel(operation, action))
            .Start();
    }

    private void OnRebindComplete(InputActionRebindingExtensions.RebindingOperation operation, InputAction action)
    {
        Debug.Log($"Rebind complete for {action.name}");

        operation.Dispose();
        _activeRebindOperation = null;

        action.Enable();

        // Optionally, persist overrides (PlayerPrefs / file, etc.)
        // string overridesJson = _inputActions.SaveBindingOverridesAsJson();
        // PlayerPrefs.SetString("input_binding_overrides", overridesJson);
        // PlayerPrefs.Save();
    }

    private void OnRebindCancel(InputActionRebindingExtensions.RebindingOperation operation, InputAction action)
    {
        Debug.Log($"Rebind canceled for {action.name}");

        operation.Dispose();
        _activeRebindOperation = null;

        action.Enable();
    }

    private void CancelActiveRebind()
    {
        if (_activeRebindOperation != null)
        {
            _activeRebindOperation.Cancel();
            _activeRebindOperation.Dispose();
            _activeRebindOperation = null;
        }
    }
}
```

## DO's and DON'Ts Summary

### ❌ DON'T

```
// Legacy Input - FORBIDDEN
if (Input.GetKey(KeyCode.W))
{
}

if (Input.GetMouseButton(0))
{
}

float horizontal = Input.GetAxis("Horizontal");
```

### ✅ DO

```
// New Input System
Vector2 move = _inputActions.Gameplay.Move.ReadValue<Vector2>();
_inputActions.Gameplay.Jump.performed += OnJump;
```