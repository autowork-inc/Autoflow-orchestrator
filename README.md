# Headless Automation Suite

This is a concurrent RPA orchestrator I built with Python and Playwright. I designed it to automate complex, multi-step workflows across legacy web applications that don't offer native APIs. Due to NDA, the source code is private, but here is a quick breakdown of the architecture.

The engine runs on a producer-consumer model. A background dispatcher queues up workloads, like data synchronization tasks, and a pool of worker threads processes them in parallel using isolated Playwright contexts. Instead of running it blindly from the terminal, I built an asynchronous desktop dashboard using Flet to monitor thread health, task progress, and live logs.

Here are the main technical problems I solved:

**Handling Flaky UIs:** The target applications were often slow and unpredictable. Standard automation clicks would randomly fail. I implemented a resilient state-machine with native event emulation. If a page hangs or an element is missing, the worker triggers a fail-fast routine, saves a full-page screenshot and HTML dump for debugging, and gracefully requeues the task.

**Automated MFA Login:** The worker nodes needed to authenticate into portals secured by multi-factor authentication. I integrated pyotp to automatically handle TOTP challenges for our automated service accounts on the fly.

**Thread-Safe Data Aggregation:** Since multiple threads process data at the same time, I wrote a custom, lock-based Excel writer using openpyxl to safely compile the final reports without corrupting the files during parallel execution.

**Tech Stack:** Python 3.10+, Playwright, Flet, PyOTP, openpyxl, concurrent.futures.
