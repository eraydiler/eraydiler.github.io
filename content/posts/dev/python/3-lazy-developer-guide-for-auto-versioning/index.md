+++
title = "The Lazy Developer's Guide to Automated Versioning"
date = 2026-03-07T10:00:00+03:00
draft = false
+++

If you have a workflow like mine—constantly context-switching between iOS, macOS, Flask/Python, and more—you know the chaos. You're making countless changes, pushing a bombardment of commits, especially in early-stage, pre-release projects. Amidst all this, is there something you constantly forget or overlook?

**Versioning!**

Recently, I realized that no matter how hard I tried to pay attention, I wasn't giving versioning the care it deserved. I decided to put an end to this once and for all.

**Automated Versioning!**

You know the drill. You’re in the zone. You’re fixing bugs, refactoring spaghetti code, and pushing commits like there’s no tomorrow.

- `git commit -m "fix typo"`
- `git commit -m "refactor logic"`
- `git commit -m "oops, broke it, fixed again"`

And then, three weeks later, you look at your `setup.py` or `package.json` and it still stares back at you: **`version: 0.0.1`**.

Imagine this scenario: unless you specify otherwise, the project's single, main version automatically updates with every commit. At the same time, if you wish, you can trigger specific version bumps (major, minor) just by tagging the importance of your change in the commit message, without ever touching a version number manually.

**Sounds logical, doesn't it?**

I implemented this for my own workflow, and now versioning requires zero effort. I can bump the version just by indicating the change's importance in the commit message. And if I don't say anything? The patch version increments automatically.

If you're suffering from the same headache, here is how you can add this logic to your projects.

## **The Problem: "Where did I put that version number again?"**

1. **The "I'll do it later" Syndrome:** When you’re **rapid prototyping**, stopping to manually increment a number feels like a speed bump on a highway. So you likely keep going without incrementing the version for each **change**.
2. **The Scavenger Hunt:** In some projects, version numbers are scattered across `package.json`, `setup.py`, and more. Even worse, they often show different numbers.

I’m lazy enough to want to get rid of these headaches. As they say, **Laziness is the mother of invention.**

## **The Solution**

Let Git do the heavy lifting. **My goal?** Whenever I type `git commit`, I want the version to bump itself.

### **Step 1: The Single Source of Truth**

First, I stopped the madness of having multiple version sources by creating a single, dedicated file: `src/version.py`.

```python
__version__ = "1.0.0"
```

### **Step 2: The Worker Bee (Script + Hook)**

We need two things:

1. A Python script to do the math (increment numbers).
2. A Git Hook to trigger it automatically when you commit.

### **2.1 The Python Script**

Create `scripts/bump_version.py`. This script reads the `BUMP` environment variable and updates `src/version.py`.

```python
#!/usr/bin/env python3
import re
import sys
import os
from pathlib import Path

# Define version file path
PROJECT_ROOT = Path(__file__).parent.parent
VERSION_FILE = PROJECT_ROOT / "src" / "version.py"

def bump_version():
    if not VERSION_FILE.exists():
        print(f"Error: Version file not found at {VERSION_FILE}")
        sys.exit(1)

    # Get bump type from environment variable (default: patch)
    bump_type = os.environ.get('BUMP', 'patch').lower()
    valid_types = ['major', 'minor', 'patch']
    
    if bump_type not in valid_types:
        print(f"Warning: Invalid bump type '{bump_type}'. Defaulting to 'patch'.")
        bump_type = 'patch'

    content = VERSION_FILE.read_text()
    
    # Regex to find __version__ = "X.Y.Z"
    pattern = r'__version__\s*=\s*"(\d+)\.(\d+)\.(\d+)"'
    match = re.search(pattern, content)
    
    if not match:
        print(f"Error: Could not find valid version string in {VERSION_FILE}")
        sys.exit(1)

    major, minor, patch = map(int, match.groups())
    
    # Calculate new version
    if bump_type == 'major':
        major += 1
        minor = 0
        patch = 0
    elif bump_type == 'minor':
        minor += 1
        patch = 0
    else:  # patch
        patch += 1
        
    new_version = f"{major}.{minor}.{patch}"
    
    # Replace content
    new_content = re.sub(
        pattern,
        f'__version__ = "{new_version}"',
        content
    )
    
    VERSION_FILE.write_text(new_content)
    print(f"🚀 Bumped version ({bump_type}): {match.group(1)}.{match.group(2)}.{match.group(3)} -> {new_version}")

if __name__ == "__main__":
    try:
        bump_version()
    except Exception as e:
        print(f"Error bumping version: {e}")
        sys.exit(1)
```

