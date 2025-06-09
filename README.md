# SiraSight Setup Leiningen Action

A fast, reliable GitHub Action for setting up Leiningen in CI/CD pipelines with anti-hanging measures and optimized caching.

## Features

- ✅ **Anti-Hanging Protection**: Implements network timeouts and fail-safe mechanisms
- ⚡ **Optimized Caching**: Uses GitHub Actions cache best practices for maximum performance  
- 🛡️ **Error Resilience**: Robust error handling and recovery mechanisms
- 🔧 **Flexible Configuration**: Customizable versions, timeouts, and cache settings
- 📊 **Performance Monitoring**: Detailed logging and timing information

## Quick Start

```yaml
steps:
  - uses: actions/checkout@v4
  
  - name: Setup Leiningen
    uses: SiraSight/setup-lein@v1
    with:
      lein-version: '2.9.1'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `lein-version` | Version of Leiningen to install | No | `2.9.1` |
| `cache-enable` | Enable caching of Leiningen installation | No | `true` |
| `timeout-seconds` | Timeout for network operations | No | `30` |

## Outputs

| Output | Description |
|--------|-------------|
| `cache-hit` | Whether the cache was restored from key |
| `lein-path` | Path to the installed Leiningen binary |

## Advanced Usage

### Custom Version and Timeout

```yaml
- name: Setup Leiningen with custom settings
  uses: SiraSight/setup-lein@v1
  with:
    lein-version: '2.10.0'
    timeout-seconds: '60'
    cache-enable: 'true'
```

### Disable Caching

```yaml
- name: Setup Leiningen without caching
  uses: SiraSight/setup-lein@v1
  with:
    lein-version: '2.9.1'
    cache-enable: 'false'
```

### Complete Workflow Example

```yaml
name: Clojure CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up JDK 24
      uses: actions/setup-java@v4
      with:
        java-version: '24'
        distribution: 'adopt'
    
    - name: Setup Leiningen
      uses: SiraSight/setup-lein@v1
      with:
        lein-version: '2.9.1'
    
    - name: Cache Maven dependencies
      uses: actions/cache@v4
      with:
        path: ~/.m2/repository
        key: ${{ runner.os }}-maven-${{ hashFiles('**/project.clj') }}
        restore-keys: |
          ${{ runner.os }}-maven-
    
    - name: Install dependencies
      run: lein deps
    
    - name: Run tests
      run: lein test
```

## Cache Strategy

This action implements GitHub Actions cache best practices:

- **Leiningen Binary Cache**: Caches the Leiningen installation using version-specific keys
- **Smart Cache Keys**: Uses `${{ runner.os }}-lein-${{ inputs.lein-version }}` for optimal cache hits
- **Fallback Restore Keys**: Implements progressive fallback for cache misses
- **Cache Invalidation**: Automatically invalidates cache when version changes

## Anti-Hanging Measures

The action includes several mechanisms to prevent hanging:

1. **Network Timeouts**: Configurable timeout for download operations
2. **Retry Logic**: Automatic retry with exponential backoff
3. **Connection Validation**: Pre-flight connectivity checks
4. **Fail-Safe Fallback**: Alternative download mirrors and methods

## Performance Optimizations

- **Parallel Operations**: Concurrent cache operations where possible
- **Minimal Downloads**: Only downloads when cache miss occurs
- **Efficient Extraction**: Optimized archive extraction process
- **Path Optimization**: Smart PATH manipulation for faster command resolution

## Troubleshooting

### Common Issues

**Cache Miss Every Time**
```yaml
# Ensure consistent cache key
- name: Debug cache key
  run: echo "Cache key would be ${{ runner.os }}-lein-${{ inputs.lein-version || '2.9.1' }}"
```

**Network Timeouts**
```yaml
# Increase timeout for slow networks
- uses: SiraSight/setup-lein@v1
  with:
    timeout-seconds: '120'
```

**Permission Issues**
```yaml
# Ensure proper permissions
- name: Fix permissions
  run: chmod +x ~/.local/bin/lein
```

### Debug Mode

Enable debug logging by setting the `ACTIONS_STEP_DEBUG` secret to `true` in your repository.

## Version Compatibility

| Leiningen Version | Java Version | Status |
|-------------------|--------------|---------|
| 2.9.1 | 8, 11, 17, 21, 24 | ✅ Recommended |
| 2.9.10 | 8, 11, 17, 21, 24 | ✅ Supported |
| 2.10.0 | 11, 17, 21, 24 | ✅ Latest |

## Contributing

1. Fork the repository
2. Create a feature branch
3. Test with multiple OS/version combinations
4. Submit a pull request

## License

MIT License - see [LICENSE](LICENSE) for details.

## Changelog

### v1.0.0
- Initial release with anti-hanging measures
- Optimized caching implementation
- Comprehensive error handling
- Multi-platform support

---

**Built for [SiraSight](https://github.com/SiraSight) with ❤️** 