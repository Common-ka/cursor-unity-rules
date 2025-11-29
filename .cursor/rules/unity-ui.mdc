---
description: "UI Toolkit (not UGUI)"
globs: ["**/UI*.cs","**/*.uxml","**/*.uss"]
alwaysApply: false
---

# UI Toolkit Rules (NOT UGUI)

## Project Structure

```
Assets/
├── UI/
│   ├── Runtime/
│   │   ├── Controllers/
│   │   │   └── MainMenuController.cs
│   │   ├── Views/
│   │   │   └── MainMenuView.cs
│   │   ├── ViewModels/
│   │   │   └── HealthViewModel.cs
│   │   ├── UXML/
│   │   │   └── MainMenu.uxml
│   │   └── USS/
│   │       └── MainMenu.uss
│   └── Editor/
│       └── UIBuilderExtensions.cs
```

## UXML Structure

```
<ui:UXML xmlns:ui="UnityEngine.UIElements">
    <ui:VisualElement name="MainContainer" class="container">
        <ui:Label text="Health:" class="label" />
        <ui:ProgressBar name="HealthBar" high-value="100" />
        <ui:Button name="StartButton" text="Start Game" />
    </ui:VisualElement>
</ui:UXML>
```

## USS Styling

```
/* Use CSS Variables for reusability */
:root {
    --primary-color: rgb(50, 150, 255);
    --secondary-color: rgb(255, 100, 50);
    --bar-height: 30px;
    --label-size: 24px;
    --container-padding: 20px;
}

.container {
    flex-direction: column;
    align-items: center;
    justify-content: center;
    padding: var(--container-padding);
}

.label {
    font-size: var(--label-size);
    color: white;
    -unity-font-style: bold;
}

#HealthBar {
    width: 300px;
    height: var(--bar-height);
    background-color: var(--primary-color);
    border-radius: 5px;
}

#StartButton {
    width: 200px;
    height: 50px;
    background-color: var(--secondary-color);
    margin-top: 20px;
}

#StartButton:hover {
    background-color: rgb(255, 120, 70);
}
```

## C# View (Pure UI Logic)

```
using System;
using UnityEngine.UIElements;

namespace UI.Runtime.Views
{
    /// <summary>
    /// Pure UI view layer - handles only visual element queries and basic UI operations
    /// </summary>
    public class MainMenuView : IDisposable
    {
        private readonly VisualElement _root;
        private readonly Button _startButton;
        private readonly ProgressBar _healthBar;
        private readonly Label _statusLabel;
        
        // Events for controller communication
        public event Action StartButtonClicked;
        
        public MainMenuView(VisualElement root)
        {
            _root = root ?? throw new ArgumentNullException(nameof(root));
            
            // Query UI elements with null checks
            _startButton = _root.Q<Button>("StartButton");
            _healthBar = _root.Q<ProgressBar>("HealthBar");
            _statusLabel = _root.Q<Label>("StatusLabel");
            
            ValidateElements();
            BindEvents();
        }
        
        private void ValidateElements()
        {
            if (_startButton == null)
                throw new InvalidOperationException("StartButton not found in UXML");
            if (_healthBar == null)
                throw new InvalidOperationException("HealthBar not found in UXML");
        }
        
        private void BindEvents()
        {
            _startButton.clicked += OnStartButtonClicked;
        }
        
        private void OnStartButtonClicked()
        {
            StartButtonClicked?.Invoke();
        }
        
        // Public API for controllers
        public void SetHealth(float value)
        {
            if (_healthBar != null)
                _healthBar.value = value;
        }
        
        public void SetStatusText(string text)
        {
            if (_statusLabel != null)
                _statusLabel.text = text;
        }
        
        public void SetInteractable(bool interactable)
        {
            if (_startButton != null)
                _startButton.SetEnabled(interactable);
        }
        
        public void Show()
        {
            _root.style.display = DisplayStyle.Flex;
        }
        
        public void Hide()
        {
            _root.style.display = DisplayStyle.None;
        }
        
        public void Dispose()
        {
            if (_startButton != null)
                _startButton.clicked -= OnStartButtonClicked;
        }
    }
}
```

