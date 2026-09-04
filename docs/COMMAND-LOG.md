# Command Log

This document records important commands used during the build. It intentionally excludes passwords, PSKs and other secrets.

## AWS identity

```powershell
aws sts get-caller-identity
```

**Purpose:** confirms which AWS IAM identity the CLI is currently using.

Expected identity during this build:

```text
arn:aws:iam::868216907523:user/chandan-admin
```

## AWS default region

```powershell
aws configure get region
```

**Purpose:** confirms the CLI default region.

Expected:

```text
ca-central-1
```

## WorkSpaces inventory

```powershell
aws workspaces describe-workspaces --region ca-central-1
```

**Purpose:** lists Personal WorkSpaces in the selected region.

At the start of the build, the result was an empty WorkSpaces list.

## Directory inventory

```powershell
aws ds describe-directories --region ca-central-1
```

**Purpose:** verifies Directory Service resources and their states.

After deleting the failed connector, the directory list was empty. The connector was later recreated successfully.

## AD account verification

```powershell
Get-ADUser svc_adconnector -Properties Enabled,PasswordNeverExpires |
    Select-Object SamAccountName,Enabled,PasswordNeverExpires
```

**Purpose:** confirms that the AD Connector service account exists and checks the important account properties.

## Secure password prompt

```powershell
$ADPassword = Read-Host "Enter svc_adconnector password" -AsSecureString
```

**Purpose:** accepts the service account password without displaying it in plain text.

The value is not stored in this repository.

## Temporary AWS connectivity test

A temporary EC2 endpoint was created at:

```text
10.20.10.221
```

The test was used only to prove the hybrid routing path and was subsequently terminated.

## Public IP verification

```powershell
Invoke-RestMethod -Uri "https://api.ipify.org"
```

**Purpose:** checks the current public egress IP used when validating the Sophos Customer Gateway address.

The value matched the Customer Gateway address at the time of testing.

## Windows network configuration

```powershell
ipconfig
```

**Purpose:** displays the current IP address, subnet mask and default gateway of the Windows system being tested.

Example FS01 result:

```text
10.10.30.30/24
Gateway 10.10.30.1
```

## Rule

Commands in future documentation should always be accompanied by a one- or two-sentence explanation of what they do.
