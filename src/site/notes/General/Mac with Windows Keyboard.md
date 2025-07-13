---
{"dg-metatags":{"description":"MacOS with Windows External keyboard","title":"Mac with Windows Keyboard","og:title":"Mac with Windows Keyboard","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["general","MacOS","Windows","ExternalKeyboard","Keyboard","Linux","Ubuntu"],"og:article:section":"Technology"},"dg-publish":true,"comments":true,"tags":["general","MacOS","Windows","ExternalKeyboard","Keyboard","Linux","Ubuntu"],"permalink":"/general/mac-with-windows-keyboard/","metatags":{"description":"MacOS with Windows External keyboard","title":"Mac with Windows Keyboard","og:title":"Mac with Windows Keyboard","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["general","MacOS","Windows","ExternalKeyboard","Keyboard","Linux","Ubuntu"],"og:article:section":"Technology"},"dgPassFrontmatter":true}
---


**How to Use One External Keyboard Seamlessly with Both macOS and Ubuntu**

If you're someone like me who uses **macOS for work** and **Ubuntu Linux for personal projects**, you've probably faced a common annoyance — **keyboard layout differences** between the two systems. It becomes especially frustrating when trying to use a **single external keyboard** across both platforms.

Good news — there's a simple fix!

### 💡 The Hack to Make macOS and Ubuntu Keyboard Layouts Compatible

While browsing for solutions, I stumbled upon this helpful [YouTube tutorial](https://www.youtube.com/watch?v=puedY8fciy0) that saved me a ton of time.

The main issue usually lies with the **Command (⌘)** and **Option (⌥)** keys on macOS, which don’t align with the **Alt** and **Super** (Windows) keys on Linux-based systems. The trick is to **remap modifier keys** in macOS to mimic the Ubuntu layout.

### 🔧 Steps to Remap Keys on macOS:

1. Open **System Settings**
    
2. Navigate to **Keyboard > Keyboard Shortcuts**
    
3. Click on **Modifier Keys**
    
4. Swap the **Option** and **Command** keys
    

![Screenshot 2025-07-13 at 20.04.00.png](/img/user/img/Screenshot%202025-07-13%20at%2020.04.00.png)

Once you do this, your keyboard will behave more consistently across both systems — no need to mentally remap keys every time you switch!

### 🔁 Want to Revert the Changes?

If you ever want to reset your keyboard to its default configuration, simply delete this preference file:

```bash
/Library/Preferences/com.apple.keyboardtype.plist
```

And you're back to a fresh setup!

---
**Pro Tip**: Use **one external keyboard for both macOS and Linux** without confusion — a game-changer for dual-OS users and developers.

---
