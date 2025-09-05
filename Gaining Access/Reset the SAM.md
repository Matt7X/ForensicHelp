
# Reset the SAM Database to Access a Locked VM
### This is specific to Hyper-V but similar process applies to VMWare

## Method 1: Offline Password Reset via VHD Mounting

### Steps:

1. **Shut down the locked VM completely** from Hyper-V Manager

2. **Create or use a helper VM:**
   - Use an existing Windows VM or create a minimal one
   - This VM needs to be running and accessible

3. **Attach the locked VM's VHD to the helper VM:**
   - In Hyper-V Manager, right-click the helper VM → Settings
   - Select "SCSI Controller" or "IDE Controller" 
   - Click "Add" → "Hard Drive" → "Browse"
   - Navigate to and select the locked VM's .vhd/.vhdx file
   - Apply the changes

4. **Access the mounted drive in the helper VM:**
   - The locked VM's system drive will appear as an additional drive (e.g., E:\)
   - Navigate to: `E:\Windows\System32\config\`

5. **Replace utilman.exe with cmd.exe:**
   ```
   cd E:\Windows\System32\
   ren utilman.exe utilman.exe.bak
   copy cmd.exe utilman.exe
   ```

6. **Detach the VHD:**
   - Safely remove the drive from within the helper VM
   - Remove the VHD from the helper VM's settings in Hyper-V

7. **Boot the locked VM:**
   - Start the previously locked VM
   - At the login screen, press Windows+U (or click Ease of Access)
   - This will open Command Prompt with SYSTEM privileges

8. **Reset the Administrator password:**
   ```
   net user Administrator NewPassword123!
   net user Administrator /active:yes
   ```

9. **Restore utilman.exe:**
   ```
   cd C:\Windows\System32\
   ren utilman.exe cmd.exe
   ren utilman.exe.bak utilman.exe
   ```

## Method 2: Using Windows Installation Media

If you prefer not to mount VHDs:

1. **Mount Windows installation ISO to the locked VM**
2. **Boot from the ISO** (change boot order if needed)
3. **Press Shift+F10** when the installer loads to open Command Prompt
4. **Identify the Windows drive** (usually C: or D:)
5. **Use the same utilman.exe replacement technique**

## Important Security Notes:

- **Document this recovery action** in your security incident log
- **After regaining access:**
  - Reset all domain user passwords
  - Review AD security logs for the breach timeline
  - Check for any persistent backdoors the attacker may have created
  - Consider forcing a domain-wide password reset
  - Review and harden AD account lockout policies
  - Implement MFA if not already in place

This is a standard emergency recovery procedure. Make sure to follow your organization's incident response procedures and consider involving your security team to investigate how the brute force attack succeeded.
