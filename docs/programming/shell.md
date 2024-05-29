# Shell

## Linux



## Windows

校验文件

```powershell
certutil -hashfile [filename] SHA256
```

批量校验文件

- cmd

```cmd
for %f in (*) do @certutil -hashfile "%f" SHA256 | findstr /v "CertUtil"
```

- PowerShell

```powershell
Get-ChildItem | ForEach-Object { certutil -hashfile $_.FullName SHA256 | Select-String -Pattern "SHA256" -Context 0,1 }
```
