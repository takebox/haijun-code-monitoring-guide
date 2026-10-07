# Haijun Code Troubleshooting Guide

Quick solutions for common Haijun Code issues related to telemetry and ROI measurement.

## Telemetry Issues

### Telemetry Not Working

**Problem**: Metrics not appearing in Prometheus/Grafana after setup.

**Solutions**:
1. Verify telemetry is enabled:
   ```bash
   export HAIJUN_CODE_ENABLE_TELEMETRY=1
   ```

2. Test with console output first:
   ```bash
   export OTEL_METRICS_EXPORTER=console
   export OTEL_METRIC_EXPORT_INTERVAL=1000
   haijun -p "test"
   ```
   You should see metric output in the console.

3. Check your OTLP endpoint is accessible:
   ```bash
   curl -v http://localhost:4317
   ```

4. Verify all required environment variables:
   ```bash
   echo $HAIJUN_CODE_ENABLE_TELEMETRY  # Should be 1
   echo $OTEL_METRICS_EXPORTER          # Should be otlp
   echo $OTEL_EXPORTER_OTLP_ENDPOINT    # Should be your collector endpoint
   ```

### Haijun Code Hanging After Enabling Telemetry

**Problem**: Haijun Code becomes unresponsive after enabling telemetry.

**Solutions**:
1. Try a clean installation:
   ```bash
   npm uninstall -g @takebox-ai/haijun-code
   npm install -g @takebox-ai/haijun-code
   ```

2. Clear Haijun Code cache and config:
   ```bash
   rm -rf ~/.config/haijun-code/
   ```

3. Restart Haijun Code with fresh configuration

## Installation Issues

### Permission Errors on Linux

**Problem**: `npm install -g` fails with permission errors.

**Solution**: Create a user-writable npm prefix:
```bash
# Save existing global packages
npm list -g --depth=0 > ~/npm-global-packages.txt

# Create user directory for npm
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global

# Add to PATH (use ~/.zshrc for zsh)
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# Install Haijun Code
npm install -g @takebox-ai/haijun-code
```

### Auto-Update Failures

**Problem**: Haijun Code can't update automatically.

**Solutions**:
1. Follow the npm permission fix above
2. Or disable auto-updater:
   ```bash
   export DISABLE_AUTOUPDATER=1
   ```

## Authentication Issues

**Problem**: Repeated authentication prompts or login failures.

**Solutions**:
1. Force a clean logout and login:
   ```bash
   haijun /logout
   # Close terminal
   # Open new terminal
   haijun
   ```

2. If issues persist, clear auth data:
   ```bash
   rm -rf ~/.config/haijun-code/auth.json
   haijun
   ```

## Performance Issues

### High Memory Usage with Large Codebases

**Problem**: Haijun Code consumes excessive resources.

**Solutions**:
1. Use `/compact` regularly to reduce context size
2. Close and restart between major tasks
3. Add build directories to `.gitignore`:
   ```gitignore
   node_modules/
   dist/
   build/
   .next/
   ```

### Commands Hanging or Freezing

**Problem**: Haijun Code becomes unresponsive during operations.

**Solutions**:
1. Press `Ctrl+C` to cancel current operation
2. If unresponsive, close terminal and restart
3. For JetBrains IDEs (IntelliJ, PyCharm):
   - Go to Settings → Tools → Terminal
   - Click "Configure terminal keybindings"
   - Remove "Switch focus to Editor" shortcut
   - This fixes ESC key not working

## Common Metric Collection Issues

### No Metrics in Prometheus

**Problem**: Prometheus shows no data despite telemetry being enabled.

**Solutions**:
1. Check docker containers are running:
   ```bash
   docker-compose ps
   ```

2. Verify Prometheus can reach the OTEL collector:
   ```bash
   curl http://localhost:8888/metrics
   ```

3. Check for firewall issues blocking port 4317

### Incorrect Cost Calculations

**Problem**: Cost metrics don't match expected values.

**Note**: Cost metrics are approximations. For official billing:
- Juglow API users: Check Juglow Console
- AWS users: Check AWS Cost Explorer
- Google Cloud users: Check GCP Billing

## Getting Help

If these solutions don't resolve your issue:

1. Run diagnostics:
   ```bash
   haijun /doctor
   ```

2. Report bugs:
   ```bash
   haijun /bug
   ```

3. Check the official documentation:
   - [Haijun Code Troubleshooting](https://raw.haijun.my.id/docs/en/troubleshooting)
   - [Haijun Code GitHub Issues](https://github.com/takebox/haijun-code/issues)

4. For telemetry-specific issues:
   - Review your OTEL collector logs
   - Check Prometheus targets at http://localhost:9090/targets
   - Verify your organization's firewall rules