## C# Controller (Business Logic)

```
using UnityEngine;
using UnityEngine.UIElements;
using UI.Runtime.Views;

namespace UI.Runtime.Controllers
{
    /// <summary>
    /// Controller that manages MainMenuView and coordinates with game systems
    /// </summary>
    [RequireComponent(typeof(UIDocument))]
    public class MainMenuController : MonoBehaviour
    {
        [SerializeField] private UIDocument uiDocument;
        
        private MainMenuView _view;
        private float _currentHealth = 100f;
        
        private void Awake()
        {
            if (uiDocument == null)
                uiDocument = GetComponent<UIDocument>();
            
            InitializeView();
        }
        
        private void InitializeView()
        {
            var root = uiDocument.rootVisualElement;
            _view = new MainMenuView(root);
            
            // Subscribe to view events
            _view.StartButtonClicked += HandleStartGame;
            
            // Initialize UI state
            _view.SetHealth(_currentHealth);
            _view.SetStatusText("Ready to start");
        }
        
        private void HandleStartGame()
        {
            Debug.Log("Starting game...");
            _view.SetInteractable(false);
            _view.SetStatusText("Loading...");
            
            // Trigger game start logic
            GameManager.Instance?.StartGame();
        }
        
        public void UpdateHealth(float newHealth)
        {
            _currentHealth = Mathf.Clamp(newHealth, 0f, 100f);
            _view?.SetHealth(_currentHealth);
        }
        
        private void OnDestroy()
        {
            // Critical: Always dispose to prevent memory leaks
            if (_view != null)
            {
                _view.StartButtonClicked -= HandleStartGame;
                _view.Dispose();
            }
        }
    }
}
```

## Advanced: Data Binding (Unity 6+)

```
using UnityEngine;
using UnityEngine.UIElements;

namespace UI.Runtime.ViewModels
{
    /// <summary>
    /// ViewModel using Unity 6 Data Binding system (MVVM pattern)
    /// </summary>
    [UxmlElement]
    public partial class HealthViewModel : BindableElement
    {
        private float _health = 100f;
        
        [UxmlAttribute]
        public float Health
        {
            get => _health;
            set
            {
                if (Mathf.Approximately(_health, value))
                    return;
                    
                _health = Mathf.Clamp(value, 0f, 100f);
                
                // Notify UI about changes
                NotifyPropertyChanged(nameof(Health));
                UpdateVisuals();
            }
        }
        
        private void UpdateVisuals()
        {
            // Update progress bar or other visual elements
            var progressBar = this.Q<ProgressBar>();
            if (progressBar != null)
                progressBar.value = _health;
        }
    }
}
```

## Performance Optimization

### Usage Hints for Dynamic Elements

```
using UnityEngine;
using UnityEngine.UIElements;

namespace UI.Runtime.Controllers
{
    public class PerformanceOptimizedUI : MonoBehaviour
    {
        private void Start()
        {
            var root = GetComponent<UIDocument>().rootVisualElement;
            
            // Dynamic elements (frequently updated/moved)
            var healthBar = root.Q<ProgressBar>("HealthBar");
            if (healthBar != null)
                healthBar.usageHints = UsageHints.DynamicTransform;
            
            // Static elements (rarely change)
            var background = root.Q<VisualElement>("Background");
            if (background != null)
                background.usageHints = UsageHints.GroupTransform;
            
            // Elements with dynamic colors/styles
            var damageIndicator = root.Q<VisualElement>("DamageFlash");
            if (damageIndicator != null)
                damageIndicator.usageHints = UsageHints.DynamicColor;
        }
    }
}
```

### Vertex Buffer Optimization

