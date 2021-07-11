# Dependency Tracking
 - Create a ```go.mod``` file using ```go mod init <module path>```
    Module path can be repo path
 - It must  be a location from which ```Go tools``` can download the module
 - go mod tidy: module maintenance, update dependencies from import declarations in code. It locates and downloads the module that contains the package imported in code.

# Package declaration

    package <package name>

 - Made up of all files in the same directory and groups functions

# Package search
 - Search packages at https://pkg.go.dev
 
 
 # Details
 
 ## Module Cache
 
  - Directory where `go` command stores downloaded module files. Default location: ```$GOPATH/pkg/mod```
    To change location set ```GOMODCACHE``` env var
  - Distinct from build cache which contains compiled packages and other build artifacts
  - Module cache doesn't have a maximum size and go command doesn't remove its contents automatically.
  - Go command creates module source files & directories in cache with read-only permissions to prevent accidental changes to module after download. Hence to delete cache use ```go clean -modcache``` or make files read/write-able with risky -modcacherw flag with go cmd
   ```go mod verify``` for detecting modifications to dependencies of main module. It scans extracted contents of each module dependency and confirms match with hash in go.sum
   > Capital letters in module paths and versions are escaped using exclamation points to avoid conflicts when serving from case-insensitive file systems, e.g., Azure is escaped as ```!azure```. This allows modules azure & Azure to be sored on disk.
  > Module cache can be shared by multiple Go projects developed on the same machine. The go command will use the same cache regardless of location of main module. Multiple instances of go command may safely access same module cache simultaneously.

### Authenticating Modules
  - When go command downloads module into module cache, it computest hash & compares it with known value to verify file integrity. Each hash is compare  with corresponding line in main module's go.sum file. On error, the downloaded file is deleted without adding to module cache. If go.sum file is not present or if it doesn't contain hash for downloaded file, global [checksum database](https://sum.golang.org/) is used
  - For private modules or if checksum db is disabled (GOSUMDB=off) no verification is done before addition to cache.
  > Each module in the shared cache will have its own go.sum file with potentially different hashes. To avoid the need to trust other modules, the go command verifies hashes using main module's go.sum whenever it accesses module cache
  > ```go mod verify``` is used to check that zip files & extracted directories have not been modified since they were added to the module cache
  
## Env vars

Variable  | Purpose
-------------|------------
GOPRIVATE | comma separated list of modules to be considered private. Default value for GONOPROXY & GONOSUMDB
GONOSUMDB | comma separated list of module path prefixes for which checksums are not verified. Defaults to GOPRIVATE
GONOPROXY | comma separated list of module path prefixes that should always be fetched directly from vcs repos not from module proxies. Defaults to GOPRIVATE
GOSUMDB | Identifies checksum db to use & optionally its public key & url. Defaults to Google-run db. (If set to off, checksum db is not consulted. Use GOPRIVATE or GONOSUMDB instead)


