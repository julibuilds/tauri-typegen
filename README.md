# tauri-typegen

A Rust crate that automatically generates TypeScript models and bindings from your Tauri commands.

> **Note**: This is a [Claude Code](https://github.com/anthropics/claude-code) powered fork of [thwbh/tauri-typegen](https://github.com/thwbh/tauri-typegen) with fixes for custom type mappings.

## Installation

```bash
cargo install tauri-typegen
```

Or add to your workspace:

```toml
[workspace.dependencies]
tauri-typegen = { path = "crates/tauri-typegen" }
```

## Usage

### Generate TypeScript Bindings

```bash
cargo tauri-typegen generate
```

This will analyze your Tauri commands and generate TypeScript types and command wrappers.

### Configuration

Configure in your `tauri.conf.json` under `plugins.typegen`:

```json
{
  "plugins": {
    "typegen": {
      "projectPath": "./src-tauri",
      "outputPath": "./src/generated",
      "validationLibrary": "none",
      "verbose": true,
      "typeMappings": {
        "DateTime<Utc>": "string",
        "PathBuf": "string",
        "Uuid": "string"
      }
    }
  }
}
```

### Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `projectPath` | string | `"./src-tauri"` | Path to your Tauri project source |
| `outputPath` | string | `"./src/generated"` | Output path for generated TypeScript |
| `validationLibrary` | string | `"none"` | Validation library: `"zod"` or `"none"` |
| `verbose` | boolean | `false` | Enable verbose output |
| `typeMappings` | object | `{}` | Custom Rust → TypeScript type mappings |
| `excludePatterns` | string[] | `[]` | File patterns to exclude |
| `includePatterns` | string[] | `[]` | File patterns to include |

### Custom Type Mappings

The `typeMappings` option lets you map Rust types that aren't automatically recognized to their TypeScript equivalents:

```json
{
  "typeMappings": {
    "DateTime<Utc>": "string",
    "PathBuf": "string",
    "Uuid": "string",
    "NonZeroU32": "number",
    "MyCustomType": "{ foo: string; bar: number }"
  }
}
```

This is useful for:
- External crate types (`chrono::DateTime`, `uuid::Uuid`)
- Standard library types (`PathBuf`, `OsString`)
- Your own custom types with specific serialization

## Generated Output

The generator creates:

- `types.ts` - TypeScript interfaces for all your Rust structs/enums
- `commands.ts` - Typed async wrappers for all `#[tauri::command]` functions
- `index.ts` - Re-exports for convenient importing

### Example Usage

```typescript
import { captureNow, getRecentCaptures, type Capture } from './generated';

// Fully typed command calls
const result = await captureNow();
const captures: Capture[] = await getRecentCaptures({ limit: 10 });
```

## CLI Options

```bash
cargo tauri-typegen generate [OPTIONS]

Options:
  -p, --project-path <PATH>     Path to Tauri project
  -o, --output-path <PATH>      Output path for generated files
  -v, --validation <LIBRARY>    Validation library (zod, none)
  --verbose                     Enable verbose output
  --visualize-deps              Generate dependency graph
  -c, --config <PATH>           Path to config file
  -h, --help                    Print help
```

## Features

- Automatic discovery of `#[tauri::command]` functions
- Struct and enum type extraction with serde attribute support
- Handles `Option<T>`, `Result<T, E>`, `Vec<T>`, `HashMap<K, V>`
- Respects `#[serde(rename)]` and `#[serde(rename_all)]` attributes
- Optional Zod schema generation for runtime validation
- Custom type mappings for external crate types

## License

MIT
