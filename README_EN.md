# **Getting Started with chezmoi**

## **What Are Dotfiles?**

Dotfiles are configuration files that control how various programs and tools behave on your system. Typically, these files start with a dot (`.`), such as `.gitconfig` or `.bashrc`, and are hidden by default. 

These files hold personalized settings for tools like your shell, editor, and terminal. If you use multiple devices, syncing these files ensures that your environment remains consistent across all of them.

> **Tip:** Utilities like **chezmoi** help you manage, sync, and install your dotfiles. You can explore more tools for dotfile management at [dotfiles utilities](https://dotfiles.github.io/utilities/).

## [**Basic Workflow**](https://www.chezmoi.io/quick-start/)

To get started with `chezmoi`, follow these essential steps:

---

### **1. Initialize chezmoi**  
Set up **chezmoi** to manage your dotfiles by initializing a local repository:  

```bash  
chezmoi init  
```  

- On **Windows**, this will create a directory at `%USERPROFILE%\AppData\Local\chezmoi` to store your dotfiles.
- On **Linux**, the directory will typically be located at `~/.local/share/chezmoi`.

---

### **2. Navigate to chezmoi’s Source Directory**  
To manage your dotfiles directly or interact with Git, access chezmoi's source directory:

```bash
chezmoi cd
```

This will open the directory where chezmoi stores your dotfiles. From here, you can:
- View or edit the raw dotfiles.
- Use Git to version-control your changes (e.g., `git add`, `git commit`).

---

### **3. Add a Dotfile**  
To start managing a file with chezmoi, use the `add` command:

```bash  
chezmoi add ~/.gitconfig  
```  

- On **Linux**, this command copies `~/.gitconfig` into chezmoi’s directory as `dot_gitconfig`.
- On **Windows**, you can either specify the full path or use the `~` shorthand:
  ```bash  
  chezmoi add C:\Users\<YourUsername>\.gitconfig  
  ```  
  or  
  ```bash  
  chezmoi add ~/.gitconfig  
  ```  
  This stores the file as `dot_gitconfig` in chezmoi’s source directory.

---

### **4. Preview Changes**  
Before applying changes to your system, preview the changes with:

```bash  
chezmoi diff  
```

---

### **5. Apply Changes**  
To update your system with the managed dotfiles, use:

```bash  
chezmoi -v apply  
```  

The `-v` (verbose) flag provides detailed logs of the changes being made. For a simpler execution, you can use:

```bash  
chezmoi apply  
```

---

### **6. Version-Control Your Dotfiles**  
Once inside the chezmoi source directory (`chezmoi cd`), you can use Git to version-control your dotfiles:

```bash
git add .
git commit -m "Track .gitconfig changes"
```

## [**Templating**](https://www.chezmoi.io/user-guide/templating/#template-data)

Templates in chezmoi allow you to dynamically adjust file contents based on the environment. For example, you might want different configurations on different machines depending on the hostname.

### **Template Data**

chezmoi provides several built-in template variables that you can use in your files. To see a list of predefined variables, run:

```bash
chezmoi data
```

These variables come from different sources, and their precedence order is as follows:
1. **Variables in `.chezmoi`**, e.g., `.chezmoi.os`
2. **Variables defined in the `[data]` section of the `chezmoi.toml` configuration file**

### **Editing the `chezmoi.toml` Configuration File**

To create or modify the `chezmoi.toml` file, use the `chezmoi edit-config` command:

```bash
chezmoi edit-config
```

This command opens the `chezmoi.toml` file in your default editor, where you can add custom data as shown below.

### **Using the `[data]` Section in `chezmoi.toml`**

In the `[data]` section of `chezmoi.toml`, you can define custom variables for use in your templates. Here’s an example of how to set them up:

```toml
[data]
email = "foo@foo.com"
name = "foo"
others = "bla bla"
```

These values can now be referenced within your templates.

### **Creating a Template File**

When you add a new file to chezmoi, you can mark it as a template by using the `--template` flag:

```bash
chezmoi add --template ~/.gitconfig
```

If the file is already managed by chezmoi but you want to convert it into a template, use the `chattr` command:

```bash
chezmoi chattr +template ~/.gitconfig
```

This will treat the file as a template, allowing you to use the variables defined in your `chezmoi.toml` file or other template data sources.

---

## [**Encryption**](https://www.chezmoi.io/user-guide/frequently-asked-questions/encryption/)

We will configure **chezmoi** to encrypt sensitive files while ensuring that you only need to provide a passphrase the first time you run `chezmoi init`. We'll use **age**, a simple and secure encryption tool.

#### 1. **Generate an Age Private Key**
First, generate a new **age** private key encrypted with a passphrase. Run the following command in your local repository:

```bash
age-keygen | age --armor --passphrase > key.txt.age
```

- You’ll be prompted to enter a **passphrase** (ensure it's strong). This passphrase is used to encrypt your private key.
- After this process, note the **public key** (e.g., `age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl`), which will be used in the configuration.

#### 2. **Ignore the Encrypted Key**  
To prevent chezmoi from managing the encrypted key, add `key.txt.age` to the `.chezmoiignore` file:

```bash
echo key.txt.age >> .chezmoiignore
```

#### 3. **Decrypt the Private Key When Needed**  
Create a script that will decrypt your private key when chezmoi is initialized. This script will only run once as needed.

**For Linux/Unix**:  
Use this shell script (`run_once_before_decrypt-private-key.sh.tmpl`):

```bash
cat > run_once_before_decrypt-private-key.sh.tmpl <<EOF
#!/bin/sh

if [ ! -f "${HOME}/.config/chezmoi/key.txt" ]; then
    mkdir -p "${HOME}/.config/chezmoi"
    chezmoi age decrypt --output "${HOME}/.config/chezmoi/key.txt" --passphrase "{{ .chezmoi.sourceDir }}/key.txt.age"
    chmod 600 "${HOME}/.config/chezmoi/key.txt"
fi
EOF
```

This script ensures that the private key is decrypted only when needed and stored securely in `~/.config/chezmoi/key.txt`.

**For Windows (PowerShell)**:  
On Windows, the process differs. Use this PowerShell script (`run_once_before_decrypt-private-key.ps1.tmpl`):

```powershell
# Define paths for the home directory and key files
$homePath = [Environment]::GetFolderPath("UserProfile")
$keyFilePath = Join-Path -Path $homePath -ChildPath ".config\chezmoi\key.txt"
$encryptedKeyPath = Join-Path -Path (chezmoi source-path) -ChildPath "key.txt.age"

# Check if the key file exists
if (!(Test-Path -Path $keyFilePath)) {
    # Create the .config/chezmoi directory if it doesn't exist
    $configPath = Join-Path -Path $homePath -ChildPath ".config\chezmoi"
    if (!(Test-Path -Path $configPath)) {
        New-Item -ItemType Directory -Path $configPath -Force | Out-Null
    }

    # Run chezmoi to decrypt the age-encrypted file
    chezmoi age decrypt --output $keyFilePath --passphrase $encryptedKeyPath
    
    # Set permissions: Read and Write access only for the current user
    $acl = New-Object System.Security.AccessControl.FileSecurity
    $accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule(
        $env:UserName, "Read, Write", "Allow"
    )
    $acl.AddAccessRule($accessRule)
    Set-Acl -Path $keyFilePath -AclObject $acl
}
```

This PowerShell script checks if the decrypted private key exists at `~/.config/chezmoi/key.txt` (or the equivalent path on Windows). If it doesn't, the script decrypts it from `key.txt.age` using **chezmoi** and adjusts the permissions to ensure only the current user has access.

#### 4. **Configure chezmoi to Use Encryption**
Update the `chezmoi.toml` file to

 specify the encryption settings. Your configuration should look like this:

```toml
encryption = "age"
[age]
    identity = "~/.config/chezmoi/key.txt"
    recipient = "age193wd0hfuhtjfsunlq3c83s8m93pde442dkcn7lmj3lspeekm9g7stwutrl"
```

This tells chezmoi to use **age** encryption, with the private key stored at `~/.config/chezmoi/key.txt` and the public key for encryption.

**Note:** Make sure `encryption` is added to the top level section at the beginning of the config, before any other sections.

#### 5. **Initialize chezmoi**
After configuring encryption, run `chezmoi init` to initialize and decrypt the private key:

```bash
chezmoi init --apply
```

You’ll be prompted to enter your passphrase to decrypt `key.txt.age`. The private key will then be stored in `~/.config/chezmoi/key.txt`.

#### 6. **Verify the Status**
Run `git status` to check the status of the files:

```bash
git status
```

You should see untracked files such as:

```
Untracked files:
  (use "git add <file>..." to include in what will be committed)
    .chezmoi.toml.tmpl
    .chezmoiignore
    key.txt.age
    run_once_before_decrypt-private-key.sh.tmpl
```

#### 7. **Add Encrypted Files**
To add files for encryption with chezmoi, use the `--encrypt` flag. For example, to encrypt an SSH private key:

```bash
chezmoi add --encrypt ~/gitconfig
```

### **How It Works on a New Machine**

When you run `chezmoi init` on a new machine:
- You will only need to enter your passphrase **once** to decrypt `key.txt.age`.
- The decrypted private key will be stored in `~/.config/chezmoi/key.txt`.

After that, files encrypted by chezmoi will be automatically managed, and you won’t need to re-enter the passphrase unless the key is deleted or the machine is re-initialized.