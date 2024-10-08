=======
### 1.2.7
* Rsync daemon life time is changed to one day
* Client has a function to check if the rsync daemon is running, otherwise it will relaunch it

### 1.2.6
* Increase the rsync server lifetime to three days
* Add statistic measures for collection

### 1.2.5
* Incremental option is changed to "--append-verify" to avoid file corruption

### 1.2.4
* Support user mode installation. It does not require any administration priviledge in Windows

### 1.2.3
* Hot-fix for 32bit version

### 1.2.2
* Support Windows 7 32bit

### 1.2.1
* Control daemon process lifecycle: the daemon process terminates after 1 day
* Sorting is based on alphabet

### 1.2.0
* This is the pre-release version
* Big update for the user interface
* Improve the filename checking logic

### 1.1.6
* Default permission is changed to 2770 for directories and 770 for files
* Focus on the text input box when new folder creating dialog box opens

### 1.1.5
* Handle the file locking issue in rsync process
* Browse the folder having spaces
* Support <kbd>Ctrl</kbd> + <kbd>C</kbd> for copying the text in console panel

### 1.1.4
* The fileserver URL is fixed
* The browse window content is initialized before authentication

### 1.1.3
* Fixed a bug of adding folders after several logins
* Rsync file check is now checksum instead of size change
* Creating new folder in the browser window

### 1.1.2
* Monitoring "addDir" as well as "add", which means that it triggers when new folder is created
* When the filename contains "space" or special characters, the app handles them
* User's home folder is included in the browser

### 1.1.1
* Fixed a bug of not collecting all the projects

### 1.1.0
* Re-transfer when files are added or changed while transferring

### 1.0.3
* Easy project spaces browsing
* Transmission resume
* Better console window

### 1.0.2
* Stand-alone application
* Better UI for browsing project spaces
* Resuming transmission when the transmission stopped unexpectedly

### 1.0.1
* Initial version with auto updater
