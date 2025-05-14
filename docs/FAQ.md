# FAQs

### Why a local sandbox?

A local sandbox is a dedicated environment that is isolated from the rest of the system. As AI agents rapidly evolve towards 70-80% success rates on average tasks, having a controlled and secure environment becomes crucial. Cua's Computer-Use AI agents run in a local sandbox to ensure reliability, safety, and controlled execution.

Benefits of using a local sandbox rather than running the Computer-Use AI agent in the host system:

- **Reliability**: The sandbox provides a reproducible environment - critical for benchmarking and debugging agent behavior. Frameworks like [OSWorld](https://github.com/xlang-ai/OSWorld), [Simular AI](https://github.com/simular-ai/Agent-S), Microsoft's [OmniTool](https://github.com/microsoft/OmniParser/tree/master/omnitool), [WindowsAgentArena](https://github.com/microsoft/WindowsAgentArena) and more are using Computer-Use AI agents running in local sandboxes.
- **Safety & Isolation**: The sandbox is isolated from the rest of the system, protecting sensitive data and system resources. As CUA agent capabilities grow, this isolation becomes increasingly important for preventing potential safety breaches.
- **Control**: The sandbox can be easily monitored and terminated if needed, providing oversight for autonomous agent operation.

### Where are the sandbox images stored?

Sandbox are stored in `~/.lume`, and cached images are stored in `~/.lume/cache`.

### Which image is Computer using?

Computer uses an optimized macOS image for Computer-Use interactions, with pre-installed apps and settings for optimal performance.
The image is available on our [ghcr registry](https://github.com/orgs/trycua/packages/container/package/macos-sequoia-cua).

### Are Sandbox disks taking up all the disk space?

No, macOS uses sparse files, which only allocate space as needed. For example, VM disks totaling 50 GB may only use 20 GB on disk.

### How do I delete a VM?

```bash
lume delete <name>
```

### How do I fix EasyOCR `[SSL: CERTIFICATE_VERIFY_FAILED]` errors?

**Symptom:**
When running an agent that uses OCR (e.g., with `AgentLoop.OMNI`), you might encounter an error during the first run or initialization phase that includes:
```
ssl.SSLCertVerificationError: [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: unable to get local issuer certificate (_ssl.c:1000)
```

**Cause:**
This usually happens when EasyOCR attempts to download its language models over HTTPS for the first time. Python's SSL module cannot verify the server's certificate because it can't locate the necessary root Certificate Authority (CA) certificates in your environment's trust store.

**Solution:**
You need to explicitly tell Python where to find a trusted CA bundle. The `certifi` package provides one. Before running your Python agent script **the first time it needs to download models**, set the following environment variables in the *same terminal session*:
```bash
# Ensure certifi is installed: pip show certifi
export SSL_CERT_FILE=$(python -m certifi)
export REQUESTS_CA_BUNDLE=$(python -m certifi)

# Now run your Python script that uses the agent...
# python your_agent_script.py
```
This directs Python to use the CA bundle provided by `certifi` for SSL verification. **Note:** Once EasyOCR has successfully downloaded its models, you typically do not need to set these environment variables before every subsequent run.

### How do I troubleshoot the agent failing to get the VM IP address or getting stuck on "VM status changed to: stopped"?

**Symptom:**
When running your agent script (e.g., using `Computer().run(...)`), the script might hang during the VM startup phase, logging messages like:
*   `Waiting for VM to be ready...`
*   `VM status changed to: stopped (after 0.0s)`
*   `Still waiting for VM IP address... (elapsed: XX.Xs)`
*   Eventually, it might time out, or you might notice the VM window never appears or closes quickly.

**Cause:**
This is typically due to known instability issues with the `lume serve` background daemon process, as documented in the main `README.md`:
1.  **`lume serve` Crash:** The `lume serve` process might terminate unexpectedly shortly after launch or when the script tries to interact with it. If it's not running, the script cannot get VM status updates or the IP address.
2.  **Incorrect Status Reporting:** Even if `lume serve` is running, its API sometimes incorrectly reports the VM status as `stopped` immediately after startup is initiated. While the underlying `Computer` library tries to poll and wait for the correct `running` status, this initial incorrect report can cause delays or failures if the status doesn't update correctly within the timeout or if `lume serve` crashes during the polling.

**Troubleshooting Steps:**
1.  **Check `lume serve`:** Is the `lume serve` process still running in its terminal? Did it print any errors or exit? If it's not running, stop your agent script (`Ctrl+C`) and proceed to step 2.
2.  **Force Cleanup:** Before *every* run, perform a rigorous cleanup to ensure no old `lume` processes or VM states interfere. Open a **new terminal** and run:
    ```bash
    # Stop any running Lume VM gracefully first (replace <vm_name> if needed)
    lume stop macos-sequoia-cua_latest

    # Force kill lume serve and related processes
    pkill -f "lume serve"
    pkill -9 -f "lume"
    pkill -9 -f "VzVirtualMachine" # Kills underlying VM process

    # Optional: Verify they are gone
    # ps aux | grep -E 'lume|VzVirtualMachine' | grep -v grep
    ```
3.  **Restart Sequence:**
    *   **Terminal 1:** Start `lume serve` cleanly:
        ```bash
        lume serve
        ```
        *(Watch this terminal to ensure it stays running).*
    *   **Terminal 2:** Run your agent script (including the `export SSL_CERT_FILE...` commands if *first time* using OCR):
        ```bash
        # export SSL_CERT_FILE=$(python -m certifi) # Only if first run with OCR
        # export REQUESTS_CA_BUNDLE=$(python -m certifi) # Only if first run with OCR
        python your_agent_script.py
        ```
4.  **Retry:** Due to the intermittent nature of the Lume issues, sometimes simply repeating steps 2 and 3 allows the run to succeed if the timing avoids the status reporting bug or the `lume serve` crash.

**Related Issue: "No route to host" Error (macOS Sequoia+)**

*   **Symptom:** Even if the `Computer` library logs show the VM has obtained an IP address, you might encounter connection errors like `No route to host` when the agent tries to connect to the internal server, especially when running the agent script from within an IDE (like VS Code or Cursor).
*   **Cause:** This is often due to macOS Sequoia's enhanced local network privacy controls. Applications need explicit permission to access the local network, which includes communicating with the VM.
*   **Solution:** Grant "Local Network" access to the application you are running the script from (e.g., your IDE or terminal application). Go to **System Settings > Privacy & Security > Local Network**, find your application in the list, and toggle the switch ON. You might need to trigger a connection attempt from the application first for it to appear in the list. See [GitHub Issue #61](https://github.com/trycua/cua/issues/61) for more details and discussion.

**Note:** Improving the stability of `lume serve` is an ongoing development area.

### How do I troubleshoot Computer not connecting to lume daemon?

If you're experiencing connection issues between Computer and the lume daemon, it could be because the port 7777 (used by lume) is already in use by an orphaned process. You can diagnose this issue with:

```bash
sudo lsof -i :7777
```

This command will show all processes using port 7777. If you see a lume process already running, you can terminate it with:

```bash
kill <PID>
```

Where `<PID>` is the process ID shown in the output of the `lsof` command. After terminating the process, run `lume serve` again to start the lume daemon.

### What information does Cua track?

Cua tracks anonymized usage and error report statistics; we ascribe to Posthog's approach as detailed [here](https://posthog.com/blog/open-source-telemetry-ethical). If you would like to opt out of sending anonymized info, you can set `telemetry_enabled` to false in the Computer or Agent constructor. Check out our [Telemetry](Telemetry.md) documentation for more details.