### **2.2 The Basic Git Hook**

Now, let's make it run automatically. Create a file at `.git/hooks/post-commit` and make it executable (`chmod +x .git/hooks/post-commit`):

```bash
#!/bin/bash
# Simple hook to bump version using BUMP env variable

# 1. Prevent infinite loops
if git show --name-only --format="" HEAD | grep -q "src/version.py"; then
    exit 0
fi

echo "🚀 Auto-bumping version..."

# 2. Run python script & Amend commit
# Note: Git doesn't pass env vars automatically, but if you run BUMP=minor git commit,
# it stays in the shell environment, so python sees it!
python3 scripts/bump_version.py

# 3. If python script succeeded (exit code 0), amend the commit
if [ $? -eq 0 ]; then
    git add src/version.py
    git commit --amend --no-verify --no-edit > /dev/null
    echo "✅ Commit amended with new version."
fi
exit 0
```

### **Step 3: Test It Out (Terminal)**

Now try this in your terminal:

```bash
# Bump minor version (1.0.0 -> 1.1.0)
BUMP=minor git commit -m "feat: adding new login flow"

# Default bump (patch: 1.1.0 -> 1.1.1)
git commit -m "fix: typo in header"
```

It works! But wait, what if you use an IDE (VS Code, Cursor, Trae)? You can't easily pass `BUMP=minor` there.

## **Bonus: The Magic Trick (Part 2: IDE Users)**

For IDE users, we want to control the version bump directly from the **commit message**.

Let's upgrade our hook to support tags like `[minor]` or `[major]` in the message.

Update your `.git/hooks/post-commit` file:

```bash
#!/bin/bash
# Advanced hook: Supports both BUMP env var AND commit message tags

# 1. Prevent infinite loops
if git show --name-only --format="" HEAD | grep -q "src/version.py"; then
    exit 0
fi

# 2. Get commit message
MSG=$(git log -1 --format=%B)

# 3. Determine bump type
# Check environment variable first (Terminal: BUMP=minor git commit ...)
if [[ -n "$BUMP" ]]; then
    BUMP_TYPE=$BUMP
# Check commit message tags (IDE: "fix bug [minor]")
elif [[ "$MSG" == *"[major]"* ]]; then
    BUMP_TYPE="major"
elif [[ "$MSG" == *"[minor]"* ]]; then
    BUMP_TYPE="minor"
elif [[ "$MSG" == *"[skip bump]"* ]]; then
    echo "⏩ Skipping version bump."
    exit 0
else
    BUMP_TYPE="patch"
fi

echo "🚀 Auto-bumping version ($BUMP_TYPE)..."

# 4. Run python script & Amend commit
BUMP=$BUMP_TYPE python3 scripts/bump_version.py

# 5. If python script succeeded, amend the commit
if [ $? -eq 0 ]; then
    git add src/version.py
    git commit --amend --no-verify --no-edit > /dev/null
    echo "✅ Commit amended with new version."
fi
exit 0
```

### **The Result? Pure Bliss.**

Now, whether I'm in the terminal or using my IDE's Source Control panel, my workflow is seamless:

- **Terminal Power User:** I want to bump minor version directly.
`BUMP=minor git commit -m "Refactored login flow"` -> **minor** bump (1.0.0 -> 1.1.0)
- **IDE User (or simple tag):** I just want to tag it in the message.
`git commit -m "Added dark mode support [minor]"` -> **minor** bump (1.0.0 -> 1.1.0)
- **Standard update (Lazy mode):** I commit normally without any flags.
`git commit -m "Fixing header alignment"` -> **patch** bump (1.0.0 -> 1.0.1)

I literally do **zero extra work**. My version numbers are always moving forward, tracking every single change I make.

## **Conclusion**

Now there’s no way to forget versioning. You don’t need to keep thinking about version numbers either. You have better things to do.

Be lazy. Automate it. Your future self (and your users) will thank you.