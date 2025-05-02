+++
title = 'How to Set Environment Variables in macOS to Securely Store API Keys'
date  = 2024-11-06T11:55:00+03:00
tags  = ["macOS", "environment variables", "security"]
draft = false
+++

When building apps that connect to third-party services, you often need API keys for authentication. Hardcoding these keys directly in your code isn’t safe, as it can lead to accidental exposure. A more secure approach is to store them as environment variables, keeping sensitive information out of your codebase.

In this post, I’ll guide you through setting environment variables on macOS for secure storage. We’ll cover `zsh` (the default shell for macOS Catalina and later) and `bash` (used in older macOS versions), so you can store API keys and other private data securely.

### Setting Environment Variables in `zsh` (macOS Catalina and Later)

Starting with macOS Catalina, `zsh` is the default shell. Here’s how to set an environment variable in `zsh`:

### 1. Add the Environment Variable to `.zshrc`

Open your `.zshrc` file in a text editor:

```bash
nano ~/.zshrc
```

Add the following line at the end, replacing `<your-api-key>` with your actual API key:

```bash
export BINGX_API_KEY="<your-api-key>"
```

### 2. Save and Close the File

Save your changes and exit (`Ctrl + X` in nano, then press `Y` to confirm).

### 3. Apply the Changes

To apply the changes without restarting your terminal, run:

```bash
source ~/.zshrc
```

### 4. Verify the Variable

Check that the variable is set by running:

```bash
printenv BINGX_API_KEY
```

If everything is set correctly, your API key should display.

### Setting Environment Variables in `bash` (Older macOS Versions)

For older macOS versions that use `bash` as the default shell, the process is nearly the same, but you’ll edit `.bash_profile`instead.

### 1. Add the Environment Variable to `.bash_profile`

Open `.bash_profile` in a text editor:

```bash
nano ~/.bash_profile
```

Add the following line at the end:

```bash
export BINGX_API_KEY="<your-api-key>"
```

### 2. Save and Close the File

Save your changes and exit.

### 3. Apply the Changes

Run this command to apply your changes:

```bash
source ~/.bash_profile
```

### 4. Verify the Variable

Check if the variable is set by running:

```bash
printenv BINGX_API_KEY
```

### Setting Environment Variables on Third-Party Services

If you’re deploying your app with a backend service, you’ll need to set environment variables there as well. For example, I use `vercel` for my Flask app. You can find detailed instructions on setting environment variables for Vercel in their [documentation](https://vercel.com/docs/projects/environment-variables).

### Conclusion

Using environment variables to store sensitive information, like API keys, is a simple and effective way to boost security. Whether you’re using `zsh` or `bash`, following these steps will help you keep credentials safe and your code clean. Remember, always avoid hardcoding sensitive data—use environment variables instead.

{{< buymeacoffee-widget >}}