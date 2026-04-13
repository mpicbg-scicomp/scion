# Scion Pipeline Diagram

## Overview
This document describes the data flow and process pipelines in the Scion application based on LandingPage.vue.

---

## Main Process Flows


### 1. Remote Mode Authentication Pipeline

```mermaid
flowchart TD
    A([User Selects Server\nfrom Server Dropdown]) --> B[User Inputs Credentials\nUser ID · Password]
    B --> C[Click Connect\nauth]
    B --> C[LDAP Authentication\nldap-srv1]
    C --> D{Success?}
    D -- No --> E[Show Error Message]
    D -- Yes --> F[Search User Groups\nLDAP memberUid filter]
    F --> G[Generate API Key\n20-char random string]
    G --> H[Get Remote Info\nSSH: getRemoteInfo\nusing selected server projectPrefix]
    H --> I["Execute SSH Commands\n1. Get available port\n2. Count writable folders"]
    I --> J["Parse Results\nport = data.toString.trim\nfolders = data.toString.trim"]
    J --> K[Create rsyncd.conf\nSSH: createRsyncConf]
    K --> L[Start Rsync Daemon\nSSH: startRsyncd]
    L --> M[Check Unfinished Transfers\ncheckUnfinishedTx]
    M --> N([connected = true\nStatus: Successfully authorized])
```


---

### 2. Remote File Transfer Pipeline

```mermaid
flowchart TD
    A([User Selects Project Path\nFileserver Browser Modal]) --> B[Browse Folders\nSSH: getFolders]
    B --> C{Create New\nFolder?}
    C -- Yes --> D[SSH: mkdir]
    C -- No --> E[Set projectPath]
    D --> E
    E --> F[Select Source Folder\nopenSendFile]
    F --> G[Start Transaction\nstartTx — Insert DB]
    G --> H[Create Remote Directory\nSSH: mkdir]
    H --> I[Start File Watching\nsendFiles]
    I --> J[Chokidar Watcher\nadd · addDir · change]
    J --> K{rsyncActive?}
    K -- Yes --> L[Set rsyncChanged = true]
    K -- No --> M[Check Rsyncd Running\nsshSession.checkRsyncdRunning]
    M --> N[rsync.rsync\n--partial-dir or --append-verify]
    N --> O[Monitor Progress\nXTerm Console Output]
    O --> P[Transfer Complete\nfinishTx]
    P --> Q[Update DB\nstatus = completed]
    Q --> R{rsyncChanged?}
    R -- Yes --> S[Restart Rsync]
    S --> N
    R -- No --> T([Done])
```

---

### 3. Transaction Management Flow

```mermaid
flowchart TD
    A([Transfer Initiated]) --> B["startTx()\nINSERT into tx table\nstatus = transfer\nserverIp = selected server"]
    B --> C[Files Transferring...]
    C --> D["finishTx()\nUPDATE status = completed"]
    D --> E[On Next Login\ncheckUnfinishedTx]
    E --> F{Unfinished Record Found\nfor current serverIp?}
    F -- No --> G([Ready])
    F -- Yes --> H[Show Resume Dialog]
    H --> I{User Choice}
    I -- Yes --> J[Resume: set filename\nprojectPath → sendFiles]
    I -- No --> K([Skip])
    I -- Cancel --> L["cancelTx()\nUPDATE status = cancelled"]
```

---

### 4. Logout Process

```mermaid
flowchart TD
    A([Click Logout]) --> B{rsyncActive?}
    B -- Yes --> C[Kill Rsync Client\nrsync.killProcess]
    B -- No --> D[Kill Rsync Daemon\nSSH: killRsyncd]
    C --> D
    D --> E[Stop File Watcher\nwatcher.close]
    E --> F[Clear State\nuserPassword · projectPath\nfilename · apikey · remoteFolders]
    F --> G([connected = false\nStatus: logged-out])
```

---

### 5. File Watching Details

