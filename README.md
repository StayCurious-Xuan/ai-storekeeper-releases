# AI 店长 Windows 安装与更新

本仓库用于发布 Windows x64 安装文件。

## 0.171.0

- 门店账号和密码登录。
- 组合商品上架后，独立核对组成、数量、单价、分类、展示文案与指定主图。
- 支持分片下载更新，包含完整离线模型。

## 首次安装

从本版 Release 下载以下三个文件到同一空文件夹：

- `AI-Storekeeper-Setup-0.171.0-x64.exe`
- `storekeeper-local-workbench-0.171.0-x64.nsis.7z.part001`
- `storekeeper-local-workbench-0.171.0-x64.nsis.7z.part002`

在该文件夹打开 PowerShell，运行以下命令。它会合并分片、核对完整安装包 SHA512，然后启动安装器；合并与安装另需至少 8 GB 可用空间。

```powershell
$parts = @('storekeeper-local-workbench-0.171.0-x64.nsis.7z.part001', 'storekeeper-local-workbench-0.171.0-x64.nsis.7z.part002')
$output = [IO.File]::Open('storekeeper-local-workbench-0.171.0-x64.nsis.7z', [IO.FileMode]::CreateNew)
try {
  foreach ($part in $parts) {
    $source = [IO.File]::OpenRead((Join-Path (Get-Location).Path $part))
    try { $source.CopyTo($output) } finally { $source.Dispose() }
  }
} finally { $output.Dispose() }
$source = [IO.File]::OpenRead((Join-Path (Get-Location).Path 'storekeeper-local-workbench-0.171.0-x64.nsis.7z'))
$sha = [Security.Cryptography.SHA512]::Create()
try { $actual = [Convert]::ToBase64String($sha.ComputeHash($source)) }
finally { $sha.Dispose(); $source.Dispose() }
if ($actual -ne 'ipdNpTSjpvRg8hLwLlAoUpjmu3xj0LvzJs26JOZ0icQfoOSjGfYKAOBV07tsVPG3seNZlxXKbFP6J3YwaKT1wA==') { throw '安装数据包校验失败，请重新下载。' }
& '.\AI-Storekeeper-Setup-0.171.0-x64.exe'
```

安装器需要与合并后的完整数据包在同一文件夹。

## 软件内更新

分片更新需要客户端 0.171.0 或更高版本。更早版本请先按上述方法安装本版。更新地址为：

`https://github.com/StayCurious-Xuan/ai-storekeeper-releases/releases/latest/download/`

软件会校验各分片和完整安装包，并在确认重启后安装。
