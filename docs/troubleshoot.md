## Troubleshooting

### SSH Host Key Verification (First-Time Setup)

When using Scion for the first time, you **must** manually SSH into the target server from a terminal before Scion can connect. This is required for two reasons:

1. **SSH host key verification** -- SSH requires you to accept the server's host key on first contact, and Scion cannot handle this interactive prompt.
2. **User home folder creation** -- your home directory on the server is created automatically on your first SSH login. Without it, Scion will not be able to browse or transfer files to the server.

#### macOS / Linux

Open a terminal and run one of the following, depending on which server you plan to use:

```bash
# For fileserver
ssh your-username@fileserver-ssh.mpi-cbg.de

# For korok
ssh your-username@korok.mpi-cbg.de
```

When prompted with:

```
The authenticity of host 'fileserver-ssh.mpi-cbg.de (...)' can't be established.
...
Are you sure you want to continue connecting (yes/no)?
```

Type **yes** and press Enter. You can then close the SSH session. After this, Scion will be able to connect to the server without issues.

#### Windows

Open **PowerShell** or **Command Prompt** and run:

```bash
# For fileserver
ssh your-username@fileserver-ssh.mpi-cbg.de

# For korok
ssh your-username@korok.mpi-cbg.de
```

Accept the host key when prompted. If `ssh` is not available, install [OpenSSH for Windows](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse) or use [PuTTY](https://www.putty.org/) to connect to the server once.


### Connection Fails After First Login

If Scion shows a connection error even after accepting the SSH host key:

1. **Check your credentials** -- make sure the username and password are correct.
2. **Check network access** -- ensure you are on the MPI-CBG network or connected via VPN.
3. **Check server availability** -- the server may be temporarily down for maintenance.


### Transfer Stalls or Fails

- **Large files**: rsync may appear to stall on very large files. Wait a moment before stopping the transfer.
- **Disk space**: verify that the destination has enough free space.
- **Network interruption**: if the network drops during transfer, Scion will resume from where it left off on the next attempt thanks to rsync's incremental transfer.


### Reporting Issues

If none of the above resolve your problem, please contact **scicomp@mpi-cbg.de** or open an issue on [GitHub](https://github.com/mpicbg-scicomp/scion/).
