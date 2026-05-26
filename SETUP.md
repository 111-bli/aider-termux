# Aider Setup for Android Termux

This guide is tailored for running **Aider** on **Termux** (Android) using **pipx**, exactly how you like it.

## 1. Install Termux & Update

```bash
pkg update && pkg upgrade -y
pkg install git python openssh -y
```

## 2. Install pipx (your preferred method)

```bash
python -m pip install --user pipx
python -m pipx ensurepath
```

> Restart Termux after this step.

## 3. Install Aider via pipx

```bash
pipx install aider-chat
```

## 4. (Optional but recommended) Install voice dependencies

Termux voice support is limited. For basic TTS:

```bash
pkg install espeak
```

For better experience, many users pair Termux with external keyboard + just type, or use Termux:API + another STT app.

Full continuous hands-free like on desktop is **difficult** on Termux due to mic permissions and background processing limits.

## 5. Set your API key

```bash
export OPENAI_API_KEY=sk-...
```

## 6. Run Aider

```bash
aider --model gpt-4o-mini   # or your preferred model
```

Or with voice flag (basic):
```bash
aider --voice
```

## Notes for your setup
- Works on older Android devices
- Keep it lightweight — avoid heavy local models
- When you upgrade to a new PC, you can switch to the full hands-free repo

## Troubleshooting
- If pipx complains, try `pipx reinstall aider-chat`
- For mic issues in Termux, grant microphone permission in Android settings for Termux

Pull requests and improvements welcome!