## `Directory.Build.props` and `Directory.Build.targets`

`Directory.Build.props` and `Directory.Build.targets` are special `MSBuild` files that let you centralize configuration across multiple C# projects in a solution. The key difference is that `.props` is loaded early to set default properties, while `.targets` is loaded late to add or override build tasks. Together, they simplify managing large solutions by avoiding duplication in individual `.csproj` files.

- `MSBuild` is the build engine behind .NET projects.
- Instead of repeating settings in every `.csproj`, you can place shared configuration in these files:
    - `Directory.Build.props` → defines properties (like compiler options, frameworks, versions).
    - `Directory.Build.targets` → defines targets (actual build steps, tasks, or overrides).

### MSBuild Import Order
`MSBuild` evaluates files in a specific sequence:

1. `Directory.Build.props`
- Imported before the project file (`.csproj`) is processed.
- This means any properties here act like defaults.
- If the project file later redefines the same property, the project’s value wins (because it comes later).

- Example:

```
<Project>
  <PropertyGroup>
    <LangVersion>latest</LangVersion>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

2. Project File (`.csproj`)
- MSBuild now reads the project file itself.
- Properties and items defined here override anything from `Directory.Build.props`.

3. `Directory.Build.targets`
- Imported after the project file is processed.
- This is the place for custom build steps or overrides.
- Because it comes last, it can override both `Directory.Build.props` and the project file.
- Example:
```
<Project>
  <Target Name="CopyOutputs" AfterTargets="Build">
    <Copy SourceFiles="$(TargetPath)" DestinationFolder="..\SharedBin\" />
  </Target>
</Project>
```

4. Other Imports
- If you explicitly import other `.props` or `.targets` inside your `.csproj`, their position in the file determines when they’re applied.
- MSBuild also has built-in imports (like `Microsoft.CSharp.targets`) that load at the end.

🔑 Key Rule
- Earlier imports = defaults.
- Later imports = overrides.
So if you set OutputPath in Directory.Build.props and then redefine it in the .csproj, the .csproj value wins. But if you redefine it in Directory.Build.targets, that wins over both.

## `global.json`

In .NET, the `global.json` file is used to control which .NET SDK version is used when building or running projects, ensuring consistency across machines and CI/CD pipelines. It does not affect the runtime version of your app, only the SDK tooling.

### Purpose of `global.json`
- **SDK Version Control:** Locks your solution to a specific .NET SDK version, avoiding unexpected changes when new SDKs are installed.
- **Consistency:** Ensures all developers and build agents use the same SDK version.
- **RollForward Policy:** Lets you define whether newer SDKs can be used if the exact version isn’t available.

### Typical Location
- Placed at the solution root (same level as `.sln`).
- Applies to all projects under that directory tree.

### Example `global.json`
```
{
  "sdk": {
    "version": "8.0.100",
    "rollForward": "latestFeature"
  }
}
```

### Key Points
- **Independent of runtime:** Even if your project targets `.NET 6.0`, the SDK used to build it could be `.NET 8.0` if specified in `global.json`.
- **No file needed if always using latest SDK:** But in CI/CD or team environments, it’s strongly recommended.
- **Supports multiple SDKs:** Useful when maintaining legacy projects alongside newer ones.


## `packages.lock.json`

`packages.lock.json` in C# projects is a NuGet lock file that ensures repeatable and deterministic packages restores by recording the exact dependency graph (including transitive dependencies and versions) used during a build.

### Purpose of `packages.lock.json`
- **Repeatable Builds:** Guarantees that all developers and CI/CD agents restore the same package versions.
- **Dependency Graph Locking:** Captures not only direct dependencies but also transitive ones.
- **Change Tracking:** Any modification in package references updates the lock file, making dependency changes visible in source control.

### How It Works
- Enable Lock File
Add this property in your `.csproj` or `Directory.Build.props`:

```
<PropertyGroup>
  <RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
</PropertyGroup>
```

1. This tells NuGet to generate and maintain `packages.lock.json`.
2. File Location
  - Created at the project root (same folder as `.csproj`).
  - Each project gets its own `packages.lock.json`.
3. Update Behavior
  - If you add or update a package, NuGet updates the lock file.
  - If no changes are made, restores use the existing lock file for consistency.

### Key Benefits-
- **Deterministic Restores:** Builds won’t break due to newer transitive versions being pulled.
- **Source Control Visibility:** Dependency changes are tracked in PRs.
- **CI/CD Reliability:** Ensures pipelines use the same dependency graph as local dev machines.

