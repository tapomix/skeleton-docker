<!-- markdownlint-disable MD024 -->
# Changelog

All notable changes to this template will be documented in this file.

---

## [1.1.0] - 2026-01-18

### Added

- Documentation section "Traefik network label" explaining network override behavior

### Changed

- Declare `service-net` as external network (allows communication with services from other projects)
- Rename `UID` and `GID` to `USER_ID` and `GROUP_ID` to avoid conflicts with reserved shell variables

### Fixed

- Fix deprecated `security_opt` syntax (use `=` instead of `:` as separator)
- Remove final newline in files of secrets directory

## [1.0.3] - 2026-01-08

### Added

- Docker secrets support (commented template in compose.yaml)
- Secrets documentation section in README
- Resource limits support (cpus, mem_limit, pids_limit)
- Logging configuration support (json-file driver)

## [1.0.2] - 2025-12-23

### Added

- Set filesystem as readonly in the container
- Drop all Linux capabilities
- Handle socket proxy network instead of direct socket access

### Changed

- Use https instead of ssh url to clone repository
- Add "required" column in environment variables listing
- Service specific network commented by default

## [1.0.1] - 2025-12-16

### Fixed

- Timezone sample values corrected

## [1.0.0] - 2025-12-15

### Added

- Docker Compose configuration with Traefik integration
- Environment variables template (`.env.dist`)
- Documentation [README](README.md)

---

## About

This project follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).  
The changelog format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
