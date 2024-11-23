# semaphoreStore


## SSH 安全登錄教學

如何使用 SSH 金鑰進行安全登錄，並包含 `ssh-copy-id` 工具的使用方法。

### 1. 產生 SSH 金鑰

使用 `ssh-keygen` 命令產生 SSH 金鑰對。建議使用 Ed25519 算法，安全性更高。

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

* `-t ed25519`: 指定使用 Ed25519 算法。
* `-C "your_email@example.com"`:  添加註釋，方便識別金鑰。

過程中會提示你輸入儲存金鑰的路徑和密碼，可以直接按 Enter 鍵使用預設值。

**注意：**  務必妥善保管你的私鑰 (`~/.ssh/id_ed25519`)，切勿與他人分享。

### 2. 將公鑰複製到遠端主機

#### 方法一：使用 `ssh-copy-id`

`ssh-copy-id` 可以自動將你的公鑰複製到遠端主機的 `authorized_keys` 文件中。

```bash
ssh-copy-id username@remote_host
```

* `username@remote_host`:  遠端主機的用戶名和地址。

**注意：**  第一次使用 `ssh-copy-id`  連接遠端主機時，會要求你輸入密碼。

#### 方法二：手動複製

1.  使用 `cat ~/.ssh/id_ed25519.pub` 命令顯示公鑰內容。
2.  登錄到遠端主機。
3.  確保 `.ssh` 目錄存在，若不存在則創建： `mkdir -p ~/.ssh`
4.  將公鑰內容添加到 `authorized_keys` 文件中： `echo "<公鑰內容>" >> ~/.ssh/authorized_keys`
5.  設定 `authorized_keys` 文件的權限： `chmod 600 ~/.ssh/authorized_keys`

### 3. 設定 SSH 客戶端

可以使用 `~/.ssh/config` 文件為不同的主機設定特定的 SSH 連接參數。

```
Host gitlab.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519

Host my_server
  HostName long.domain.com
  User my_username
  IdentityFile ~/.ssh/id_rsa.my_server
```

* `Host`:  指定主機名或域名，可以設定別名。
* `PreferredAuthentications publickey`:  優先使用金鑰驗證。
* `IdentityFile`:  指定私鑰文件路徑。
* `HostName`:  指定真實主機名或 IP 地址。
* `User`:  指定登錄遠端主機的用戶名。

### 4.  使用 SSH 金鑰登錄

完成以上步驟後，你就可以使用 SSH 金鑰登錄遠端主機了。

```bash
ssh username@remote_host
```

如果設定了 SSH config 文件，可以使用別名登錄：

```bash
ssh my_server
```

### 5.  其他注意事項

*  **多個金鑰：** 你可以在遠端主機的 `authorized_keys` 文件中添加多個公鑰，每個公鑰佔一行。
*  **金鑰管理：**  妥善保管你的私鑰，可以使用密碼管理器或其他安全工具。
*  **SSH 代理：**  SSH 代理可以將你的私鑰密碼儲存在記憶體中，避免重複輸入。
*  **安全性：**  定期檢查你的 `authorized_keys` 文件，確保只有授權的金鑰可以訪問。
*  **相容性：**  有些舊版本的 SSH 服務器可能不支持 Ed25519 算法，可以使用 RSA 或其他算法。