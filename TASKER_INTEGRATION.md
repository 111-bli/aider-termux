# Tasker Profile Configuration for Aider on Termux

This document explains how to set up **Tasker profiles** to automate and trigger your Aider voice setup on Android using Termux.

## Why Use Tasker + Termux:Tasker?

- Trigger Aider hands-free via voice (with AutoVoice)
- Run scripts in background or foreground
- React to Android events (WiFi, time, shake, notifications, etc.)
- Get output back into Tasker for notifications or TTS

## Prerequisites

1. Install from **F-Droid** (recommended):
   - Termux
   - Termux:API
   - Termux:Tasker
2. Install **Tasker** (paid, from Play Store or official site)
3. (Strongly recommended for voice) Install **AutoVoice** plugin
4. Grant permissions:
   - Tasker → Additional permissions → `com.termux.permission.RUN_COMMAND`
   - Disable battery optimization for Termux, Tasker, and Termux:Tasker
   - For Android 10+: Termux → "Draw over other apps"

## Step 1: Prepare Scripts in Termux

```bash
mkdir -p ~/.termux/tasker
cd ~/.termux/tasker

# Create a launcher script (example)
nano aider_voice_launcher.sh
```

Example `aider_voice_launcher.sh`:

```bash
#!/data/data/com.termux/files/usr/bin/bash
# This can call your Python voice script or start Aider with arguments

COMMAND="$1"

if [ -n "$COMMAND" ]; then
    echo "Received command: $COMMAND"
    # Example: pass to your Python script or Aider
    python ~/aider-voice/voice_aider.py --command "$COMMAND"
else
    python ~/aider-voice/voice_aider.py
fi
```

Make it executable:
```bash
chmod +x ~/.termux/tasker/aider_voice_launcher.sh
```

## Step 2: Create a Task in Tasker

1. Open Tasker → Tasks tab → + → New Task (name it "Run Aider Voice")
2. Add Action:
   - **Plugin** → **Termux:Tasker**
3. Configure:
   - **Executable**: `~/.termux/tasker/aider_voice_launcher.sh`
   - **Arguments**: `%avcommand`   (this comes from AutoVoice)
   - **Run in background**: Yes (for most cases)
4. (Optional) Add more actions like `termux-tts-speak` via Termux:API or notifications.

## Step 3: Create a Voice-Triggered Profile (Recommended for Hands-Free)

This gives you the closest experience to saying "Hey Aider" like in this chat.

1. Go to **Profiles** tab → + → Event
2. Choose **Plugin** → **AutoVoice** → **Recognized**
3. In configuration:
   - **Command Filter**: `hey aider (.*)`
   - Enable **Regex**
4. Link it to the Task you created above ("Run Aider Voice")
5. (Optional) Add **Event Behaviour** if you want it to stay active.

Now say: **"Hey Aider, create a new Python script for..."**

Tasker will capture it and run your Termux script.

## Other Useful Profile Examples

- **Shake to Launch Aider**: Event → Sensor → Shake
- **Specific Time**: Time context
- **When connecting to home WiFi**: State → Net → WiFi Connected
- **On notification from certain app**: Event → UI → Notification

## Returning Output to Tasker

You can have your script output variables that Tasker can read, or use Logcat Entry profiles to capture stdout.

## Limitations on Android

- True continuous always-listening is restricted by Android battery rules.
- AutoVoice helps a lot but may need "continuous" mode enabled in its settings.
- Test thoroughly and whitelist apps from battery optimization.

## Next Steps

Combine this with the `voice_aider.py` from the main setup. You can extend the Python script to accept commands via arguments or stdin.

For full automation, we can add pexpect logic inside the launched script to control Aider sessions.

Pull the latest from the repo to see updates.
