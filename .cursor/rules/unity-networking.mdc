---
description: "Netcode for GameObjects"
alwaysApply: false
---

# Netcode for GameObjects

## NetworkBehaviour

```
using System;
using Unity.Netcode;
using UnityEngine;

public class NetworkPlayer : NetworkBehaviour
{
    // Modern Standard: Use _camelCase for private fields, remove "m_" prefix
    // Security: NetworkVariable should usually be read-only for others
    private readonly NetworkVariable<int> _health = new NetworkVariable<int>(
        100, 
        NetworkVariableReadPermission.Everyone, 
        NetworkVariableWritePermission.Server
    );

    // Reactivity: Event to update UI/Animations when health changes
    public event Action<int> OnHealthChanged;

    public override void OnNetworkSpawn()
    {
        // Subscribe to changes to update UI automatically
        _health.OnValueChanged += HandleHealthChanged;
        
        // Initialize state immediately on spawn
        HandleHealthChanged(0, _health.Value);

        if (IsOwner)
        {
            // Owner only logic (e.g., Input handling)
        }
        
        if (IsServer)
        {
            // Server only logic
        }
    }

    public override void OnNetworkDespawn()
    {
        _health.OnValueChanged -= HandleHealthChanged;
    }

    private void HandleHealthChanged(int previousValue, int newValue)
    {
        // Update UI or play local sounds here
        OnHealthChanged?.Invoke(newValue);
    }
    
    // FIX: Removed insecure [ServerRpc] that allowed clients to dictate damage.
    // Replaced with a Server-side method. This should be called by the server
    // when a projectile hits or an explosion occurs.
    public void TakeDamage(int damage)
    {
        // Security check: Only the server can modify state
        if (!IsServer) return;
        
        // Validation: Prevent negative damage or logic errors
        if (damage < 0) return;

        // State safety: Clamp values to prevent underflow
        int newHealth = _health.Value - damage;
        _health.Value = Mathf.Clamp(newHealth, 0, 100);

        if (_health.Value == 0)
        {
            // Handle death
        }
    }
    
    [ClientRpc]
    public void PlayEffectClientRpc()
    {
        // Play visual/audio effect on all clients
    }
}
```