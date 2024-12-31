# My Journey with AWS S3 Presigned URLs

## 🎯 What I Set Out to Learn
I wanted to figure out how to share private S3 files securely without making my whole bucket public. This project taught me how to use presigned URLs, which are like temporary passes to access specific files.

## 🛠 Tools I Used
- AWS Console
- AWS CLI
- S3 Bucket with private files
- A web browser for testing

## 🎓 What I Learned

### Two Ways to Create Presigned URLs
I discovered there are two ways to create these special URLs:
1. Through AWS Console (great for beginners!)
2. Using AWS CLI (perfect for automation)

### Using AWS Console (The Easy Way)
Here's what I figured out:
1. Go to S3 and find your file
2. Check the box next to the file
3. Click Actions → Share with presigned URL
4. Choose how long you want the link to work (up to 12 hours)
5. Click Create presigned URL
<img width="665" alt="image" src="https://github.com/user-attachments/assets/dec2669a-b7dc-4c0d-8cfe-d4d8b8116d50" />
<img width="657" alt="image" src="https://github.com/user-attachments/assets/1a12c0d2-55e5-4061-89cc-a625563a208c" />

**Pro Tip I Learned:** The URL gets copied automatically - super handy!

### Using AWS CLI (The Power User Way)
First, I set up my AWS profile:
```bash
aws configure --profile s3
# Add your access key and secret key
# Use us-east-1 as region
```

To create a presigned URL with CLI:
```bash
aws s3 presign s3://your-bucket-name/your-file.jpg \
    --expires-in 900 \
    --profile s3
```
<img width="628" alt="image" src="https://github.com/user-attachments/assets/dbfb5650-6835-47fa-9c23-2ece98008bfc" />

**Cool Discovery:** With CLI, you can set expiration up to 7 days (way longer than console's 12 hours)!

## 🚨 Challenges I Faced

### 1. Expiration Times
- **Problem:** I wasn't sure how long to set the expiration
- **Solution:** Started with 15 minutes for testing, then adjusted based on needs
- **Lesson:** Shorter times are safer but less convenient

### 2. URL Length
- **Problem:** The URLs were super long and messy
- **Solution:** Used URL shorteners for sharing (but kept original for reference)
- **Lesson:** Long URLs are actually good - they contain important security info

### 3. Access Management
- **Problem:** Worried about who could access my files
- **Solution:** Started logging URL creation and using shorter expiration times
- **Lesson:** Treat presigned URLs like temporary passwords

## 💡 Best Practices I Developed

1. **Always Set Expiration Times**
   - Don't use default times
   - Match the duration to your needs
   - Shorter is safer

2. **Track Your URLs**
   - Keep notes on which URLs you've created
   - Monitor who you've shared them with
   - Remember when they expire

3. **Test Before Sharing**
   - Always test URLs before sending
   - Check in a different browser
   - Verify file downloads correctly

## 🔒 Security Tips I Learned

1. Don't use presigned URLs for super sensitive data
2. Keep expiration times as short as possible
3. Be careful who you share URLs with
4. Remember - anyone with the URL can access the file

## 🎉 Cool Things You Can Do

- Share private files without changing bucket permissions
- Create temporary access to files
- Automate file sharing with CLI commands
- Control exactly how long someone can access a file

## 📝 Key Takeaways

1. Presigned URLs are super useful but need careful handling
2. CLI offers more flexibility than console
3. Security requires balance with convenience
4. Always think about who might get the URL


## 🤔 Final Thoughts
This project really opened my eyes to secure file sharing in AWS. While presigned URLs are powerful, they need to be used responsibly. The biggest lesson? Always think about security first, convenience second!
