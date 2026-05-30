# SSH: My setup for GitHub SSH access

GitHub Docs documentation:

1. [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=windows)

1. [Working with SSH key passphrases](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/working-with-ssh-key-passphrases?platform=windows)


## Git for Windows SSH

To ensure Git for Windows uses the agent where you've stored your credentials, force Git to use the system's SSH binary by running:

```bash
git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"
```

[link to GitHub Docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=windows#troubleshooting-ssh-agent-conflicts-in-windows)


## Create SSH key

1. Create the key using `ssh-keygen`:

    ```bash
    ssh-keygen -t ed25519 -C "<email_address>"
    ```

    Note: This email address should correspond to the GitHub account's email address.

2. On windows, make sure to set the correct path to store the key, the default path will fail and cause the key files to be stored at `Users/username` folder:

    ```
    C:\Users\user/.ssh/id_ed25519
                 ^    ^
    ```

    Re-enter the correct path when prompted `Enter file in which to save the key (C:\Users\user/.ssh/id_ed25519)`:

    ```
    C:\Users\user\.ssh\id_ed25519
    ```

3. Enter the passphrase twice and the key will be generated.


## Add the SSH public key to GitHub user settings

1. On GitHub's page > Top right user icon (User navigation menu) > Settings > SSH and GPG keys (on the left menu) > New SSH key.

2. Enter a title (e.g. name to identify the device using the SSH key).

3. Make sure Key Type is Authentication Key.

4. Open the public key file (stored in the `.ssh` folder) using a text editor.

    On Windows it is at:

    ```
    %USERPROFILE%\.ssh
    ```
    
    Note: If the key file is named `id_ed25519`, open the `id_ed25519.pub` file.

    **WARNING**: DO NOT copy and paste the private key file (i.e. the `id_ed25519` file).

5. Copy the public key from the text editor and paste it on the Add new SSH key page > Key field.

6. Click Add SSH key.

To test the SSH connection, refer to [instructions on GitHub docs](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection).


## Git repo URL

In the local git repo, point to the remote repo using the following SSH URL format:

```
git@github.com:<repo_owner>/<repo_name>.git
```

If you have more than one SSH key, you should use the SSH `config` file to map host names to the correct keys (refer to [this section](#optional-modify-or-add-the-ssh-config-file-for-multiple-github-users)).


## [Optional] Windows OpenSSH Authentication Agent service

This section is optional. These instructions are useful if the user prefers to store SSH key passphrase using OpenSSH Auth Agent service on Windows (`ssh-agent`).


### Enable the OpenSSH Authentication Agent service


#### Option 1: `services.msc`

1. Open the Services management console via start menu or run.

1. Look for `OpenSSH Authentication Agent`. Open its properties.

1. Change startup type to `Automatic (Delayed Start)`.

1. Click Start to start the service.


#### Option 2: Terminal

1. Open a PowerShell terminal with Administrator privillege.

1. Execute the following:

    ```powershell
    Get-Service -Name ssh-agent | Set-Service -StartupType AutomaticDelayedStart
    Start-Service ssh-agent
    ```


## [Optional] Modify or add the SSH config file for multiple GitHub users

This section is optional. These instructions are useful if a computer requires SSH access on more than one GitHub accounts.

1. Generate the required SSH keys and then add them into the GitHub accounts as described above.

1. Go to the `.ssh` folder:

    ```
    %USERPROFILE%\.ssh
    ```

1. If there is a `config` file, open it.
    
    Otherwise create a new text file and name it `config`.
    
    Copy the contents from [this example file](config). It is an example `config` file for GitHub SSH access with two users.

1. In the `config` file, change the placeholders for username (`user1` and `user2`) and SSH key file (`key1` and `key2`) accordingly.

1. In the git repo, point to the remote repo URL as:

    ```
    gh.<accessing_user>:<repo_owner>/<repo_name>
    ```

    Note: Change the `<accessing_user>` placeholder in the URL to the username which replaced `user1` or `user2`.

    For an example in the URL `gh.alice:mona/test`, `alice` is the user with the SSH key. `mona` is the repo owner. `test` is the repo name.
