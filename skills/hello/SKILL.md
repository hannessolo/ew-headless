---
name: hello
description: "Hello World skill for the experience-workspace plugin. Use when the user types `/experience-workspace:hello` or wants to verify the plugin is installed and working."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Hello, Experience Workspace

Greet the user and confirm the experience-workspace plugin is active.

Respond with:

```
Hello from experience-workspace! The plugin is installed and ready.
```

If the user passed arguments (available in `$ARGUMENTS`), acknowledge them in the greeting.
