# Linux Performance Optimization Guide (CachyOS)

**Date:** 2026-01-26
**System:** CachyOS (Arch-based)
**Hardware:** Intel Core i7-10750H, NVIDIA GTX 1650

## 1. Current Status Analysis
Your system is already running an optimized kernel (`linux-cachyos`) and has `ananicy-cpp` (auto-nice daemon) active, which is excellent for responsiveness.

## 2. Recommended Actions

### A. Set CPU Governor to Performance
By default, the CPU runs in `powersave` mode. Switching to `performance` ensures the CPU ramps up clock speeds faster, improving system responsiveness and gaming performance.

**Command:**
```bash
sudo cpupower frequency-set -g performance
```

*Note: This setting resets on reboot. To make it persistent, you may need to edit `/etc/default/cpupower` or use a TLP configuration, but running it manually before gaming is safe.*

### B. Enable GameMode
Feral Interactive's GameMode is installed but inactive. Enabling it allows games to request a temporary set of optimizations (like inhibiting the screensaver, changing CPU governor automatically, etc.).

**Command:**
```bash
systemctl --user enable --now gamemoded
```

### C. Verify GPU Drivers
Your NVIDIA drivers are installed (`590.48.01`). Ensure you launch games with the dedicated GPU if you are on a hybrid laptop setup (often handled automatically by CachyOS or via `prime-run`).

## 3. Verification Commands
To confirm your changes have taken effect:

```bash
# Check CPU Governor
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

# Check GameMode Status
systemctl --user status gamemoded
```
