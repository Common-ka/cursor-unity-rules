---
description: "Unity Test Framework (Production Standards)"
alwaysApply: false
---

# Unity Test Framework Guidelines

When writing tests in Unity 6.2, adhere to the **AAA (Arrange, Act, Assert)** pattern and use the **Constraint Model** for assertions (`Assert.That`). This ensures readability and maintainability.

## 1. Assembly Definition Requirement
Ensure your test script is located in a folder with an `.asmdef` (Assembly Definition) file that:
1. References `UnityEngine.TestRunner` and `UnityEditor.TestRunner`.
2. References the Assembly Definition of your main project logic (Runtime code).

## 2. Edit Mode Test (Pure Logic)
Use this for testing logic that is independent of the Engine Loop (e.g., Math, Data Parsing, State Machines). These tests run very fast.

```
using NUnit.Framework;
// using MyProject.Systems; 

public class HealthSystemTests
{
    [Test]
    public void TakeDamage_DecreasesCurrentHealth()
    {
        // Arrange
        var healthSystem = new HealthSystem(maxHealth: 100);

        // Act
        healthSystem.TakeDamage(30);

        // Assert
        // Use Constraint Model for better readability
        Assert.That(healthSystem.CurrentHealth, Is.EqualTo(70));
    }
}
```

## 3. Play Mode Test (Integration & Physics)
Use this for testing GameObject interactions, Physics, and Coroutines.
**IMPORTANT:** Always use `[TearDown]` to destroy created objects. If you rely on `Object.Destroy` at the end of the test method and an assertion fails beforehand, the object will leak into the scene.

```
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
using System.Collections;

public class PlayerMovementTests
{
    // Standard Microsoft C# style: _camelCase for private fields
    private GameObject _player;
    private PlayerController _controller;

    [SetUp]
    public void Setup()
    {
        // Create an isolated environment for each test
        _player = new GameObject("TestPlayer");
        _controller = _player.AddComponent<PlayerController>();
    }

    [TearDown]
    public void Teardown()
    {
        // Guaranteed cleanup even if an assertion fails
        if (_player != null)
        {
            Object.Destroy(_player);
        }
    }

    [UnityTest]
    public IEnumerator Move_ForwardInput_ChangesPosition()
    {
        // Arrange
        var startPosition = _player.transform.position;
        
        // Act
        _controller.Move(Vector3.forward);
        
        // Wait for one frame (for Update logic)
        yield return null; 
        
        // OR wait for physics step if using Rigidbody
        // yield return new WaitForFixedUpdate(); 
        
        // Assert
        Assert.That(_player.transform.position.z, Is.GreaterThan(startPosition.z));
    }
}
```