# README.md

```markdown

## 📋 Description

Production-ready `.cursor/rules` configuration for Unity 6.2+ projects. Modern AI rules system for Cursor IDE (2025) using the new `.mdc` format instead of the deprecated `.cursorrules`.

## 🚀 Quick Start

### Requirements

- Unity 6.2 or higher
- Cursor IDE
- .NET SDK for C# development

### Installation

1. Clone the repository into your Unity project root:

```
cd YourUnityProject
git clone https://github.com/Common-ka/cursor-unity-rules.git
```

2. Or manually copy the `.cursor/` folder to your project root:

```
YourUnityProject/
├── .cursor/
│   ├── index.mdc
│   └── rules/
│       ├── unity-core.mdc
│       ├── unity-input.mdc
│       ├── unity-performance.mdc
│       ├── unity-architecture.mdc
│       ├── unity-ecs.mdc
│       ├── unity-ui.mdc
│       ├── unity-networking.mdc
│       ├── unity-testing.mdc
│       └── code-organization.mdc
├── Assets/
├── ProjectSettings/
└── ...
```

3. Copy `.vscode/settings.json` to your project:

```
cp cursor-unity-rules/.vscode/settings.json YourUnityProject/.vscode/
```

4. Open your project in Cursor IDE

5. Rules will automatically apply when working with code

## 📄 License

MIT License - see [LICENSE](LICENSE)
