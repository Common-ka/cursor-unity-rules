---
description: "Performance Optimization"
alwaysApply: false
---

# Unity Performance Rules

## Update/FixedUpdate/LateUpdate

### Usage Rules

```
using UnityEngine;

public class PerformanceExample : MonoBehaviour
{
    private Rigidbody _rigidbody;
    
    // ✅ DO: Cache components in Awake/Start
    private void Awake()
    {
        _rigidbody = GetComponent<Rigidbody>();
    }
    
    // ❌ DON'T: Frequent GetComponent calls in Update
    /* 
    private void Update()
    {
        GetComponent<Rigidbody>().AddForce(Vector3.up); // Bad! Expensive call every frame.
    }
    */
    
    private void FixedUpdate()
    {
        // ✅ DO: Physics calculations in FixedUpdate
        _rigidbody.AddForce(Vector3.up);
    }
    
    private void Update()
    {
        // ✅ DO: Input processing and frame-logic in Update
        ProcessInput();
    }
    
    private void LateUpdate()
    {
        // ✅ DO: Camera following logic in LateUpdate (after player moves)
        UpdateCameraPosition();
    }
    
    private void ProcessInput() { /* ... */ }
    private void UpdateCameraPosition() { /* ... */ }
}
```

## Object Pooling (UnityEngine.Pool)

**Unity 6.2 Note:** Do not write custom Queue-based pools. Use the native `UnityEngine.Pool` API.

```
using UnityEngine;
using UnityEngine.Pool;

public class BulletManager : MonoBehaviour
{
    [SerializeField] private Bullet _bulletPrefab;
    
    // Native Unity Pool interface
    private IObjectPool<Bullet> _bulletPool;
    
    private void Awake()
    {
        // Initialize the pool
        _bulletPool = new ObjectPool<Bullet>(
            createFunc: CreateBullet,
            actionOnGet: OnGetBullet,
            actionOnRelease: OnReleaseBullet,
            actionOnDestroy: OnDestroyBullet,
            collectionCheck: true, // Checks for double-return errors (debug only)
            defaultCapacity: 50,
            maxSize: 100
        );
    }

    private Bullet CreateBullet()
    {
        Bullet bullet = Instantiate(_bulletPrefab, transform);
        bullet.SetPool(_bulletPool); // Inject pool reference into the bullet
        return bullet;
    }

    private void OnGetBullet(Bullet bullet) => bullet.gameObject.SetActive(true);
    private void OnReleaseBullet(Bullet bullet) => bullet.gameObject.SetActive(false);
    private void OnDestroyBullet(Bullet bullet) => Destroy(bullet.gameObject);
    
    public void FireBullet(Vector3 position, Vector3 direction)
    {
        Bullet bullet = _bulletPool.Get();
        bullet.transform.position = position;
        bullet.Fire(direction);
    }
}

public class Bullet : MonoBehaviour
{
    private IObjectPool<Bullet> _pool;

    public void SetPool(IObjectPool<Bullet> pool) => _pool = pool;

    public void Fire(Vector3 direction) { /* Physics logic */ }

    private void DisableSelf()
    {
        // ✅ DO: Return to pool instead of Destroy()
        _pool.Release(this);
    }
}
```

## Addressables (NOT Resources)

**Unity 6.2 Note:** Use `Awaitable` instead of `System.Threading.Tasks`.

```
using UnityEngine;
using UnityEngine.AddressableAssets;
using UnityEngine.ResourceManagement.AsyncOperations;

public class AssetLoader : MonoBehaviour
{
    // ❌ DON'T: Resources.Load (Synchronous, increases memory footprint)
    /*
    private void BadLoad()
    {
        GameObject prefab = Resources.Load<GameObject>("Prefabs/Enemy");
    }
    */
    
    private AsyncOperationHandle<GameObject> _handle;
    private GameObject _loadedInstance;

    // ✅ DO: Addressables with Awaitable (Unity 6)
    public async Awaitable LoadEnemyAsync()
    {
        // Avoid loading if already loaded
        if (_handle.IsValid()) return;

        _handle = Addressables.LoadAssetAsync<GameObject>("Enemy");
        
        try 
        {
            await _handle.Task; // Native await integration

            if (_handle.Status == AsyncOperationStatus.Succeeded)
            {
                GameObject prefab = _handle.Result;
                _loadedInstance = Instantiate(prefab);
            }
        }
        catch (System.Exception e)
        {
            Debug.LogError($"Failed to load asset: {e.Message}");
        }
    }
    
    // ✅ DO: Correct Memory Release
    private void OnDestroy()
    {
        if (_loadedInstance != null) Destroy(_loadedInstance);

        if (_handle.IsValid())
        {
            // Crucial: Release the handle to free memory
            Addressables.Release(_handle);
        }
    }
}
```

## Memory Management (Zero Allocations)

```
using UnityEngine;
using System.Collections.Generic;
using TMPro;
using System.Text;

public class MemoryOptimization : MonoBehaviour
{
    // ✅ DO: Pre-allocate collections to avoid resizing in runtime
    private readonly List<int> _numbers = new List<int>(100); 

    private void Update()
    {
        // ❌ DON'T: New List allocation or resizing in Update
        // List<int> temp = new List<int>(); 
        
        // ✅ DO: Reuse collections
        _numbers.Clear();
        // Fill list...
    }
}

public class UiOptimization : MonoBehaviour
{
    [SerializeField] private TMP_Text _scoreText;
    
    // ✅ DO: Reusable StringBuilder
    private StringBuilder _sb = new StringBuilder(64);

    public void UpdateScore(string playerName, int score)
    {
        _sb.Clear();
        _sb.Append("Player: ").Append(playerName).Append(", Score: ").Append(score);
        
        // ✅ DO: Pass StringBuilder directly to SetText.
        // Avoids .ToString() which creates a new string allocation every frame.
        _scoreText.SetText(_sb);
    }
}
```