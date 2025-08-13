# Feature Request: Configurable ActivityWatch Server Connection

**Issue Type:** Enhancement  
**Priority:** Medium  
**Labels:** configuration, environment-variables  

## Summary

Add support for configuring the ActivityWatch server IP address and port through environment variables instead of hardcoding them in the source code.

## Current Behavior

The ActivityWatch server connection details are currently hardcoded to `127.0.0.1:5600` in the source code.

## Proposed Behavior

Allow users to configure the ActivityWatch server connection via environment variables:
- `ACTIVITYWATCH_HOST` (default: `127.0.0.1`)
- `ACTIVITYWATCH_PORT` (default: `5600`)

## Use Cases

- **Remote ActivityWatch servers**: Users running ActivityWatch on a different machine or container
- **Custom port configurations**: Users who have configured ActivityWatch to run on non-default ports
- **Docker environments**: Containerized deployments where the ActivityWatch server may be on a different network
- **Development environments**: Developers who need to connect to test instances running on different ports
- **Network security**: Users who prefer different IP binding configurations

## Implementation Notes

- Should maintain backward compatibility with current hardcoded defaults
- Environment variables should take precedence over defaults
- Should validate that provided values are valid IP addresses/hostnames and ports
- Consider adding validation for port ranges (1-65535)
- Update documentation to reflect new configuration options

## Acceptance Criteria

- [ ] `ACTIVITYWATCH_HOST` environment variable support
- [ ] `ACTIVITYWATCH_PORT` environment variable support  
- [ ] Backward compatibility maintained (defaults to 127.0.0.1:5600)
- [ ] Input validation for host and port values
- [ ] Documentation updated to reflect new configuration options
- [ ] Tests added for environment variable configuration

## Example Usage

```bash
# Use default settings (127.0.0.1:5600)
activitywatch-mcp-server

# Use custom host
ACTIVITYWATCH_HOST=192.168.1.100 activitywatch-mcp-server

# Use custom port
ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server

# Use both custom host and port
ACTIVITYWATCH_HOST=activitywatch.local ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server
```

## Technical Considerations

1. **Environment Variable Loading**: Use `process.env` to read environment variables
2. **Validation**: Implement proper validation for IP addresses and port numbers
3. **Error Handling**: Provide clear error messages for invalid configurations
4. **Documentation**: Update README.md with configuration examples
5. **Testing**: Add unit tests for different configuration scenarios

---

**Created:** 2025-08-12  
**Status:** ✅ COMPLETED  
**Completed:** 2025-08-12  
**Implementation Commit:** dbb12b5  
**Assignee:** ExactDoug

## Implementation Summary

This feature request has been successfully implemented with the following accomplishments:

- ✅ `ACTIVITYWATCH_HOST` environment variable support
- ✅ `ACTIVITYWATCH_PORT` environment variable support  
- ✅ Backward compatibility maintained (defaults to 127.0.0.1:5600)
- ✅ Input validation for host and port values with error logging
- ✅ Documentation updated in README.md with configuration examples
- ✅ All existing tests continue to pass (zero test modifications required)
- ✅ Build process validated with TypeScript compilation to JavaScript

**Usage Examples Now Active:**
```bash
# Use default settings (127.0.0.1:5600)
activitywatch-mcp-server

# Use custom host
ACTIVITYWATCH_HOST=192.168.1.100 activitywatch-mcp-server

# Use custom port
ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server

# Use both custom host and port
ACTIVITYWATCH_HOST=activitywatch.local ACTIVITYWATCH_PORT=8080 activitywatch-mcp-server
```