```
using UnityEngine;
using UnityEngine.UIElements;

namespace UI.Runtime.Controllers
{
    public class UISetup : MonoBehaviour
    {
        [SerializeField] private PanelSettings panelSettings;
        
        private void Awake()
        {
            if (panelSettings != null)
            {
                // Increase vertex buffer size for complex UI
                // Default is 64K vertices
                panelSettings.dynamicAtlasSettings.maxAtlasSize = 2048;
            }
        }
    }
}
```

## World Space UI (Unity 6.2+)

```
using UnityEngine;
using UnityEngine.UIElements;

namespace UI.Runtime.Controllers
{
    /// <summary>
    /// Setup for 3D World Space UI using UI Toolkit
    /// </summary>
    public class WorldSpaceUIController : MonoBehaviour
    {
        [SerializeField] private UIDocument uiDocument;
        [SerializeField] private RenderTexture targetTexture;
        
        private void Awake()
        {
            SetupWorldSpaceUI();
        }
        
        private void SetupWorldSpaceUI()
        {
            var panelSettings = ScriptableObject.CreateInstance<PanelSettings>();
            
            // Configure for world space rendering
            panelSettings.targetDisplay = 0;
            panelSettings.renderMode = PanelRenderMode.WorldSpace;
            panelSettings.targetTexture = targetTexture;
            panelSettings.scale = 1f;
            
            // Apply to UIDocument
            uiDocument.panelSettings = panelSettings;
            
            // Position in 3D space
            transform.position = new Vector3(0, 2, 5);
            transform.rotation = Quaternion.Euler(0, 180, 0);
        }
    }
}
```

## Visibility Management

```
using UnityEngine.UIElements;

namespace UI.Runtime.Views
{
    public static class UIVisibilityExtensions
    {
        /// <summary>
        /// Completely removes element from layout and rendering
        /// Use for performance when element is not needed
        /// </summary>
        public static void HideCompletely(this VisualElement element)
        {
            element.style.display = DisplayStyle.None;
        }
        
        /// <summary>
        /// Hides element but preserves layout space
        /// Use for animations or when maintaining layout structure
        /// </summary>
        public static void HideButKeepSpace(this VisualElement element)
        {
            element.style.visibility = Visibility.Hidden;
        }
        
        /// <summary>
        /// Makes element transparent but still renders
        /// Use for fade-out animations
        /// </summary>
        public static void MakeTransparent(this VisualElement element)
        {
            element.style.opacity = 0;
        }
        
        /// <summary>
        /// Shows element that was hidden
        /// </summary>
        public static void ShowElement(this VisualElement element)
        {
            element.style.display = DisplayStyle.Flex;
            element.style.visibility = Visibility.Visible;
            element.style.opacity = 1;
        }
    }
}
```

## Best Practices Checklist

### Memory Management
- ✅ Always unsubscribe from events in `OnDestroy()` or `Dispose()`
- ✅ Use `null` checks after `Q<T>()` queries
- ✅ Dispose of views when controllers are destroyed
- ✅ Clear event handlers before reassigning UI elements

### Performance
- ✅ Set appropriate `UsageHints` for dynamic elements
- ✅ Use CSS variables for style reusability
- ✅ Minimize DOM queries - cache references in `Awake()`
- ✅ Batch UI updates when possible
- ✅ Use `display: none` for hidden elements to save rendering cost

### Architecture
- ✅ Separate View (UI logic) from Controller (business logic)
- ✅ Use events for View-to-Controller communication
- ✅ Keep Views stateless - Controllers manage state
- ✅ Use ViewModels with Data Binding for complex UIs
- ✅ Structure folders by feature: `Controllers/`, `Views/`, `UXML/`, `USS/`

### Code Quality
- ✅ Use meaningful names following Microsoft C# conventions
- ✅ Private fields use `_camelCase` prefix
- ✅ Add XML documentation comments for public APIs
- ✅ Validate UI elements in constructor/Awake
- ✅ Handle edge cases (null elements, invalid values)