# Set Security Permissions
+ Thư mục wwwroot của ASP.NET Core
+ Thư mục wwwroot/Logs
+ Thư mục wwwroot/UploadFiles
+ IWebHostEnvironment
+ IHostingEnvironment
+ Static Files Middleware
+ IFormFile
+ IOException
+ SecurityException

## Cần lưu ý gì về quyền đọc/ghi tệp cho ứng dụng ASP.NET và ASP.NET Core
+ https://docs.google.com/document/d/10_x_WzoVUbFtXYkAFlBD7LdVXX5EEi7c1eLD3Pc788U

## Chỉ tôi cách set quyền cho Application Pool Identity hoặc IIS_IUSRS
+ https://docs.google.com/document/d/1QAvE3WOy5U48AjBtLcO_MgGAccTJMUYxJcjJxDMdo0Y

```
# Path to the application's root directory (e.g., C:\inetpub\wwwroot\MyWebApp)
$appRoot = "C:\inetpub\wwwroot\YourAspNetCoreApp"

# Your Application Pool name. This usually matches the website name in IIS.
$appPoolName = "YourAspNetCoreApp"

# The Application Pool Identity account
$appPoolIdentity = "IIS AppPool\" + $appPoolName

# Full paths to the directories that need permissions
$logsPath = Join-Path -Path $appRoot -ChildPath "wwwroot\logs"
$uploadPath = Join-Path -Path $appRoot -ChildPath "wwwroot\UploadFiles"

# Check and create the directories if they do not exist
if (-not (Test-Path $logsPath)) { New-Item -ItemType Directory -Path $logsPath }
if (-not (Test-Path $uploadPath)) { New-Item -ItemType Directory -Path $uploadPath }

# Get the current ACL of the directories
$aclLogs = Get-Acl $logsPath
$aclUploads = Get-Acl $uploadPath

# Create an Access Rule for the Application Pool account, granting Read and Write permissions
# This is the minimum necessary permission. "Modify" would also grant the right to delete files/directories.
$permission = "Read,Write"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule($appPoolIdentity, $permission, "ContainerInherit,ObjectInherit", "None", "Allow")

# Add the rule to the ACL
$aclLogs.AddAccessRule($rule)
$aclUploads.AddAccessRule($rule)

# Apply the permission changes
Set-Acl -Path $logsPath -AclObject $aclLogs
Set-Acl -Path $uploadPath -AclObject $aclUploads

Write-Host "Granted '$permission' permissions to the '$appPoolIdentity' account on directories '$logsPath' and '$uploadPath'."
```

## Tìm hiểu về Logging trong ứng dụng web ASP.NET Core trong 15 phút
+ https://www.youtube.com/watch?v=64TaISeOc5k
