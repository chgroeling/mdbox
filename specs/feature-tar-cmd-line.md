# Feature: Tar-Style CLI Refactor (Command & Argument Structure)

## 1. Overview
This feature refactors the `quiver` CLI to mirror the syntax and behavior of the `tar` utility. The primary shift moves from a subcommand-based structure (`pack`) to a flag-based operation mode (`-c` for create). This aligns the tool with POSIX-style archiving conventions, allowing for bundled flags and a more streamlined input/output definition.

---

## 2. Requirements

### 2.1. Command Syntax & Flag Bundling
The tool must move away from the `pack` subcommand in favor of operation flags.

* **New Primary Command:** `quiver -cf <output.xml> <input_path...>`
* **Flag Bundling:** The CLI must support combined short flags (e.g., `-cvf`, `-cbf`).
* **The `-f` Constraint:** Consistent with `tar`, the `-f` (file) flag must be the last flag in a bundle if an argument follows, or it must immediately precede the output filename.
* **Input Paths:** All arguments following the output file are treated as positional inputs (files or directories) to be processed recursively.

### 2.2. Mapping Existing Logic to Tar-Style
The existing global flags and subcommands must be mapped to the new syntax:

| Old Syntax (Subcommand) | New Syntax (Tar-style) | Description |
| :--- | :--- | :--- |
| `quiver pack <in> -f <out>` | `quiver -cf <out> <in>` | **Create:** Starts the packing process. |
| `quiver unpack <in>` | `quiver -xf <in>` | **Extract:** (Mapped to the existing stub). |
| `--verbose` / `-v` | `-v` | **Verbose:** Integrated into the flag bundle. |
| `--debug` | `-b` (or `--debug`) | **Debug:** Structured logging. |

### 2.3. Behavior & Validations
* **Silence by Default:** If `-v` or `--debug` are not present, the tool must remain silent on success.
* **Auto-Detection:** The logic must still automatically detect if positional inputs are individual files or directories.
* **Multiple Inputs:** Unlike the previous version, the new CLI must support multiple input paths in one command:
    * *Example:* `quiver -cf archive.xml ./src ./README.md ./docs`
* **Error Handling:** * If `-c` is invoked without `-f`, the tool should provide a clear error (or default to stdout if supported in future versions).
    * Missing input paths should trigger a "Usage" help message.

### Usages

Support both usage styles like the original tar
UNIX-style usage
       tar -A [OPTIONS] -f ARCHIVE ARCHIVE...

       tar -c [-f ARCHIVE] [OPTIONS] [FILE...]

       tar -d [-f ARCHIVE] [OPTIONS] [FILE...]

       tar -r [-f ARCHIVE] [OPTIONS] [FILE...]

       tar -t [-f ARCHIVE] [OPTIONS] [MEMBER...]

       tar -u [-f ARCHIVE] [OPTIONS] [FILE...]

       tar -x [-f ARCHIVE] [OPTIONS] [MEMBER...]

   GNU-style usage
       tar {--catenate|--concatenate} [OPTIONS] --file ARCHIVE ARCHIVE...

       tar --create [--file ARCHIVE] [OPTIONS] [FILE...]

       tar {--diff|--compare} [--file ARCHIVE] [OPTIONS] [FILE...]

       tar --delete [--file ARCHIVE] [OPTIONS] [MEMBER...]

       tar --append [--file ARCHIVE] [OPTIONS] [FILE...]

       tar --list [--file ARCHIVE] [OPTIONS] [MEMBER...]

       tar --test-label [--file ARCHIVE] [OPTIONS] [LABEL...]

       tar --update [--file ARCHIVE] [OPTIONS] [FILE...]

       tar {--extract|--get} [--file ARCHIVE] [OPTIONS] [MEMBER...]

### 2.4 New feature
*  Dont forget to add "--debug" . this flag does not have a short hand (e.g. "-d") 
---

## 3. Implementation Comparison

### Legacy Command (Subcommand Pattern)
```bash
quiver -v pack ./my_project -f backup.xml
```

### New Command (Tar-style Pattern)
```bash
quiver -cvf backup.xml ./my_project
```

