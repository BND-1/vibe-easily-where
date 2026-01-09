# Vibe Easily Everywhere: The Complete Guide to Mobile Vibe Coding

Have you ever thought about completing tasks without a computer? I built a complete feature using my phone. "From requirements analysis to code implementation, I didn't open my computer once. The passenger next to me might have thought I was reading an ebook, but I was actually pair programming with Claude Code."

First, let's look at the result, vibe on the subway: ![subway](G:\xwechat_files\wxid_34zwsleyo8u322_6cb4\temp\RWTemp\2026-01\b7fc0dca4752f7f8f3bc2367014b015f\1732b69779bebbd623da4b5e3cdf2d74.jpg)


![Image 2](https://mmbiz.qpic.cn/mmbiz_jpg/onUpicncef3p0TiaicozzLSCfdXd0jax3blTMRYE2XSWZIjVPkr0quuicrU84sRHWY55DfMAJulo5t95vsZhL26rQg/640?wx_fmt=jpeg)



In this article, I want to share how I set up this "Mobile Vibe Coding" workflow.


![Image 3](https://mmbiz.qpic.cn/mmbiz_jpg/onUpicncef3oPgZfkwFLocdRKChRlic3G33WklKm25T9Yciays0E5utLRPmCZn7XrOzaLWydgGQPVBTPBjKJ36gWw/640?wx_fmt=jpeg)


## > Step_01. Why I Need This Workflow

As an AI Product Manager, my work life is split: My days are filled with meetings, aligning requirements with frontend/backend and business teams. The time I can truly focus on writing code or building demos is often limited to evenings and weekends.

But here's the problem: my daily commute adds up to about 2 hours. While on the subway, ideas often pop into my head: "Would this feature be better if I added XX?" "Was that bug caused by XX?"

Previously, I could only jot these down in a notes app and deal with them when I got home. But often, once home, I'd get busy and forget, or the inspiration wouldn't be as clear anymore.

I wondered: **Can I write code directly on my phone?**

I tried many solutions:

*   GitHub Web Editor: Can edit code, but can't run it.
*   Code-Server: Too heavy, poor experience on a phone screen.
*   Various Cloud IDEs: Either expensive or high latency.

Until Claude Code appeared, and everything changed.

Claude Code is conversational—you don't need to type code line by line. Instead, you describe what you want in natural language, and it writes it for you. This interaction model is naturally suited for mobile: **More reading than writing, and using voice instead of typing.**

> **WORKFLOW**
>
> Cloud Server + Termius + tmux + Claude Code + Voice Input
>
> After a few weeks of tinkering, I figured out this workflow. Let's go through it step by step.

## > Step_02. Preparing the Cloud Server

You need a server that can run Claude Code. It can be:

*   A Cloud Server (AWS, Google Cloud, Azure, etc.)
*   Your own computer + Intranet Penetration (Port Forwarding/Tunneling)
*   A NAS with a Public IP

I repurposed my old university laptop into a Linux server. The specs don't need to be high; 2 Cores and 4GB RAM are sufficient.

**The key is to ensure you can connect via SSH.**

Taking a Linux server as an example, check if the SSH service is running:

```bash
sudo systemctl status sshd
```

If it's not started:

```bash
sudo systemctl start sshd
sudo systemctl enable sshd
```

**Port Forwarding Configuration**

If your server is behind a NAT (e.g., at home), you need to configure port forwarding. Using a common router as an example:

**1.** Log in to the router management page.

**2.** Find "Port Forwarding" or "Virtual Server".

**3.** Map external port 22 (or a custom port) to your server's local IP.

If using a cloud server, remember to allow the SSH port in the security group/firewall settings.

## > Step_03. Termius Download & Configuration

Termius is the best mobile SSH client I've used. It has a modern interface, smooth operation, and most importantly—the free version is sufficient.

**Download:**

*   iOS: Search "Termius" in the App Store.
*   Android: Google Play or official site https://termius.com/

**Configure Connection**

Open Termius, find the ➕ button in the top right to add a new host:

**Address:** Your server IP or domain name.

**Port:** 22 (or your custom port).

**Username:** Your login username.

**Authentication:** Password or SSH Key (Key is recommended for better security).

> **PRO TIP: Important Setting: Keep Connection Alive**
>
> Mobile networks are unstable, and the default configuration drops connections easily. You need to adjust two parameters:
> 1. Go to Host Settings.
> 2. Find **Keepalive interval**, set to **30 seconds**.
> 3. Enable the **Expect answer** option.


![Image 4](https://mmbiz.qpic.cn/mmbiz_jpg/onUpicncef3oPgZfkwFLocdRKChRlic3G391Hak8J3QH6uI9qMh8t7JSVZdzA5YzajJ1wQbicOmOXxTw5bnIHZWXA/640?wx_fmt=jpeg)


## > Step_04. tmux — The Soul of Mobile Coding

Here, I must introduce a powerful tool: tmux.

What is tmux? Simply put, it makes your terminal sessions "immortal".

**Without tmux:**

*   You open Claude Code on your phone.
*   The subway goes into a tunnel, and the network disconnects.
*   You reconnect to the server, only to find Claude Code is gone, and the previous conversation is lost.

**With tmux:**

*   The session runs on the server, independent of your connection.
*   After reconnecting, type `tmux a`, and everything is still there.
*   Claude Code continues running as if nothing happened.

**Install tmux**

Most Linux distributions can install it directly:

```bash
# Ubuntu/Debian
sudo apt install tmux

# CentOS/RHEL
sudo yum install tmux

# macOS
brew install tmux
```

**Basic Usage Workflow**

Once on the subway, two actions are enough:

```bash
# First time starting tmux

# Start Claude Code inside tmux
claude
```

If you disconnect midway:

```bash
# After reconnecting, restore session
tmux a
```

It's that simple.

### Common Issues Using Claude Code in tmux

**Issue 1: I want to scroll up to see Claude's previous answers, but I can't scroll?**

By default, scrolling down on your phone screen in Termius won't scroll the history inside tmux.

**Solution**: Enable mouse mode.

```bash
tmux set -g mouse on
```

Once enabled, you can slide your finger on the Termius screen to view history directly, just like a normal interface.

**Issue 2: How to completely close tmux?**

When you get home and don't want to continue coding, if you just close Termius, tmux will keep running in the background (consuming server memory).

**Solution**: Inside the tmux interface, type:

```bash
exit
```

Or press `Ctrl + d`. You firmly exit only when you see the green bar at the bottom disappear.

**Issue 3: Typing `tmux set -g mouse on` every time is annoying?**

You can configure it permanently:

```bash
# Edit configuration file
nano ~/.tmux.conf

# Add this line inside
set -g mouse on

# Press Ctrl+O then Enter to save, Press Ctrl+X to exit
```

From now on, mouse mode will be automatically enabled every time you start tmux.

### tmux Common Commands Cheat Sheet

**New Session:** `tmux` or `tmux new -s name`

**Restore Session:** `tmux a` or `tmux attach`

**List All Sessions:** `tmux ls`

**Detach Session (Keep in Background):** `Ctrl+b` then press `d`

**Completely Close Session:** `exit` or `Ctrl+d`

> **SUMMARY: Standard Mobile Coding Workflow**
>
> **Enter Station (Start Work):** `tmux` → `claude`
>
> **Disconnect & Reconnect (Continue Work):** `tmux a`
>
> **Home (End Work):** `exit`

## > Step_05. Voice Input — Hands Free

Typing on a phone is indeed inefficient, but I found a game-changer: **Voice Input**.

Claude Code is conversational; you only need to describe what you want, and it writes the code. Voice input is perfectly suited for this interaction.

I use iFlytek Voice Input (讯飞输入法); the recognition accuracy is high, and it gets technical terms right most of the time.

**Download:**

*   iOS: Search "iFlytek Input" (讯飞输入法) in the App Store.
*   Android: Major app stores or official site https://srf.xunfei.cn/

> ⚠️ **WARNING: Note for iPhone Users**
>
> If you use an iPhone, you might encounter an issue: when using voice input, words might get "eaten". For example, you say "Help me write a login feature", and it might only show "Help me write a login", dropping the rest.
>
> **Solution:**
> 1. Open iFlytek Input settings.
> 2. Find "Voice Settings".
> 3. Switch "Voice Result Display Mode" to **"Display after speaking"**.


![Image 5](https://mmbiz.qpic.cn/mmbiz_jpg/onUpicncef3oPgZfkwFLocdRKChRlic3G3icftiaYQibyR9UXfJOUCUwNmQYQhm2asnhnae8MV7NQA9l4vvbWHA92MQ/640?wx_fmt=jpeg)


Doubao Voice Input (豆包语音输入法) is also a good choice; the recognition effect is similar to iFlytek, depending on personal preference.

## > Step_06. Some Little Tips

**1. Use Voice Punctuation:** Say "comma", "period", "new line", and iFlytek will recognize them as corresponding symbols.

**2. Save Common Commands:** Termius supports Snippets; save common commands to execute them with one click.

**3. Communicate in Short Sentences:** When talking to Claude Code, try to use short sentences and state one requirement at a time to reduce errors.

**4. Describe Then Confirm:** After saying "Help me change the login button to blue", wait for Claude to understand and confirm before executing, to avoid wrong changes.

**5. Use Fragmented Time for Small Tasks:** Fixing bugs, writing small features, code reviews—these are suitable for the subway. Leave large refactoring for quiet times.

## > END. Final Thoughts

I've been using this workflow for over a week, and my biggest feeling is: **Fragmented time finally has value.**

Previously, my 2-hour commute was just for browsing news or watching videos. Now, I can complete many "small but important" tasks on the subway.

Of course, the phone screen is small and not suitable for complex development work. But the core of Vibe Coding is "Conversational Programming"; you are communicating with AI more than staring at code and typing. This mode is naturally suitable for mobile.

If you want to try it, the whole process is actually not complicated:

**1.** Prepare a cloud server.

**2.** Download Termius and configure the SSH connection.

**3.** Install tmux and Claude Code on the server.

**4.** Install a good voice input method.

Then, next time you are on the subway, try completing a small task.

> **INSIGHT:**
>
> You will discover that commute time can also be creative time.

🔗 **Related Links:**

If you don't understand Claude Code yet:

[Claude Code Zero-Base Guide: Can't Code? You Can Still Develop! Read This to Double Efficiency!](https://mp.weixin.qq.com/s/Dx-XYcj74c2LdZOWwNS7GQ)

[Claude Skills: Turning AI Assistants into Domain Expert "Skill Pack" Systems](https://mp.weixin.qq.com/s/3hOHFMNZFb5iKgd2koVXOw)

Productivity Tools:

[CC Hooks: No More Staring at Screens Waiting for AI; Get Notified When Done.](https://mp.weixin.qq.com/s/9AfrfjZiY8eeCwfHBpJRhA)

[From 70 Minutes to 9 Minutes: WeChat Official Account Automation Skills! Efficiency Monster!](https://mp.weixin.qq.com/s/iBKgEX_vfYNIe90qPi03Sw)

AI Product Manager Related:

[I Use Claude Code to Write PRDs, Review Pass Rate Increased from 40% to 85%](https://mp.weixin.qq.com/s/YPMRX27NATexsTfWKEjbuw)

[Google Open Source A2UI: A Revolutionary Protocol Letting AI "Speak" UI](https://mp.weixin.qq.com/s/OWv_poBobcvhL9RLNc8Unw)

[Claude Skill: Why Will It Replace Dify, n8n, and Coze?](https://mp.weixin.qq.com/s/rXl4nLI6ouJMIMfvL1iSbQ)
