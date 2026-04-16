## Development guideline

### Architecture

Scion uses Vue 3 Composition API with TypeScript. The main UI component `LandingPage.vue` is a thin orchestrator that delegates logic to 11 composables:

| Composable | Purpose |
|-----------|---------|
| `useAuth` | LDAP authentication, connection state, login/logout |
| `useRsync` | Rsync process state, stop transfer, status/error management |
| `useDatabase` | SQLite transaction tracking (start, resume, complete, cancel) |
| `useFileWatcher` | Chokidar-based file monitoring, triggers re-sync on changes |
| `useAppState` | Electron-store persistence for layout, rsync options, server IP |
| `useLayout` | GoldenLayout panel management, xterm terminal setup |
| `useRemoteFolders` | Remote folder browsing via SSH, modal management |
| `useServer` | Server availability watching |
| `useNetworkDrive` | Windows network drive detection |
| `useTransfer` | File transfer orchestration (local + remote rsync) |
| `useTheme` | Dark/light/system theme toggle with electron-store persistence |

All composables are consumed by `LandingPage.vue` only; child components receive state via props.

### Configuration

Server list and LDAP settings are centralized in `src/config.ts`. To add a new server, add an entry to the `servers` array:

```typescript
{
  name: 'servername',
  defaultIp: 'server.example.com',
  projectPrefix: '/projects/'
}
```

### Testing

Tests use [Vitest](https://vitest.dev/) with the following patterns:

- `vi.hoisted()` + `Module._load` intercept for CJS-style mocks (sqlite3, @electron/remote, ldapjs)
- `fileParallelism: false` in `vitest.config.mts` to prevent race conditions
- Component tests require `@vitejs/plugin-vue` plugin
- Per-file environment overrides (e.g., `// @vitest-environment jsdom`)

```bash
# Run all tests
npm run test:vitest:ci

# Run tests with UI
npm run test:vitest:ui

# Run tests in watch mode
npm run test:vitest
```

### Versioning

`commit-and-tag-version` package is used for generating *Change Logs* and *Version number* tags.

```shell
# Release with a specific version
npm run release -- --release-as 1.2.13
```

### Commit message convention

Uses **"\<type>: \<description>"** format:

```
git commit -a -m"<type>[optional scope]: <description>"
```

* **feat**: introduces a new feature (MINOR in SemVer, e.g. 2.0.0 -> 2.1.0)
* **fix**: a bugfix (PATCH in SemVer, e.g. 2.0.0 -> 2.0.1)
* **chore**: a change irrelevant to functions
* **refactor**: code readability and style improvements
* **docs**: documentation changes
* **style**: UI changes
* **perf**: performance improvements
* **test**, **build**, **ci**: test, build and CI changes respectively
* `feat!:`, `fix!:`, `refactor!:`, etc.: breaking changes (MAJOR in SemVer, e.g. 2.0.0 -> 3.0.0)

References:
* ["How to generate Changelog using Conventional Commits"](https://medium.com/jobtome-engineering/how-to-generate-changelog-using-conventional-commits-10be40f5826c)

### Rsync process management

All rsync path resolution is handled by `getRsyncExe()` in `lib/rsync/process.js`:

- **macOS**: resolves to `/opt/homebrew/bin/rsync` (arm64) or `/usr/local/bin/rsync` (x64)
- **Windows**: resolves to `rsync.exe` and adds the bundled `utils/windows64/` (or `windows32/`) directory to PATH automatically
- **Linux**: uses the system `rsync`

The rsync child process is spawned with `detached: true` on all platforms so the process group can be killed cleanly on stop/logout.

`smbmap` has been removed -- remote folder listing is now handled entirely via SSH.

