## `Directory.Build.props` and `Directory.Build.targets`

`Directory.Build.props` and `Directory.Build.targets` are special `MSBuild` files that let you centralize configuration across multiple C# projects in a solution. The key difference is that `.props` is loaded early to set default properties, while `.targets` is loaded late to add or override build tasks. Together, they simplify managing large solutions by avoiding duplication in individual .csproj files

- `MSBuild` is the build engine behind .NET projects.
- Instead of repeating settings in every `.csproj`, you can place shared configuration in these files:
    - `Directory.Build.props` → defines properties (like compiler options, frameworks, versions).
    - `Directory.Build.targets` → defines targets (actual build steps, tasks, or overrides).

### Load Order
`MSBuild` imports files in a specific sequence:

1. `Directory.Build.props`
- Imported before the project file.
- Used to set defaults (e.g., language version, nullable settings, package references).
- Example:

```
<Project>
  <PropertyGroup>
    <LangVersion>latest</LangVersion>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```
2. Project File (.csproj)
- Contains project-specific settings.

3. Directory.Build.targets
- Imported after the project file.
- Used to add or override build steps (e.g., post-build copy, running tools).

- Example:



