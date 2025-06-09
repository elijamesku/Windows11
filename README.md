# Windows 11 Upgrade

## Objective

Update the existing `Production Group - Update Ring` policy in Intune to:

- Upgrade eligible Windows 10 devices to Windows 11 automatically  
- Continue managing update behavior for devices already on Windows 11  
- Eliminate the need for manual update calls or remote sessions  


## Current Policy Behavior

**Policy Name:** `Production Group - Update Ring`

| Setting                                      | Current Value           |
|---------------------------------------------|--------------------------|
| Upgrade Windows 10 to Windows 11            | No (disabled)            |
| Quality Update Deferral                     | 10 days                  |
| Feature Update Deferral                     | 10 days                  |
| Servicing Channel                           | General Availability     |
| Drivers & Microsoft Product Updates         | Allowed                  |
| Active Hours                                 | 8 AM – 5 PM              |
| Auto Reboot                                  | Enabled                  |
| Notifications                                | Default                  |
| Assignment Group                             | AADJ - Devices           |
| Conflicts/Errors                             | None major (15 minor)    |


## What It's Doing Now

- Keeps Windows 10 devices updated with quality/feature/driver updates  
- Blocks upgrade from Windows 10 to Windows 11  
- Manages Windows 11 devices normally (no reinstall, updates continue)


## Proposal: Modify Existing Update Ring

### 1. Enable Windows 11 Upgrades

- `Devices > Windows > Update rings for Windows 10 and later`
- `Production Group - Update Ring`
- Change the following setting:  
  `Upgrade Windows 10 devices to Latest Windows 11 release` from **No** to **Yes**
- Save the policy

### 2. Add a Filter to Target Only Windows 10 Devices

To avoid reapplying upgrade logic to devices already on Windows 11:

**Create the Filter:**

- Name: `Windows 10 Only`  
- Platform: Windows 10 and later  
- Rule syntax:
  ```kusto
  (device.operatingSystemVersion -startsWith "10.")
  
**Apply the Filter:**

- Edit the assignment on the update ring

**Under the assigned group (AADJ - Devices), set:**

- `Filter Mode: Include`

- `Filter: Windows 10 Only`

## Expected Behavior
**Device Type	Outcome**
- Windows 10 (eligible)	Automatically upgrades to Windows 11
- Windows 10 (ineligible)	Skipped, continues receiving Windows 10 updates
- Windows 11	Remains on 11, continues receiving managed updates

## Benefits
- No manual remote upgrades or user intervention needed

- Consistent update control across Windows 10 and 11

- Future feature and quality updates handled automatically

- Simplifies policy management using a single update ring

## Monitoring and Validation
**Monitor rollout status:**
`Devices > Monitor > Windows update status`

**Check individual OS versions:**
`Devices > Windows > [Device] > Properties > OS Version`

## End
This update automates the upgrade to Windows 11 while continuing to manage patching and update settings for both Windows 10 and Windows 11 devices. It uses the existing update ring and group structure with optional filters to control rollout scope.
