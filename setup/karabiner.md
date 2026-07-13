# Karabiner-Elements Setup

My [Karabiner-Elements](https://karabiner-elements.pqrs.org/) key remappings on macOS.

## Rules

### Swap Enter & Shift+Enter

In chat apps, swap what **Enter** does so I can write multi-line messages naturally:

| Key           | Sends                       |
| ------------- | --------------------------- |
| `Enter`       | newline (was: send)         |
| `Shift+Enter` | send message (was: newline) |

Only active when one of these apps is frontmost:

- Discord — `com.hnc.Discord`
- ChatGPT — `com.openai.chat`
- Claude — `com.anthropic.claudefordesktop`

## Install

Prerequisite: [Homebrew](https://brew.sh/).

```bash
brew install --cask karabiner-elements
```

Grant the requested Input Monitoring and Accessibility permissions on first launch.

## Configure

Create the rule in-app, then enable it from the UI.

1. Open **Karabiner-Elements → Settings → Complex Modifications → Add your own rule**.
2. Paste the JSON below and save:

```json
{
    "description": "Swap Enter & Shift+Enter",
    "manipulators": [
        {
            "conditions": [
                {
                    "bundle_identifiers": [
                        "com.hnc.Discord",
                        "com.openai.chat",
                        "com.anthropic.claudefordesktop"
                    ],
                    "type": "frontmost_application_if"
                }
            ],
            "from": {
                "key_code": "return_or_enter",
                "modifiers": { "mandatory": ["shift"] }
            },
            "to": [{ "key_code": "return_or_enter" }],
            "type": "basic"
        },
        {
            "conditions": [
                {
                    "bundle_identifiers": [
                        "com.hnc.Discord",
                        "com.openai.chat",
                        "com.anthropic.claudefordesktop"
                    ],
                    "type": "frontmost_application_if"
                }
            ],
            "from": { "key_code": "return_or_enter" },
            "to": [
                {
                    "key_code": "return_or_enter",
                    "modifiers": ["shift"]
                }
            ],
            "type": "basic"
        }
    ]
}
```

3. Find **Swap Enter & Shift+Enter** and click **Enable**.

## Verify

Focus one of the listed apps and type in its message box:

- `Enter` adds a new line without sending.
- `Shift+Enter` sends the message.
- In ChatGPT, `Cmd+Enter` works natively and is no longer remapped here.

To add more apps, append their bundle identifiers to each `bundle_identifiers` list. Find an app's identifier with:

```bash
osascript -e 'id of app "AppName"'
```
