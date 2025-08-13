# Development Plan: Environment Variable Configuration (Simple Implementation)

**Created:** 2025-08-12  
**Status:** Ready for Implementation  
**Complexity:** Low  
**Estimated Time:** 15 minutes  

## Overview

Implement environment variable configuration for ActivityWatch server host and port using the simplest possible approach that maintains full backward compatibility and requires zero test changes.

## Requirements Met

- ✅ Allow server host/IP to be set via `ACTIVITYWATCH_HOST` environment variable
- ✅ Allow server port to be set via `ACTIVITYWATCH_PORT` environment variable  
- ✅ Basic validation to ensure values are valid
- ✅ Use reasonable defaults (127.0.0.1:5600) when env vars not set
- ✅ Ensure existing unit tests continue to pass unchanged

## Implementation Strategy

### Single Pattern Applied to 4 Files

Replace the hardcoded constant in each file with a validation function that reads environment variables.

**Files to Modify:**
1. `src/bucketList.ts`
2. `src/query.ts`
3. `src/rawEvents.ts`
4. `src/getSettings.ts`

### Code Pattern

**Replace this:**
```typescript
const AW_API_BASE = "http://127.0.0.1:5600/api/0";
```

**With this:**
```typescript
function getValidatedApiBase(): string {
  const host = process.env.ACTIVITYWATCH_HOST || '127.0.0.1';
  const port = process.env.ACTIVITYWATCH_PORT || '5600';
  
  // Basic validation
  const portNum = parseInt(port);
  if (isNaN(portNum) || portNum < 1 || portNum > 65535) {
    console.error(`Invalid ACTIVITYWATCH_PORT: ${port}, using default 5600`);
    return 'http://127.0.0.1:5600/api/0';
  }
  
  if (!host.trim()) {
    console.error(`Invalid ACTIVITYWATCH_HOST: empty, using default 127.0.0.1`);
    return 'http://127.0.0.1:5600/api/0';
  }
  
  return `http://${host}:${port}/api/0`;
}

const AW_API_BASE = getValidatedApiBase();
```

## Validation Logic

### Host Validation
- Check not empty/whitespace only
- Let axios handle invalid hostnames naturally (keep it simple)

### Port Validation  
- Must be valid integer
- Must be in range 1-65535
- Invalid values fall back to default with error message

### Error Handling
- Invalid values → Use defaults + log warning to stderr
- Missing values → Use defaults silently
- Continue execution with safe defaults

## Test Compatibility

**Why existing tests work unchanged:**
- Tests already mock `axios.get()` and `axios.post()`
- Tests don't care about actual URL being called
- Validation function runs once at module load
- Mock responses returned regardless of URL
- Tests run in clean environment with no env vars
- Defaults kick in, everything works as before

**Zero test modifications required.**

## Implementation Steps

1. **Update `src/bucketList.ts`**
   - Replace hardcoded constant with validation function

2. **Update `src/query.ts`**
   - Replace hardcoded constant with validation function

3. **Update `src/rawEvents.ts`**
   - Replace hardcoded constant with validation function

4. **Update `src/getSettings.ts`**
   - Replace hardcoded constant with validation function

5. **Run tests to verify**
   - `npm test` should pass all existing tests

6. **Manual testing**
   - Test with no env vars (should use defaults)
   - Test with valid env vars
   - Test with invalid env vars (should fall back to defaults)

## Documentation Update

Add configuration section to `README.md`:

```markdown
## Configuration

Set custom ActivityWatch server connection:

```bash
# Custom host
ACTIVITYWATCH_HOST=192.168.1.100 activitywatch-mcp-server

# Custom port
ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server

# Both
ACTIVITYWATCH_HOST=activitywatch.local ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server
```

Defaults: `127.0.0.1:5600`
```

## Benefits of This Approach

- **Minimal code change** - Same pattern applied 4 times
- **No new dependencies** - Uses built-in Node.js features
- **No new files** - No additional maintenance burden
- **No architectural changes** - Preserves existing structure
- **Zero breaking changes** - Identical behavior when no env vars set
- **Test-friendly** - No test modifications needed
- **Easy to revert** - Minimal surface area if issues arise

## Environment Variable Examples

```bash
# Use defaults (127.0.0.1:5600)
activitywatch-mcp-server

# Connect to remote server
ACTIVITYWATCH_HOST=192.168.1.100 activitywatch-mcp-server

# Use custom port
ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server

# Both custom
ACTIVITYWATCH_HOST=activitywatch.local ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server

# Docker environment
ACTIVITYWATCH_HOST=activitywatch-container ACTIVITYWATCH_PORT=5600 activitywatch-mcp-server
```

## Validation Examples

**Valid configurations:**
- `ACTIVITYWATCH_HOST=localhost`
- `ACTIVITYWATCH_HOST=192.168.1.100`
- `ACTIVITYWATCH_HOST=activitywatch.local`
- `ACTIVITYWATCH_PORT=5600`
- `ACTIVITYWATCH_PORT=8080`

**Invalid configurations (will use defaults):**
- `ACTIVITYWATCH_HOST=""` (empty string)
- `ACTIVITYWATCH_HOST="   "` (whitespace only)
- `ACTIVITYWATCH_PORT=0` (invalid port)
- `ACTIVITYWATCH_PORT=99999` (port out of range)
- `ACTIVITYWATCH_PORT=abc` (non-numeric)

## Success Criteria

- ✅ All existing tests pass without modification
- ✅ Environment variables work when set
- ✅ Defaults work when env vars not set
- ✅ Invalid values fall back to defaults with warnings
- ✅ No breaking changes to existing functionality
- ✅ Documentation updated with examples

## Risk Assessment

**Risk Level:** Extremely Low

- Same behavior when no environment variables set
- Minimal code surface area changed
- No architectural modifications
- Easy rollback if issues discovered
- Comprehensive fallback to known-good defaults
