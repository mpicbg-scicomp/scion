=======
## [1.2.12](https://git.mpi-cbg.de/scicomp/scidev_team/scion/-/compare/v1.2.1...v1.2.12) (2026-03-30)

### Features

* **Dark mode** — three-state theme toggle (system / light / dark) with CSS variable system, OS preference detection via `prefers-color-scheme`, and persistence to electron-store. GoldenLayout panels, xterm terminal, Bootstrap 3 components, modals, and tree views all adapt to the selected theme. Includes transparent-background logo variant for dark mode.
* **Multi-server support** — server selection dropdown in the Login panel with configurable server list. Each server has its own project path prefix and transaction tracking via `serverIp` in the database.
* **Composition API migration** — refactored LandingPage.vue from a 1500+ line monolithic Options API component into a thin orchestrator with 11 composables: `useAuth`, `useRsync`, `useDatabase`, `useFileWatcher`, `useAppState`, `useLayout`, `useRemoteFolders`, `useServer`, `useNetworkDrive`, `useTransfer`, `useTheme`.
* **TypeScript migration** — renderer and main process converted to TypeScript with shared type definitions.
* **Enter to connect** — pressing Enter in the password field triggers authentication.
* **Pipeline documentation** — added Scion pipeline setup and configuration guide.

### Bug Fixes

* **Stop transfer now works** — fixed `shell:true` PID bug where killing the shell process orphaned rsync. Now uses `detached:true` with process group kill (`-pid`) to reliably terminate rsync and all children.
* **No more uncaught runtime errors on stop** — rsync close handler now resolves instead of rejecting the promise (which was never awaited), eliminating "Uncaught runtime errors" overlay when stopping a transfer.
* **SSH port range** — fixed upper port limit in SSH session handling.
* **Database serverIp column** — reorganized `ALTER TABLE` / backfill logic to avoid `SQLITE_MISUSE: Database handle is closed` error.
* **Tooltip clipping** — removed `overflow:hidden` from panels that was clipping tooltip popups.
* **GoldenLayout light theme** — fixed panes showing dark backgrounds in light mode caused by imported GL dark theme CSS winning the cascade.
* **Rsync --no-group** — added `--no-group` flag to prevent permission errors on shared filesystems.

### UI Improvements

* Distinct tinted backgrounds for Login / Transfer / Console panes (both themes)
* Distinct tinted backgrounds for panel types (default / info / success)
* Per-tab color coding matching each pane's background
* Modernized panels with left accent stripes, rounded corners, and shadows
* Modernized buttons, inputs, modals, tooltips, and status bar
* Custom scrollbar styling for both themes
* GoldenLayout maximize button visibility fix in light mode
* Radio button and label alignment fix in Transfer options
* FOUC prevention — inline script in index.ejs applies theme before first paint

### Maintenance

* Updated package dependencies to latest versions
* Removed unused SMB file listing module
* Added vitest test infrastructure (17 passing tests including useTheme composable)
* Added beads issue tracking integration

## 1.2.11
* Another test for autoUpdate

## 1.2.10
* Test for autoUpdate

## 1.2.9
* Test for update

## 1.2.8
* Fix PID file check for rsync daemon — The SSH session was checking for the wrong PID file (rsync.pid instead of rsyncd.pid), which meant
  the daemon could be launched multiple times. Also fixed the kill script to clean up the sleep.pid file after terminating rsync.
* Fix race condition in rsyncActive flag — Moved rsyncActive = true to before the status message and SSH check call, preventing a window
  where a second file watcher event could trigger a duplicate rsync process.

## 1.2.7
* Rsync daemon life time is changed to one day
* Client has a function to check if the rsync daemon is running, otherwise it will relaunch it

## 1.2.6
* Increase the rsync server lifetime to three days
* Add statistic measures for collection

## 1.2.5
* Incremental option is changed to "--append-verify" to avoid file corruption

## 1.2.4
* Support user mode installation. It does not require any administration priviledge in Windows

## 1.2.3
* Hot-fix for 32bit version

## 1.2.2
* Support Windows 7 32bit

## 1.2.1
* Control daemon process lifecycle: the daemon process terminates after 1 day
* Sorting is based on alphabet

## 1.2.0
* This is the pre-release version
* Big update for the user interface
* Improve the filename checking logic

## 1.1.6
* Default permission is changed to 2770 for directories and 770 for files
* Focus on the text input box when new folder creating dialog box opens

## 1.1.5
* Handle the file locking issue in rsync process
* Browse the folder having spaces
* Support <kbd>Ctrl</kbd> + <kbd>C</kbd> for copying the text in console panel

## 1.1.4
* The fileserver URL is fixed
* The browse window content is initialized before authentication

## 1.1.3
* Fixed a bug of adding folders after several logins
* Rsync file check is now checksum instead of size change
* Creating new folder in the browser window

## 1.1.2
* Monitoring "addDir" as well as "add", which means that it triggers when new folder is created
* When the filename contains "space" or special characters, the app handles them
* User's home folder is included in the browser

## 1.1.1
* Fixed a bug of not collecting all the projects

## 1.1.0
* Re-transfer when files are added or changed while transferring

## 1.0.3
* Easy project spaces browsing
* Transmission resume
* Better console window

## 1.0.2
* Stand-alone application
* Better UI for browsing project spaces
* Resuming transmission when the transmission stopped unexpectedly

## 1.0.1
* Initial version with auto updater
