# Tasker Profile Configuration for Hands-Free Aider on Termux

This guide shows how to set up **Tasker** + **Termux:Tasker** + **AutoVoice** so you can trigger Aider completely hands-free on Android (e.g. say "Hey Aider, fix the bug in main.py" and have it run).

It builds on the main `SETUP.md` and the voice script in this repo.

## Why This Combo is Powerful

- Voice wake-word style triggering ("Hey Aider ...")
- Run Python/bash scripts in background
- React to Android events (WiFi, shake, time, notifications)
- Use native Android TTS (`termux-tts-speak`) for spoken replies
- Capture Aider output and show it as notification or speak it back
- Much closer to the continuous voice experience you want

## Prerequisites

1. **From F-Droid** (recommended for stability):
   - Termux
   - Termux:API
   - Termux:Tasker
2. **Tasker** (paid app)
3. **AutoVoice** plugin (for easy voice commands)
4. **Permissions & Battery**:
   - In Tasker → ⋮ → More → Android Settings → Additional permissions → enable `com.termux.permission.RUN_COMMAND`
   - Disable battery optimization for: Termux, Termux:API, Termux:Tasker, Tasker
   - Android 10+: Allow Termux "Draw over other apps"
   - Grant microphone permission to Termux:API app in Android Settings

## 1. Prepare Scripts Folder

```bash
mkdir -p ~/.termux/tasker
cd ~/.termux/tasker
```

### Recommended Launcher Script: `aider_voice_launcher.sh`

Create this file:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# This launcher can be called from Tasker
# It supports both voice commands and direct Aider launch

COMMAND="$1"

if [ -n "$COMMAND" ]; then
    echo "[Aider] Voice command received: $COMMAND"
    # Option A: Call your Python voice wrapper (recommended)
    python3 ~/aider-termux/voice_aider.py --command "$COMMAND"
    
    # Option B: Direct Aider with the command (if you have a project open)
    # aider --message "$COMMAND" --yes
else
    echo "[Aider] Starting voice mode..."
    python3 ~/aider-termux/voice_aider.py
fi
```

Make it executable:
```bash
chmod +x ~/.termux/tasker/aider_voice_launcher.sh
```

> **Tip**: Adjust the path `~/aider-termux/voice_aider.py` to wherever you cloned the repo.

## 2. Create a Task in Tasker

1. Open Tasker → **Tasks** tab → **+** → name it **"Aider Voice Launcher"**
2. Add Action:
   - **Plugin** → **Termux:Tasker** → **Run** (or the main action)
3. Configure:
   - **Executable**: `~/.termux/tasker/aider_voice_launcher.sh`
   - **Arguments**: `%avcommand`   (this variable comes from AutoVoice)
   - **Run in background**: **Yes** (important for hands-free)
   - Timeout: 120 seconds or more
4. (Optional but recommended) Add a second action:
   - **Plugin** → **Termux:API** → **TTS Speak**
   - Text: `Aider task started`

Save the Task.

## 3. Create Voice-Triggered Profile (Best for Hands-Free)

This is the closest to saying "Hey Aider" like in this chat.

1. Go to **Profiles** tab → **+** → **Event**
2. Choose **Plugin** → **AutoVoice** → **Recognized**
3. Configure:
   - **Command Filter**: `hey aider (.*)`
   - Check **Regex**
   - **Continuous** mode: Enable if available in AutoVoice settings
4. Link this Profile to the Task **"Aider Voice Launcher"** you created above.
5. (Optional) Add **Event Behaviour** → check "Stay active after task"

**Test it**: Say **"Hey Aider, list the files in this folder"** or **"Hey Aider, create a README for my project"**

Tasker will capture everything after "hey aider" and pass it to your script.

## 4. Returning Output & Speaking Results

Termux:Tasker can return stdout/stderr back to Tasker.

In your Task:
- After the Termux:Tasker action, add:
  - **Variable Set** → name `%aider_output` → value `%stdout` (or the variable Termux:Tasker returns)

Then you can:
- Use **Termux:API → TTS Speak** with `%aider_output`
- Or show a **Notification** with the result

This way Aider can "talk back" to you through Android's native voice.

## 5. Other Useful Profile Examples

- **Shake Phone to Start Aider**: Event → Sensor → Shake
- **When connected to home WiFi**: State → Net → WiFi Connected (SSID = YourHomeWiFi)
- **Time-based** (e.g. every morning at 9am)
- **On specific notification**: Event → UI → Notification
- **Widget / Quick Settings tile**: Add a 1-tap launcher

## 6. Battery Optimization & Reliability Tips

- Whitelist Termux + Tasker from battery optimization (very important on Android 12+)
- In Termux settings → Battery optimization → Unrestricted
- Test in background vs foreground
- For long-running voice listening, you may need a foreground service or Tasker "Stay awake" action
- AutoVoice has settings for continuous listening — experiment with them

## 7. Advanced: Full pexpect Automation

You can extend `voice_aider.py` (or the launcher) to use `pexpect` to automatically control an Aider session in the background when a voice command arrives.

This is the next level for true hands-free coding on your phone.

If you want me to add an example `pexpect` version of the launcher, just say so.

## Troubleshooting

- **Script not running**: Check path in the launcher and that it's executable.
- **No voice trigger**: Make sure AutoVoice has microphone permission and Regex is enabled.
- **Output not returning**: Check the variable name Termux:Tasker uses for stdout (usually `%stdout` or check its docs).
- **Termux killed in background**: Battery optimization is the #1 cause.

## Next Steps

1. Pull the latest from this repo.
2. Set up the basic voice script first (see SETUP.md).
3. Add the Tasker layer on top for the best hands-free experience.

This combination (Termux:API + Termux:Tasker + AutoVoice) currently gives the smoothest voice automation possible on Android/Termux without rooting.

---

*Updated for your Kali NetHunter + Termux workflow and pipx preference.*