```mermaid
flowchart TD
    A([Start Chokidar Watcher]) --> B["Configuration\nIgnored: **/_temp/** · **/_TMP*/**\ndepth: 2000\nawaitWriteFinish: 2000ms / 100ms"]
    B --> C{File System Event}
    C -- addDir --> D[Log filepath + stats]
    C -- add --> D
    C -- change --> D
    D --> E{rsyncActive?}
    E -- True --> F[Set rsyncChanged = true\nQueue next sync]
    E -- False --> G[Set rsyncActive = true\nTrigger Rsync Transfer]
```

---

### 6. Rsync Transfer Options

```mermaid
flowchart LR
    A([User Selects Transfer Mode]) --> B{Mode}
    B -- Default --> C["--partial-dir=.rsync-partial\n\nUse for: ND · ND2 · ZIP · any files\nBehavior: retransfer whole file\nif size changed"]
    B -- Incremental --> D["--append-verify\n\nUse for: Big Tiff\nBehavior: transfer only increased\nportion + checksum verification"]
```

---

## Key Components Integration

### Database (tx.db)
- **Table**: `tx` (transactions)
- **Fields**: id, encPass, src, dest, status, created, updated, serverIp
- **Statuses**: `transfer`, `completed`, `cancelled`
- **Purpose**: Track transfer sessions per server and enable resume functionality
- **Multi-server**: Each transaction row stores the `serverIp` it belongs to. `checkUnfinishedTx` only prompts for rows matching the currently selected server.
- **Migration**: On first run after upgrade, the `serverIp` column is added via `ALTER TABLE`. Existing rows are backfilled to `fileserver-ssh.mpi-cbg.de`.

### SSH Session Module
- **Functions**:
  - `mkdir()` — Create remote directories
  - `getFolders()` — List remote folders
  - `checkRsyncdRunning()` — Verify rsync daemon status
  - `getRemoteAvailablePort()` — Get available port for rsyncd
  - `createRsyncConf()` — Configure rsync daemon
  - `startRsyncd()` — Start rsync daemon on server
  - `killRsyncd()` — Stop rsync daemon

### Rsync Module
- **Functions**:
  - `rsync()` — Remote file transfer
  - `rsyncLocal()` — Local file transfer
  - `killProcess()` — Stop active transfer
  - `rsyncVersion()` — Check rsync version

### LDAP Authentication
- **Server**: ldap-srv1
- **Base DN**: `dc=ldap-srv1,dc=mpi-cbg,dc=de`
- **User DN**: `cn={user},cn=users,dc=ldap-srv1,dc=mpi-cbg,dc=de`
- **Group Search**: `cn=groups,dc=ldap-srv1,dc=mpi-cbg,dc=de`
- **Filter**: `(memberUid={user})`

---

## State Management

### Key Reactive Variables

```mermaid
graph LR
    subgraph Auth
        localMode
        connected
        userId
        userPassword
        apikey
        givenPort
    end
    subgraph Transfer
        rsyncActive
        rsyncChanged
        rsyncOption
        filename
        projectPath
        watcher
    end
    subgraph UI
        remoteFolders
        status
        isError
    end
```

---

## User Interface Panels

```mermaid
graph TD
    App([Scion App]) --> LP[Login Panel]
    App --> TP[Transfer Panel]
    App --> CP[Console Panel]

    LP --> LP1[Server Dropdown\nfileserver · korok]
    LP --> LP2[User Credentials]
    LP --> LP3[Connect / Logout Buttons]
    LP --> LP4[Status Display]

    TP --> TP1[Transfer Options\nDefault / Incremental]
    TP --> TP2[Destination Folder Browser]
    TP --> TP3[Source Folder Selector]
    TP --> TP4[Upload / Stop Buttons]

    CP --> CP1[XTerm Terminal]
    CP --> CP2[Real-time Transfer Logs]
    CP --> CP3[Progress Monitoring]
```

---

## Error Handling

```mermaid
flowchart TD
    E([Error Occurs]) --> F{Error Type}
    F -- Auth Failure --> A[Display LDAP\nerror message]
    F -- Missing Project Path --> B[Show: Select project\npath first]
    F -- Not Logged In --> C[Show: Please\nlogin first]
    F -- File System Error --> D[Log to console]
    F -- Transfer Interrupted --> G[Mark status = transfer\nin DB for resume]
```

