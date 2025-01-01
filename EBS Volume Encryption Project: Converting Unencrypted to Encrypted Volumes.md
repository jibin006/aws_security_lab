# EBS Volume Encryption Project: Converting Unencrypted to Encrypted Volumes

## Project Overview
I implemented a secure method to encrypt existing unencrypted EBS volumes on running EC2 instances. This project was crucial for improving our data security posture in AWS.

## The Challenge
I needed to encrypt an already running production EC2 instance's EBS volume without losing any data. The main catch? AWS doesn't allow direct encryption of existing volumes!

## Step-by-Step Implementation

### 1. Initial Assessment
First, I had to find all unencrypted volumes:
<img width="603" alt="image" src="https://github.com/user-attachments/assets/f27712a5-ade7-44f5-8f3a-d520564dece1" />

- Used EC2 console to check the "Encryption" column
<img width="616" alt="image" src="https://github.com/user-attachments/assets/243f9493-1475-4106-89dd-9b1c7da5bebb" />

- Found volumes marked as "Not encrypted"
- Documented the volume IDs and their attached instances

### 2. Volume Backup Strategy
Created backups before making any changes:
- Took snapshots of unencrypted volumes
  <img width="581" alt="image" src="https://github.com/user-attachments/assets/b00b991e-2650-4708-815a-2684447af6e7" />
- Named them clearly for easy tracking
- Waited for snapshots to complete (took about 2-3 minutes)

### 3. Encryption Process
Here's where it got interesting! I had to:
1. Create an encrypted copy of the snapshot
   <img width="545" alt="image" src="https://github.com/user-attachments/assets/d017db39-a074-4e75-9c64-8ef8db114bff" />

   - Used AWS-managed key (aws/ebs) for encryption
   - Kept the same region for simplicity
   - Waited for copy completion (about 2-5 minutes)

3. Create new encrypted volume:
   - Used the encrypted snapshot
   - Matched the original volume's size (8GB)
   - Selected same availability zone as original
   - Made sure encryption was enabled
     <img width="527" alt="image" src="https://github.com/user-attachments/assets/40075d63-b60b-4eba-9022-448df618dcb1" />


### 4. The Tricky Part: Volume Swap
This was the most challenging part. I had to:
1. Stop the EC2 instance
2. Note the device name (/dev/xvda)
3. Detach the old unencrypted volume
<img width="540" alt="image" src="https://github.com/user-attachments/assets/f81c155f-8cb3-4bfc-8529-a8f650746d46" />
<img width="552" alt="image" src="https://github.com/user-attachments/assets/0ac02b74-6f8f-4f77-8d17-02f08ca314dd" />

5. Attach new encrypted volume
   <img width="506" alt="image" src="https://github.com/user-attachments/assets/6c0303b6-ecd3-4ca3-82fa-c4428d1e5cd3" />
   <img width="406" alt="image" src="https://github.com/user-attachments/assets/e066fff1-2c3a-4ff7-a94c-a4c579fdb945" />
   <img width="525" alt="image" src="https://github.com/user-attachments/assets/578f03f2-6d52-42f7-88c1-f305e64de7e9" />

7. Start the instance again
    Instance State > Start Instance

## Problems I Faced & Solutions

### Problem 1: Timing Issues
**Challenge:** Snapshot creation and copying took longer than expected
**Solution:** 
- Created a checklist for other tasks to do while waiting
- Used refresh button to check status instead of waiting blindly
- Learned to parallelize tasks when possible

### Problem 2: Device Naming
**Challenge:** Had to make sure the new volume used exactly the same device name
**Solution:**
- Documented the original device name before detaching
- Double-checked before attaching the new volume
- Verified mount points after restart

### Problem 3: Data Verification
**Challenge:** Needed to verify all data was intact
**Solution:**
- Used `df -h` and `lsblk` commands to verify volume attachment
  <img width="533" alt="image" src="https://github.com/user-attachments/assets/33090c3e-2390-450a-a5e9-7993c78cdbad" />

- Checked file systems and mount points
- Documented everything for future reference

## Key Learnings

1. **Always Have Backups:**
   - Never start encryption without snapshots
   - Keep original volumes until verification is complete

2. **Document Everything:**
   - Device names
   - Volume IDs
   - Snapshot IDs
   - Commands used

3. **Time Management:**
   - Plan for AWS operation delays
   - Use waiting time efficiently
   - Have a rollback plan ready

## Best Practices I Discovered

1. Enable EBS encryption by default for new volumes
2. Use proper naming conventions for snapshots and volumes
3. Test the process on non-production instances first
4. Always verify mount points after volume swap
5. Clean up unused resources to save costs

## Future Improvements

1. Automation ideas:
   - Create Lambda function for automated encryption
   - Set up CloudWatch events for monitoring
   - Build automated verification scripts

2. Process enhancements:
   - Add automated backup before starting
   - Include notification system
   - Create rollback automation

## Tools Used
- AWS EC2 Console
- AWS EBS
- AWS KMS
- Linux commands (df, lsblk)
- EC2 Instance Connect

## Conclusion
This project taught me the importance of careful planning when handling storage encryption. While AWS doesn't allow direct encryption of volumes, having a systematic approach makes the process manageable and safe.

The most valuable lesson? Always test, document, and verify each step. What seems like a simple volume swap can have many moving parts!
